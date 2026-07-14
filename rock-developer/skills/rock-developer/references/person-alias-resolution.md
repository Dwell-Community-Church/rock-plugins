# Person identity (PersonAlias) resolution

Adapted from Greg Lawless's Rock documentation. This is a universal Rock rule, but the
official docs don't state it in one place, so it lives here. Apply it proactively whenever a question
touches person data in SQL, C#, or Lava.

Rock entities reference people via `PersonAliasId`, not `PersonId`. One person can have many
PersonAliases (from merges, historical records, etc.). Treating distinct PersonAliasIds as distinct
people is a bug.

**Always resolve to `PersonAlias.PersonId` before:**
- Grouping or counting people
- Deduplicating donor / attendee / member lists
- Comparing identity across rows or entities
- Joining person data from multiple sources
- **Filtering "records for this person"**: any `WHERE *PersonAliasId = @id` /
  `.Where(x => x.SomePersonAliasId == aliasId)` / Lava `where:'...PersonAliasId == "{{ id }}"'` that
  means "give me this person's records" is a bug. Resolve to PersonId first, then filter by
  `IN (every alias id for that person)` (SQL `IN` / EF `Contains` / Lava OR-chain or `{% sql %}`). A
  single-alias equality filter silently drops records stored under any other alias of the same person,
  common after a merge.

## Auditing existing code

When auditing a codebase for this rule, do not stop at `GROUP BY` / `DISTINCT` / `COUNT` / `JOIN`
patterns. The most common bug is plain equality filtering on a `*PersonAliasId` column. Flag every one
of these as suspect until you confirm intent:

- SQL: `WHERE *PersonAliasId = ...`, `ON ... = *PersonAliasId`
- C#/EF: `.Where(x => x.SomePersonAliasId == someAliasId)`, `.FirstOrDefault(...)`, `.Any(...)`, `.Single(...)`
- Lava: `{% entity where:'...PersonAliasId == "{{ id }}"' %}`

It is only safe when the intent is genuinely "this exact alias row" (e.g. auditing which alias was
active at the time, or joining back to the alias record itself). For "this person's records," it is
wrong, even when the surrounding code looks like a simple lookup rather than a group or count.

## SQL

```sql
-- Correct: resolve through PersonAlias, group by PersonId
SELECT  p.Id AS PersonId, p.NickName, p.LastName, COUNT(*) AS Count
FROM    FinancialTransaction ft
JOIN    PersonAlias pa ON pa.Id = ft.AuthorizedPersonAliasId
JOIN    Person      p  ON p.Id  = pa.PersonId
GROUP BY p.Id, p.NickName, p.LastName;

-- Wrong: groups by alias, splits merged people into duplicates
-- GROUP BY ft.AuthorizedPersonAliasId
```

## C#

```csharp
// Resolve alias -> person
var person   = new PersonAliasService( rockContext ).GetPerson( aliasId );

// ID-only check (avoids loading the Person)
var personId = new PersonAliasService( rockContext ).GetPersonId( aliasId );

// Or via navigation when the entity is already loaded
var person   = transaction.AuthorizedPersonAlias.Person;
```

## Lava

Use the `PersonByAliasId` filter or traverse `PersonAlias.Person`. Never compare `PersonAliasId` values
for identity equality.

## Entities to watch

Most person-referencing entities use `*PersonAliasId` columns: FinancialTransaction,
FinancialScheduledTransaction, FinancialPledge, Attendance, Communication, CommunicationRecipient,
Note, History, ConnectionRequest, RegistrationRegistrant, Interaction.

Exceptions where the column is already a PersonId (no alias resolution needed): Person, GroupMember,
UserLogin, PhoneNumber, PersonSearchKey, and PersonAlias.PersonId itself.
