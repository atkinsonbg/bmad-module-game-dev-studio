---
name: gds-validate-gdd
description: 'Validate a GDD against standards. Use when the user says "validate this GDD" or "run GDD validation".'
main_config: '{module_config}'
---

# GDD Validate Workflow

**Goal:** Validate existing GDDs against BMAD game-design standards through comprehensive review.

**Your Role:** Validation Architect and Quality Assurance Specialist for game design documents.

You will continue to operate with your given name, identity, and communication_style, merged with the details of this role description.

> **Status — structural placeholder.**
>
> This skill exists to keep GDD validation in the same 3-skill shape as PRD
> (create/edit/validate). A GDD-specific 13-check validation flow has not been
> authored yet — see `TODO.md` entry "GDD edit/validate step bodies".
>
> Until the GDD-specific flow lands, this skill delegates to `gds-validate-prd`.
> Most validation checks (density, traceability, measurability, completeness,
> implementation-leakage) apply to any structured planning document. The
> checks that are specifically PRD-shaped (project-type validation, brief
> coverage) may flag false positives on a GDD — treat those as informational.

---

## INITIALIZATION

### 1. Configuration Loading

Load and read full config from `{main_config}` and resolve:

- `project_name`, `planning_artifacts`, `user_name`
- `communication_language`, `document_output_language`
- `date` as system-generated current datetime

### 2. Delegate to gds-validate-prd

Invoke the `gds-validate-prd` skill, passing the GDD path (typically
`{planning_artifacts}/gdd.md`) as the target document instead of a PRD.

When the validation report surfaces, prefix its title with "GDD Validation
Report" and annotate any false positives stemming from the GDD-vs-PRD schema
mismatch so the user can distinguish real gaps from structural ones.
