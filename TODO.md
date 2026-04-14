# TODO - what's on BMGD's schedule

* Adapt document-project workflow to BMGD
* Add and adapt research agents to pre-production (either to designer agent or new analyst agent)
* Author GDD-specific step bodies for `gds-edit-gdd/steps-e/*` and `gds-validate-gdd/steps-v/*`. Both skills currently delegate to their PRD counterparts as a structural placeholder. Model after `gds-edit-prd` and `gds-validate-prd`, but replace PRD-specific validation checks (project-type, brief coverage) with GDD equivalents (game-type, gameplay-loop coverage, mechanics completeness).
* Review whether `src/workflows/gds-quick-flow/` parent directory still earns its keep now that it contains only `gds-quick-dev/`. Candidate: promote `gds-quick-dev` up to `src/workflows/4-production/` or similar and retire the `gds-quick-flow/` wrapper.