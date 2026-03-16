# CHANGELOG

## v0.2.2 - Mar 16, 2026

### Agent Skill Conversion

All 7 agent YAML files converted to native skill format (`gds-agent-*` directories with SKILL.md and bmad-skill-manifest.yaml). Agents are now invocable as skills rather than parsed from YAML definitions.

* Cloud Dragonborn (Game Architect), Samus Shepard (Game Designer), Link Freeman (Game Developer), Indie (Game Solo Dev), Max (Scrum Master), GLaDOS (Game QA), Paige (Technical Writer)
* Tech Writer agent now includes prompt files for each capability (write-document, validate-doc, mermaid-gen, explain-concept, update-standards)

### Complete gds- Prefix Rename

All remaining workflow directories renamed to use `gds-` prefix for multi-module coexistence. This completes the rename started in v0.2.1.

* 22 workflow directories renamed (production, technical, gametest, design, preproduction, document-project, quick-spec)
* All step file `workflow_path` references updated to match new directory names
* SKILL.md passthrough files added to all renamed directories

### Other Changes

* module-help.csv updated to use `skill:` references for all workflows
* Removed `src/teams/` folder (default-party.csv, team-gamedev.yaml)
* Removed agent YAML schema test infrastructure (fixtures, schema, test scripts)
* Simplified all workflow bmad-skill-manifest.yaml to `type: skill`

## v0.2.1 - Mar 13, 2026

* Fix: Rename all skill directories to use `gds-` prefix for multi-module coexistence
* Fix: Remove accidentally committed `.idea/` directory

## v0.2.0 - Mar 13, 2026

### Skill Format Migration

All workflows converted to the unified skill format. This aligns with BMAD-METHOD Beta 7 conventions and enables consistent skill-based invocation across all agentic tools.

**Phase 3 (3-technical) - 2 new workflows:**

* NEW: check-implementation-readiness - ported from BMAD-METHOD, adapted for GDD-based validation
* NEW: create-epics-and-stories - ported from BMAD-METHOD, adapted for game design requirements

**Phase 2 (2-design) - 2 new workflows:**

* NEW: create-ux-design - ported from BMAD-METHOD, adapted for game UI/HUD design with player-centric framing
* NEW: create-prd - optional workflow for generating PRDs from GDD, primarily for external tool compatibility (bmad-assist)

**Phase 1 (1-preproduction) - 1 new workflow suite:**

* NEW: research suite (market, domain, technical) - ported from BMAD-METHOD with game industry context (player demographics, ESRB/PEGI ratings, game engine research, middleware evaluation)

**Quick Flow - 1 new workflow:**

* NEW: quick-dev-new-preview - unified quick flow (experimental), ported from BMAD-METHOD

### Agent Updates

* All 7 agent files updated to use new skill format
* game-architect: added check-implementation-readiness menu item
* game-solo-dev: added quick-dev-new-preview menu item

### Help System

* bmad-help fully updated with correct workflow paths, new workflows, and skill references

## v0.1.10 - Feb 28, 2026

* Knowledge base added for the 4 most popular game development engines (Unity, Unreal, Godot and Phaser) to inform the game architecture design workflow.

## v0.1.9 - Feb 23, 2026

* Fix workflow YAML quoting to prevent installation breakage

## v0.1.8 - Feb 22, 2026

* Standardize all 21 workflow descriptions to follow consistent format for improved AI skill invocation accuracy
  - Add short human-readable prefixes to descriptions
  - Use explicit trigger phrases with intent markers
  - Limit to max 2 phrases per workflow to reduce false positives
  - Fix substring matching issues with questions

## v0.1.7 - Feb 10, 2026

* Removed incorrect obsolete references to "validate-story" in the scrum-master agent and the help system.
* Minor changes from recent BMM versions backported to production workflows.
* Help files are in place. They are still somewhat placeholdery and will be improved soon.

## v0.1.5 - Feb 1, 2026

* Improve module-help.csv descriptions with "Use when..." clauses for better LLM comprehension
* Update all 26 workflow descriptions with '[Action]. Use [when].' pattern
* Move Correct Course workflow from anytime to 4-production phase (sequence 55)
* Remove sequence numbers from all anytime items
* Change "AI agent" to "chosen agentic tool" for vendor neutrality
* Properly order anytime items at top before phased workflows

## v0.1.4 - Jan 27, 2026

* The architecture creation process has been revamped and now has significantly more content relevant to game development. When creating your architecture document, you'll now make decisions on such things as rendering pipelines, physics systems, anti-cheat libraries, dialogue systems, and more. You'll be prompted for common starter templates and useful MCPs that interact with game engines.
* Fixed bug in multiple workflows that were using BMM's user_skill_level instead of GDS' game_dev_experience.

## v0.1.3 - Jan 26, 2026

* Help system update

## v0.1.2 - Jan 22, 2026

* Workflow init replaced with new help system
* Changing all references of BMGD to GDS

## v0.1.1 - Jan 21, 2026

NOTE: This is in preparation for BMAD's Beta 6 launch. It is a *significant* update and recommended for all BMGD users as it integrates all the new BMAD Beta 6 work done in the past month. Documentation is still incomplete and will come shortly. Thank you for your patience.

* Update of all shared BMM code to current version
  * Brownfield workflow init
  * Greenfield workflow init
  * Project context file is now created as part of both brownfield and greenfield workflow inits
  * quick-flow massively updated (quick-dev, quick-spec updated, quick-prototype removed) to match recent BMM Beta 6 changes
  * All workflows in 4-production changed to match recent BMM Beta 6 changes
  * Retrospective now uses actual BMGD agents, not BMM agents. Now GLaDOS will pat you on the head and Samus will randomly run around the table to work off energy. (Not really, but you get the idea.)
* Adding technical writer agent
* Adding document project workflow (used by technical writer agent and when beginning a brownfield (pre-existing codebase) project). Unmodified for now so will have many sections not typically used by games (such as API documentation)