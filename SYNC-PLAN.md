# Game-Dev Studio Sync Plan — 2026-04-14

Syncing `bmad-module-game-dev-studio` against `bmad-code-org/BMAD-METHOD@main` (`6b964acd`, post-v6.3.0).

## Architectural guardrail — DO NOT touch

Upstream lives under `src/bmm-skills/{1-analysis,2-plan-workflows,3-solutioning,4-implementation}/` plus `src/core-skills/`. Game-dev lives under `src/workflows/{1-preproduction,2-design,3-technical,4-production,gametest,gds-quick-flow,gds-document-project}/` plus `src/agents/` and `src/gametest/`.

**This sync preserves game-dev's top-level organization.** We port *content and skill-directory shape* within the existing tree; we do not relocate into `src/bmm-skills/`.

---

## Decisions locked in 2026-04-14

**Agents (Phase 4 mirror):** Merge `gds-agent-game-qa` and `gds-agent-game-scrum-master` into `gds-agent-game-dev`. Upstream collapsed to a single Developer agent (commits `48c2324b` / `003c979d`) — Phase 4 of game-dev will follow. Phases 1–3 agents (designer, architect, solo-dev, tech-writer) remain distinct.

**quick-dev:** Delete `gds-quick-dev` and `gds-quick-spec` entirely. Port upstream `bmad-quick-dev` 1:1 as the replacement. No chain, no overlap-preservation.

**gametest relocation:** Move `src/gametest/` (the knowledge base + `qa-index.csv`) into `src/agents/gds-agent-game-dev/gametest/`. Reason: `_bmad-output/` is being deprecated as a runtime location; knowledge the dev agent uses should live with the dev agent. `src/workflows/gametest/` (the 7 test workflows) stays where it is.

---

## Phase 1 — quick-dev full replacement + quick-flow cleanup

### Scope

