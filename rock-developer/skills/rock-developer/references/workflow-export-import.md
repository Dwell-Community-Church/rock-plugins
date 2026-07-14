> Adapted from Greg Lawless's Rock documentation. Reverse-engineered from Rock core's ShareWorkflow
> block and the Rock.Utility.EntityCoding library, so it is not covered by the official Rock docs.

# Rock RMS Workflow Export / Import

## Source of Truth

The canonical implementation lives in Rock core at:

- `RockWeb/Blocks/WorkFlow/ShareWorkflow.ascx.cs`: the block that drives export and import
- `Rock/Utility/EntityCoding/`: the underlying library (`EntityCoder`, `EntityDecoder`, `ExportedEntitiesContainer`, `WorkflowTypeExporter`, `CodingHelper`, and processor classes)

Namespace: `Rock.Utility.EntityCoding`

---

## How Export Works (C# side)

```csharp
using Rock.Utility.EntityCoding;

var rockContext = new RockContext();
var workflowType = new WorkflowTypeService( rockContext ).Get( workflowTypeId );

var coder = new EntityCoder( new RockContext() );
coder.EnqueueEntity( workflowType, new WorkflowTypeExporter() );
var container = coder.GetExportedEntities();

// Serialize to JSON
string json = Newtonsoft.Json.JsonConvert.SerializeObject( container, Newtonsoft.Json.Formatting.Indented );
```

**File naming convention** used by Rock itself:

```csharp
string.Format( "{0}_{1}.json",
    workflowType.Name.MakeValidFileName(),       // e.g. "My Workflow"
    RockDateTime.Now.ToString( "yyyyMMddHHmm" )  // e.g. "202510040041"
)
// Result: "My Workflow_202510040041.json"
```

---

## How Import Works (C# side)

```csharp
var container = Newtonsoft.Json.JsonConvert.DeserializeObject<ExportedEntitiesContainer>( json );

var decoder = new EntityDecoder( new RockContext() );

// Required: tell the importer which Category to place the workflow in
decoder.UserValues.Add( "WorkflowCategory", categoryService.Get( categoryId ) );

List<string> messages;
bool success = decoder.Import( container, dryRun: false, out messages );
```

The only required `UserValues` key for a workflow import is `"WorkflowCategory"`. Any `Reference` with `Type = 4` (UserDefined) and `Data = "WorkflowCategory"` will be resolved using this value at import time.

---

## Exported JSON Schema

The exported file is a serialized `ExportedEntitiesContainer`.

### Top-level structure

```json
{
  "Entities": [ ...EncodedEntity... ],
  "RootEntities": [ "guid-of-root-workflow-type" ]
}
```

`RootEntities` is a list of Guids identifying which entities in `Entities` are the roots of the export (typically a single WorkflowType Guid).

### EncodedEntity

```json
{
  "EntityType": "Rock.Model.WorkflowType",
  "Guid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "GenerateNewGuid": true,
  "Properties": {
    "Name": "My Workflow",
    "Description": "",
    "IsActive": true,
    "IsLoginRequired": false,
    "NoActionMessage": "...",
    "SummaryViewText": "...",
    "IconCssClass": "fa fa-list-ol",
    "WorkTerm": "Workflow",
    "ProcessingIntervalSeconds": 0,
    "IsPersisted": true,
    "LoggingLevel": 0,
    "MaxWorkflowAgeDays": null,
    "IsFormBuilder": false
  },
  "Transforms": {
    "Rock.Utility.EntityCoding.WorkflowActionFormProcessor": { ...opaque object... }
  },
  "References": [ ...Reference... ]
}
```

