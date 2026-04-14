---
name: gds-edit-gdd
description: 'Edit an existing GDD. Use when the user says "edit this GDD" or "improve this GDD".'
main_config: '{module_config}'
---

# GDD Edit Workflow

**Goal:** Edit and improve existing Game Design Documents through structured enhancement.

**Your Role:** GDD improvement specialist.

You will continue to operate with your given name, identity, and communication_style, merged with the details of this role description.

> **Status — structural placeholder.**
>
> This skill exists to keep GDD editing in the same 3-skill shape as PRD
> (create/edit/validate). A GDD-specific step pattern has not been authored
> yet — see `TODO.md` entry "GDD edit/validate step bodies".
>
> Until the GDD-specific flow lands, this skill delegates to `gds-edit-prd`.
> The PRD edit flow (discovery → review → edit → complete, in `steps-e/`)
> generalizes cleanly to any structured planning document, and the user
> collaborates section-by-section rather than running a fully-automated pass.

---

## INITIALIZATION

### 1. Configuration Loading

Load and read full config from `{main_config}` and resolve:

- `project_name`, `planning_artifacts`, `user_name`
- `communication_language`, `document_output_language`, `game_dev_experience`
- `date` as system-generated current datetime

### 2. Delegate to gds-edit-prd

Invoke the `gds-edit-prd` skill, passing the GDD path (typically
`{planning_artifacts}/gdd.md`) as the target document instead of a PRD.

When advancing through the PRD edit flow, substitute these GDD equivalents
wherever the PRD flow references PRD-specific fields:

| PRD concept                 | GDD equivalent                       |
| --------------------------- | ------------------------------------ |
| Product vision              | Game vision / core fantasy           |
| Personas / user journeys    | Player persona / core gameplay loop  |
| Functional requirements     | Core mechanics / systems             |
| Non-functional requirements | Technical constraints / target specs |
| Success metrics             | Design goals / retention hooks       |
| Scoping                     | Content scope / progression arc      |

Return control to this skill only if the PRD edit flow surfaces a concern
the GDD schema cannot accommodate — in that case, HALT and ask the user how
to proceed.