- **Delete** `src/workflows/gds-quick-flow/gds-quick-dev/` (all current content — divergent enough that no salvage is useful)
- **Delete** `src/workflows/gds-quick-flow/gds-quick-spec/` (redundant once upstream's bmad-quick-dev is ported; upstream absorbs spec generation into `step-02-plan`)
- **Delete** `src/workflows/gds-quick-flow/gds-quick-dev-new-preview/` (already-approved cleanup)
- **Port** upstream `bmad-quick-dev` 1:1 to a new `src/workflows/gds-quick-flow/gds-quick-dev/`

### Upstream source

`src/bmm-skills/4-implementation/bmad-quick-dev/` — 11 files, flat layout:
- `SKILL.md`, `workflow.md`, `spec-template.md`, `step-oneshot.md`
- `step-01-clarify-and-route.md`, `step-02-plan.md`, `step-03-implement.md`, `step-04-review.md`, `step-05-present.md`
- `compile-epic-context.md`, `sync-sprint-status.md`

### Adaptation work during port

Copy upstream files as-is, then rewrite references for game-dev context:
- `_bmad/bmm/` → `_bmad/gds/` config paths
- `bmad-agent-dev` → `gds-agent-game-dev`
- Any `{module_root}` resolutions to point at game-dev module structure
- Preserve upstream step names, structure, and framing ("hardened, reviewable artifact")

After port, verify `gds-quick-flow/` only contains the new `gds-quick-dev/`. The flow folder may eventually collapse to just the skill itself — flag in `TODO.md` if it does.

### Phase 1 verification

- `npm run lint:md` clean
- `gds-quick-dev` resolves via installer (manual `npx bmad-method install` smoke test)
- Grep confirms zero refs to `gds-quick-spec` or `gds-quick-dev-new-preview` remain in `src/`

---

## Phase 2 — PRD + GDD skill split

### Upstream shape

```
src/bmm-skills/2-plan-workflows/
├── bmad-create-prd/     (SKILL.md, workflow.md, steps-c/, data/, templates/)
├── bmad-edit-prd/       (SKILL.md, workflow.md, steps-e/, data/prd-purpose.md) ← NEW data file
└── bmad-validate-prd/   (SKILL.md, workflow.md, steps-v/)
```

`bmad-edit-prd` just gained new step files (`step-e-01-discovery.md`, `step-e-01b-legacy-conversion.md`, `step-e-02-review.md`, `step-e-03-edit.md`, `step-e-04-complete.md`) and a new `data/prd-purpose.md` reference. Upstream `bmad-create-prd/steps-c/step-08-scoping.md` and `step-11-polish.md` also changed.

### Game-dev current shape

**PRD is consolidated** at `src/workflows/2-design/create-prd/`:
```
create-prd/
├── bmad-skill-manifest.yaml   (no SKILL.md!)
├── data/, templates/
├── steps-c/, steps-e/, steps-v/
├── workflow-create-prd.md
├── workflow-edit-prd.md
└── workflow-validate-prd.md
```

**GDD is a single skill** at `src/workflows/2-design/gds-create-gdd/` with `steps/` (14 step files) — no edit or validate variant.

### Plan

**2a. Split PRD into 3 skill dirs:**

```
src/workflows/2-design/
├── gds-create-prd/     (from workflow-create-prd.md + steps-c/ + shared data/templates)
├── gds-edit-prd/       (from workflow-edit-prd.md + steps-e/; port upstream step-e-* updates + prd-purpose.md)
└── gds-validate-prd/   (from workflow-validate-prd.md + steps-v/)
```

Each gets `SKILL.md` + `bmad-skill-manifest.yaml`. Shared `data/` and `templates/` stay deduplicated where possible (symlink or copy-with-note).

**2b. Mirror the same split for GDD** (user-approved — "GDD is used as PRD by default in gds"):

```
src/workflows/2-design/
├── gds-create-gdd/     (existing, rename steps/ → steps-c/ for consistency)
├── gds-edit-gdd/       (NEW — model on gds-edit-prd; port step-e pattern, adapt to game-design content)
└── gds-validate-gdd/   (NEW — model on gds-validate-prd; adapt AC and quality checks to GDD structure)
```

This is the **biggest net-new content** in the sync. `gds-edit-gdd` and `gds-validate-gdd` do not exist today.

**2c. Port upstream content changes:**
- `bmad-create-prd/steps-c/step-08-scoping.md` (93 line change) and `step-11-polish.md` → into `gds-create-prd/steps-c/` equivalents
- `bmad-edit-prd/data/prd-purpose.md` (197 new lines) → into `gds-edit-prd/data/` (and optionally `gds-edit-gdd/data/gdd-purpose.md` as a parallel)
- All `step-e-*` file updates → `gds-edit-prd/steps-e/`

### Phase 2 verification

- All three PRD skills resolve via installer
- GDD edit + validate stubs compile (OK if step bodies are initially lean — flag in `TODO.md`)

---

## Phase 2.5 — Agent merge + gametest relocation

### Agent merge

Goal: mirror upstream's single-Developer-agent model for Phase 4 roles.

1. **Capabilities audit** — read:
   - `src/agents/gds-agent-game-qa/SKILL.md` (Quinn-equivalent; owns QA knowledge lookup)
   - `src/agents/gds-agent-game-scrum-master/SKILL.md` (Bob-equivalent; owns sprint/story ceremony)
   - Upstream `bmad-agent-dev/SKILL.md` post-merge (shows how QA was absorbed — party-mode and generate-e2e-tests references preserved)
2. **Merge into `src/agents/gds-agent-game-dev/SKILL.md`:**
   - Keep Link Freeman persona; append QA + SM capability sections under `## Capabilities`
   - Pull QA-specific critical actions (knowledge-fragment lookup via `gametest/qa-index.csv` — see relocation below)
   - Pull SM-specific critical actions (sprint-status sync, story-file ownership)
   - Update `description` frontmatter to note combined role without losing Link Freeman trigger phrase
3. **Delete** `src/agents/gds-agent-game-qa/` and `src/agents/gds-agent-game-scrum-master/` directories entirely
4. **Global reference sweep:**
   - grep `src/` for `gds-agent-game-qa` and `gds-agent-game-scrum-master` — redirect every hit to `gds-agent-game-dev`
   - Expected hit sites: workflows in `4-production/` (sprint planning/status/retro reference SM), `gametest/` workflows (reference QA), `module-help.csv`, `bmad-skill-manifest.yaml` files
5. **`module.yaml` + `module-help.csv`** — remove dropped agents; update agent registry

### gametest relocation

Move knowledge base from `src/gametest/` to `src/agents/gds-agent-game-dev/gametest/`.

1. Move:
   - `src/gametest/knowledge/*.md` (17 files) → `src/agents/gds-agent-game-dev/gametest/knowledge/`
   - `src/gametest/qa-index.csv` → `src/agents/gds-agent-game-dev/gametest/qa-index.csv`
2. Delete empty `src/gametest/` directory
3. **Path rewrites** — grep `src/` for:
   - `{module_root}/gametest/` → `{skill_root}/gametest/` (or `{agents_root}/gds-agent-game-dev/gametest/` depending on which variable resolves correctly at install time — verify with upstream's path-resolver convention)
   - `gametest/qa-index.csv` (bare) → new path
   - `gametest/knowledge/` (bare) → new path
   - Known hit sites to verify: `src/workflows/gametest/gds-test-design/test-design-template.md:205`, former `gds-agent-game-qa/SKILL.md:30,33` content now living in `gds-agent-game-dev/SKILL.md`
4. `_bmad-output` references (currently 1 hit in `gds-code-review/workflow.md:20`) — review with user in Phase 4 whether to remove the explicit exclusion comment since `_bmad-output` is being deprecated (leave for now; it's informational)

### Phase 2.5 verification

- No lingering refs to `gds-agent-game-qa`, `gds-agent-game-scrum-master` in `src/` (grep exits with no matches)
- No lingering refs to `src/gametest/` or `{module_root}/gametest/` at the old location
- `gds-agent-game-dev/SKILL.md` parses; frontmatter valid
- Installer smoke test loads combined agent + gametest knowledge correctly

---

## Phase 3 — Production-phase steps/ backfill

### Current state (game-dev, no `steps/` subdir)

- `src/workflows/4-production/gds-code-review/`
- `src/workflows/4-production/gds-correct-course/`
- `src/workflows/4-production/gds-create-story/`
- `src/workflows/4-production/gds-dev-story/`
- `src/workflows/4-production/gds-retrospective/`
- `src/workflows/4-production/gds-sprint-planning/`
- `src/workflows/4-production/gds-sprint-status/`

### Upstream source to port from

- `bmad-code-review/steps/{step-01-gather-context.md, step-02-review.md, step-04-present.md}` — recently updated
- `bmad-correct-course/workflow.md` + `checklist.md` — updated
- `bmad-retrospective/workflow.md` — updated (268-line change; substantial rewrite)
- `bmad-sprint-planning/workflow.md`, `bmad-sprint-status/workflow.md` — minor
- `bmad-create-story`, `bmad-dev-story` — no `steps/` upstream either (skip)

### Plan

For each game-dev skill above, create a `steps/` subdir matching upstream's step decomposition. Copy step file contents, rewrite references to:
- `_bmad/bmm/...` → `_bmad/gds/...`
- `bmad-agent-dev` → `gds-agent-game-dev`
- `gds-agent-game-qa` / `gds-agent-game-scrum-master` refs → `gds-agent-game-dev` (per Phase 2.5 — should already be clean by the time Phase 3 runs)
- Any terminology swap (story → game-story where appropriate)

Update each `workflow.md` to reference the new step files if it currently inlines everything.

### Phase 3 verification

- `npm test` in game-dev repo passes
- Each skill's `workflow.md` correctly references its new `steps/*.md` via relative paths

---

## Phase 4 — Manifest + module.yaml audit

### Scope

- `src/module.yaml` — compare schema against upstream's `src/bmm-skills/module.yaml` and `src/core-skills/module.yaml`; harmonize field set
- Every `bmad-skill-manifest.yaml` under `src/workflows/**/` and `src/agents/**/` — validate against upstream skill-manifest schema (whatever `tools/validate-skills.js` in upstream expects)
- `src/module-help.csv` — regenerate from skill frontmatter (post-agent-merge; drops QA + SM entries)
- Any dangling references to `bmad-agent-qa` / `bmad-agent-sm` / `bmad-init` in copied content → clean up

### Plan

1. Run upstream's `tools/validate-skills.js` against game-dev `src/` (adapt path config)
2. Regenerate `module-help.csv` from SKILL.md frontmatter
3. Diff `module.yaml` schema against upstream; add missing fields with game-dev values
4. grep game-dev for `bmad-agent-qa`, `bmad-agent-sm`, `bmad-init` (removed in #2159) — clean up refs

### Phase 4 verification

- `npm run lint` + `npm run lint:md` + `npm run format:check` clean
- Validate-skills script exits 0

---

## Phase 5 — Cleanup

- `src/workflows/gds-quick-flow/gds-quick-dev-new-preview/`, `gds-quick-spec/`, and original `gds-quick-dev/` should already be gone from Phase 1
- `src/agents/gds-agent-game-qa/` and `src/agents/gds-agent-game-scrum-master/` should already be gone from Phase 2.5
- `src/gametest/` should already be gone from Phase 2.5
- Delete stale `install-success-message.md`, `codex-review.md` at repo root if superseded (check git log first)
- Update `TODO.md` with any known-deferred items (incomplete GDD edit/validate step bodies, etc.)
- Update `CHANGELOG.md` with sync summary

---

## Out of scope (deferred)

- Restructuring `src/workflows/` → `src/bmm-skills/` (game-dev's top-level org is intentional)
- Adding `gds-help` and `gds-party-mode` (user skipped)
- Editorial/adversarial review skills (`bmad-editorial-review-*`, `bmad-review-adversarial-general`, `bmad-review-edge-case-hunter`) — not requested; flag in `TODO.md` for future
- Full `src/workflows/gametest/` relocation into the dev agent (out of scope for this sync; only the knowledge-base `src/gametest/` moves)
- Whether `gds-quick-flow/` parent directory survives long-term (flag for follow-up)

---

## Execution order + checkpoints

All pre-flight decisions are locked in. Recommend phase-by-phase execution with a commit between each:

1. Phase 1 (quick-dev full replacement + delete quick-spec + delete new-preview) — commit
2. Phase 2a (PRD split into 3 skill dirs) — commit
3. Phase 2b (GDD split into 3 skill dirs) — commit
4. Phase 2c (port upstream PRD content updates — step-08-scoping, step-11-polish, step-e-*, prd-purpose.md) — commit
5. Phase 2.5 (agent merge QA+SM→dev; relocate `src/gametest/` into `gds-agent-game-dev/gametest/`) — commit
6. Phase 3 (production steps/ backfill; agent refs already clean from 2.5) — commit
7. Phase 4 (manifest audit + module-help.csv regen + final cleanup) — commit
8. Full `npm test` run + manual `npx bmad-method install` smoke test into a scratch dir → push

Each phase is independently revertible via `git revert`.
