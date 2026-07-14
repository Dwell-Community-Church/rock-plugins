# rock-documentation

Answers Rock RMS questions by routing to Rock's official documentation and fetching the current
content, so answers stay current instead of guessed from memory.

## What's inside

- `skills/rock-documentation/SKILL.md` — the answer workflow: identify the topic → find the right URL
  in the map → fetch it → answer with a doc link.
- `skills/rock-documentation/references/rock-docs-map.md` — a curated index of Rock's official docs
  (section → URL → audience) plus routing rules (current vs legacy, leaf-vs-index, the `/Lava` and
  `/developer` roots).

## How it works

Rock's documentation is public and changes often, so this skill doesn't hard-code answers, it maps a
question to the correct official doc page and fetches it live, then answers with the link. Covers
everyday staff how-tos (check-in, people/families, groups, attendance, reports, giving, communications)
through admin and developer topics (workflows, Lava, data model, REST API).

## Scope

Official Rock behavior only. Your church's own customizations (custom attributes, local procedures) are
out of scope: the skill answers with general Rock behavior and flags that your own setup may differ.

## Requirements

Needs the runtime to be able to fetch web pages (`community.rockrms.com`). If fetching isn't available,
the skill falls back to handing the user the direct doc URL plus a general answer.