`GenerateNewGuid` controls whether the importer assigns a new Guid to this entity on import:
- `true` on most workflow entities (WorkflowType, ActivityType, ActionType, WorkflowActionForm, AttributeValues)
- `false` on Attributes (matched by Key + qualifier instead of Guid, so they don't need a new one)

`Transforms` carries opaque processor-specific data. The only processor you will encounter in practice is `Rock.Utility.EntityCoding.WorkflowActionFormProcessor`, which serializes the delimited `Actions` string on `WorkflowActionForm` so embedded Guids can be remapped on import. You do not need to manually construct this; the exporter builds it automatically.

### Reference

```json
{
  "Property": "CategoryId",
  "EntityType": "Rock.Model.Category",
  "Type": 4,
  "Data": "WorkflowCategory"
}
```

`Type` is the `ReferenceType` enum:

| Value | Name | Resolution |
|-------|------|-----------|
| 0 | Null | No-op, property is left unset |
| 1 | Guid | Look up entity by Guid in `Data`; write resolved Id to `Property` |
| 2 | EntityType | Look up EntityType by full class name in `Data`; write Id to `Property` |
| 3 | FieldType | Look up FieldType by full class name in `Data`; write Id to `Property` |
| 4 | UserDefined | Look up from `decoder.UserValues` using key in `Data`; write Id to `Property` |

Rules:
- Foreign key properties (e.g. `CategoryId`, `EntityTypeQualifierColumn`) are never stored in `Properties`. They are stripped out and replaced by a `Reference` entry.
- `EntityType` and `FieldType` records are never exported as `Entities`. They are always referenced-only using Type 2 and Type 3 respectively, and must already exist on the target system.
- `WorkflowCategory` is the only UserDefined reference a workflow export produces. It appears on the root `WorkflowType` entity's `CategoryId` property.

---

## Entity Tree for a Workflow Export

The `WorkflowTypeExporter` traverses this object graph. Entities appear in `Entities` in dependency order (parents before children) so the importer can process them sequentially:

```
WorkflowType
  ├── Attribute (workflow-level)
  │     └── AttributeQualifier
  ├── WorkflowActivityType
        ├── Attribute (activity-level)
        │     └── AttributeQualifier
        └── WorkflowActionType
              └── WorkflowActionForm (optional, for user-entry actions)
                    └── WorkflowActionFormAttribute
```

Attributes are discovered via `EntityTypeQualifierColumn` / `EntityTypeQualifierValue` rather than a direct FK, which is why a custom exporter is needed rather than generic entity traversal.

---

## Generating an Importable Workflow JSON

To generate a valid importable file without running Rock, construct the JSON directly following the schema above. The critical rules:

1. Every entity must have a stable Guid. Generate random v4 Guids for all entities.
2. Set `GenerateNewGuid: true` on everything except `Attribute` entities.
3. All `*Id` foreign-key properties must be absent from `Properties` and present in `References` instead.
4. The root `WorkflowType` needs one Reference with `Type: 4, Data: "WorkflowCategory", Property: "CategoryId"`.
5. Child entities reference their parents via `Type: 1` (Guid) references. For example, `WorkflowActivityType.WorkflowTypeId` becomes a Reference pointing at the WorkflowType's Guid.
6. `EntityTypeId` on Attributes becomes a `Type: 2` Reference with the full class name (e.g., `Rock.Model.WorkflowType`).
7. `FieldTypeId` on Attributes becomes a `Type: 3` Reference with the FieldType class (e.g., `Rock.Field.Types.TextFieldType`).
8. Do not include `Id`, `CreatedByPersonAliasId`, `ModifiedByPersonAliasId`, `CreatedDateTime`, or `ModifiedDateTime` in `Properties`.
9. `RootEntities` must contain the Guid of the `WorkflowType` entity.

### Minimal example: WorkflowType with one ActivityType and one AttributeType

```json
{
  "Entities": [
    {
      "EntityType": "Rock.Model.WorkflowType",
      "Guid": "11111111-1111-1111-1111-111111111111",
      "GenerateNewGuid": true,
      "Properties": {
        "Name": "Example Workflow",
        "Description": "A minimal importable workflow.",
        "IsActive": true,
        "IsLoginRequired": false,
        "IsPersisted": true,
        "WorkTerm": "Workflow",
        "ProcessingIntervalSeconds": 0,
        "LoggingLevel": 0,
        "IconCssClass": "fa fa-list-ol",
        "NoActionMessage": "This workflow does not have an active entry form.",
        "SummaryViewText": "",
        "Order": 0,
        "IsFormBuilder": false
      },
      "Transforms": {},
      "References": [
        {
          "Property": "CategoryId",
          "EntityType": "Rock.Model.Category",
          "Type": 4,
          "Data": "WorkflowCategory"
        }
      ]
    },
    {
      "EntityType": "Rock.Model.WorkflowActivityType",
      "Guid": "22222222-2222-2222-2222-222222222222",
      "GenerateNewGuid": true,
      "Properties": {
        "Name": "Start",
        "Description": "",
        "IsActivatedWithWorkflow": true,
        "IsUserSelectable": false,
        "Order": 0
      },
      "Transforms": {},
      "References": [
        {
          "Property": "WorkflowTypeId",
          "EntityType": "Rock.Model.WorkflowType",
          "Type": 1,
          "Data": "11111111-1111-1111-1111-111111111111"
        }
      ]
    },
    {
      "EntityType": "Rock.Model.WorkflowActionType",
      "Guid": "33333333-3333-3333-3333-333333333333",
      "GenerateNewGuid": true,
      "Properties": {
        "Name": "Set Name",
        "Order": 0,
        "IsActionCompletedOnSuccess": true,
        "IsActivityCompletedOnSuccess": false
      },
      "Transforms": {},
      "References": [
        {
          "Property": "ActivityTypeId",
          "EntityType": "Rock.Model.WorkflowActivityType",
          "Type": 1,
          "Data": "22222222-2222-2222-2222-222222222222"
        },
        {
          "Property": "EntityTypeId",
          "EntityType": "Rock.Model.EntityType",
          "Type": 2,
          "Data": "Rock.Workflow.Action.SetWorkflowName"
        }
      ]
    }
  ],
  "RootEntities": [
    "11111111-1111-1111-1111-111111111111"
  ]
}
```

---

## Common Attribute Field Type Class Names

For `FieldTypeId` References (`Type: 3`):

| Field Type | Class Name |
|------------|-----------|
| Text | `Rock.Field.Types.TextFieldType` |
| Boolean | `Rock.Field.Types.BooleanFieldType` |
| Integer | `Rock.Field.Types.IntegerFieldType` |
| Decimal | `Rock.Field.Types.DecimalFieldType` |
| Date | `Rock.Field.Types.DateFieldType` |
| DateTime | `Rock.Field.Types.DateTimeFieldType` |
| Person | `Rock.Field.Types.PersonFieldType` |
| Group | `Rock.Field.Types.GroupFieldType` |
| Defined Value | `Rock.Field.Types.DefinedValueFieldType` |
| Workflow Attribute | `Rock.Field.Types.WorkflowAttributeFieldType` |
| Email | `Rock.Field.Types.EmailFieldType` |
| Memo | `Rock.Field.Types.MemoFieldType` |
| Single Select | `Rock.Field.Types.SelectSingleFieldType` |
| Multi Select | `Rock.Field.Types.SelectMultiFieldType` |
| URL Link | `Rock.Field.Types.UrlLinkFieldType` |
| Campus | `Rock.Field.Types.CampusFieldType` |
| HTML | `Rock.Field.Types.HtmlFieldType` |

---

## Common Workflow Action EntityType Class Names

For `EntityTypeId` References (`Type: 2`) on `WorkflowActionType`:

| Action | Class Name |
|--------|-----------|
| Set Workflow Name | `Rock.Workflow.Action.SetWorkflowName` |
| Set Attribute Value | `Rock.Workflow.Action.SetAttributeValue` |
| Set Attribute from Entity | `Rock.Workflow.Action.SetAttributeFromEntity` |
| Activate Activity | `Rock.Workflow.Action.ActivateActivity` |
| Complete Workflow | `Rock.Workflow.Action.CompleteWorkflow` |
| Send Email | `Rock.Workflow.Action.SendEmail` |
| Delay | `Rock.Workflow.Action.Delay` |
| Run Lava | `Rock.Workflow.Action.RunLava` |
| Run SQL | `Rock.Workflow.Action.RunSQL` |
| User Entry Form | `Rock.Workflow.Action.UserEntryForm` |
| Person Get From Fields | `Rock.Workflow.Action.PersonGetFromFields` |
| Group Member Add | `Rock.Workflow.Action.AddGroupMember` |
| Workflow Attribute Set from Entity | `Rock.Workflow.Action.GetPersonFromFields` |

For the authoritative list, query `EntityType` where `Name` starts with `Rock.Workflow.Action`.

---

## Attribute Structure for Workflow-Level Attributes

A workflow-level `Attribute` entity uses `EntityTypeQualifierColumn = "WorkflowTypeId"` and `EntityTypeQualifierValue = ""` (the qualifier value is left blank in the export; the importer fills it in using the actual `WorkflowType.Id` after saving).

```json
{
  "EntityType": "Rock.Model.Attribute",
  "Guid": "44444444-4444-4444-4444-444444444444",
  "GenerateNewGuid": false,
  "Properties": {
    "IsSystem": false,
    "Key": "Person",
    "Name": "Person",
    "Description": "",
    "Order": 0,
    "IsGridColumn": false,
    "DefaultValue": "",
    "IsMultiValue": false,
    "IsRequired": false,
    "EntityTypeQualifierColumn": "WorkflowTypeId",
    "EntityTypeQualifierValue": ""
  },
  "Transforms": {},
  "References": [
    {
      "Property": "EntityTypeId",
      "EntityType": "Rock.Model.EntityType",
      "Type": 2,
      "Data": "Rock.Model.Workflow"
    },
    {
      "Property": "FieldTypeId",
      "EntityType": "Rock.Model.FieldType",
      "Type": 3,
      "Data": "Rock.Field.Types.PersonFieldType"
    }
  ]
}
```

Note: `GenerateNewGuid: false` because Attributes are matched by Key + qualifier, not Guid. If an Attribute with the same Key already exists for that workflow, the importer will skip creating it.

---

## Validating Generated JSON

Before using a generated workflow JSON file in production, import it on the Rock demo site at `https://rock.rocksolidchurchdemo.com` using the Share Workflow block at Admin Tools > Power Tools > Workflow Import/Export. A dry-run import (`cbDryRun = true`) will report errors without making changes.
