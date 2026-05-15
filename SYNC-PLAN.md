# Game-Dev Studio Sync Plan — 2026-05-15

Syncing `bmad-module-game-dev-studio` (**v0.4.0**) against `bmad-code-org/BMAD-METHOD@main` (`5090cfb0`, post-v6.6.0).

> Supersedes the 2026-04-14 plan (the v6.3.0 sync — completed, recorded in `CHANGELOG.md` v0.3.0).

## Context — this is NOT another architecture migration

BMGD **v0.4.0** already adopted the BMAD-METHOD `customize.toml` architecture (PRs #22 / #23 / #25):

- All 5 agents + 31 workflow skills are on the integrated `SKILL.md` + `customize.toml` pattern.
- `workflow.md` and `bmad-skill-manifest.yaml` are gone.
- `module.yaml` carries the `agents:` roster; `module-help.csv` is on the 13-column schema.
- Skills read config from `{project-root}/_bmad/gds/config.yaml`.

BMAD-METHOD was updated 6.3.0 → 6.6.0 (`5090cfb0`) in this session. BMGD v0.4.0 froze its *content* at v6.3.0 and adopted the architecture independently (it tracks the v6.4.0 `customize.toml` design). **This sync covers only the content delta v6.3.0 → v6.6.0+** — three content ports, three new/consolidated skills, and a catalog rename.

## Architectural guardrail — preserved

BMGD keeps its top-level organization: `src/workflows/{1-preproduction,2-design,3-technical,4-production,gametest,gds-quick-flow}/` + `src/agents/`. We port content and skill-directory shape within that tree — no relocation into `src/bmm-skills/`.

## Decisions locked 2026-05-15

1. **PRD/GDD consolidation** (user-approved). Collapse `gds-create-prd` + `gds-edit-prd` + `gds-validate-prd` → single `gds-prd`, and `gds-create-gdd` + `gds-edit-gdd` + `gds-validate-gdd` → single `gds-gdd`, mirroring upstream `bmad-prd`'s consolidated intent-detection pattern (create / update / validate modes in one skill).
2. **No deprecation shims** _(approved 2026-05-15)_. Upstream keeps `bmad-create/edit/validate-prd` as forwarding stubs for public backward-compat (v7.0 removal). BMGD is private and pre-1.0 — the six old trio directories are removed cleanly.
3. **gds-investigate**: port upstream `bmad-investigate` with light game-adaptation (path / agent / config rewrites only). Place under `4-production/`. Wire into `gds-agent-game-dev` menu as code `IN`.
4. **gds-customize**: NOT ported. `bmad-customize` is a `core-skills` skill; BMGD installs alongside `core`, which supplies it (and the `resolve_*.py` scripts). _[Verify in Phase 6: confirm BMGD cannot install fully standalone without `core`.]_
5. **project_name**: stays module-level in BMGD `module.yaml` ("What is the name of your game project?"). Upstream moved BMM's `project_name` to `[core]`; BMGD's is an intentional game-specific prompt — keep as-is.

## Real content delta — v6.3.0 → v6.6.0+

Pure-architecture commits (`ffdd9bc6`, `87292cd8`, `4405b817`, `e7a213ed`) are excluded — BMGD v0.4.0 already has that work. The genuine content changes:

| Upstream commit | Change | BMGD target | Phase |
|---|---|---|---|
| `1ad1f91e` (#1826) | Brownfield epic scoping — file-overlap detection + Implementation Efficiency principle + design-completeness gate | `gds-create-epics-and-stories` | 4 |
| `c29b72ec` (#2274) | create-story reads UPDATE-marked files before generating dev notes | `gds-create-story` | 4 |
| `815600e4` (#2347) | architecture `step-07` validation checklist no longer ships pre-primed | `gds-game-architecture` | 4 |
| `#1927` | PRD no longer silently de-scopes; explicit confirmation before scope reduction | folded into `gds-prd` | 1 |
| `c52c9b5b` (post-6.6) | new consolidated `bmad-prd` skill | → `gds-prd` | 1 |
| `24a81706` (post-6.6) | new `bmad-investigate` skill | → `gds-investigate` | 3 |
| `e36f219c` (#2360) | catalog columns `after`/`before` → `preceded-by`/`followed-by` | `module-help.csv` | 5 |
| `#2286` | `team: software-development` on agents | agent roster | 5 |

Skills with **only** architecture commits in the v6.3→v6.6 range — no content port needed: `bmad-code-review`, `bmad-sprint-planning`, `bmad-sprint-status`, `bmad-dev-story`, `bmad-retrospective`, `bmad-correct-course`, `bmad-check-implementation-readiness`.

## Pre-existing issues surfaced (not upstream-driven)

`module-help.csv` lists **3 phantom skills with no directories**: `gds-market-research`, `gds-technical-research`, `gds-quick-prototype`. The `gds-agent-game-dev` menu also has code `QP` → phantom `gds-quick-prototype`. Resolved in Phase 5 _(approved 2026-05-15)_: remove the rows + the `QP` menu entry and record "build research + prototype skills" in `TODO.md`, rather than building three skills mid-sync.

---

## Phase 1 — PRD consolidation → `gds-prd`

### Upstream source

`src/bmm-skills/2-plan-workflows/bmad-prd/` (BMAD-METHOD):

```
bmad-prd/
├── SKILL.md                                  # intent detection → create/update/validate modes
├── customize.toml                            # [workflow] + prd_template, validation_checklist, output_dir, external_handoffs…
├── assets/{prd-template.md, prd-validation-checklist.md,
│           validation-report-template.html, headless-schemas.md}
├── references/{facilitation-guide.md, headless.md, validation-render.md}
└── scripts/render-validation-html.py
```

The SKILL.md detects intent from the user's message (`create` / `update` / `validate`), then branches to the matching operating mode. Validate mode spawns a subagent and renders an HTML report; no `steps/` subdirectory.

### Current BMGD shape (to be replaced)

- `2-design/gds-create-prd/` — `steps-c/` (12 steps), `templates/`, `data/`
- `2-design/gds-edit-prd/` — `steps-e/` (5 steps), `data/`
- `2-design/gds-validate-prd/` — `steps-v/` (13 steps), `data/`

### Plan

1. Create `src/workflows/2-design/gds-prd/` modeled structurally on upstream `bmad-prd` (compact: `SKILL.md` + `customize.toml` + `assets/` + `references/` + `scripts/`).
2. Port `assets/`, `references/`, `scripts/render-validation-html.py` from upstream.
3. **Harvest game-specific content** from BMGD's PRD trio (`steps-c/`, `steps-e/`, `steps-v/`, `templates/`, `data/`) — any game-PRD terminology, game-domain checks, or sections that diverge from upstream — and fold it into `gds-prd`'s assets/references. Do not lose game-specific PRD content; the new structure is the container, the BMGD trio is the source of game adaptation.
4. Adaptation rewrites: `_bmad/bmm/` → `_bmad/gds/` config path; `bmad-agent-*` → `gds-agent-*`; preserve upstream intent-detection framing.
5. Delete `gds-create-prd/`, `gds-edit-prd/`, `gds-validate-prd/`.
6. Update the agent menu that exposes PRD (designer / solo-dev) — three trio menu codes collapse to one `gds-prd` entry.

### Phase 1 verification

- `gds-prd` resolves via installer smoke test; `npm run lint:md` clean.
- Grep confirms zero refs to `gds-create-prd` / `gds-edit-prd` / `gds-validate-prd` remain in `src/`.
- All three intent modes (create/update/validate) are reachable from `gds-prd/SKILL.md`.

---

## Phase 2 — GDD consolidation → `gds-gdd`

There is **no upstream GDD skill** — `gds-gdd` is modeled on the `gds-prd` built in Phase 1, adapted to game-design-document content. This is the largest net-new piece: `gds-edit-gdd` and `gds-validate-gdd` are currently thin stubs that delegate to the PRD counterparts, so consolidation actually *implements* real update/validate modes for the GDD for the first time.

### Current BMGD shape (to be replaced)

- `2-design/gds-create-gdd/` — `steps-c/` (14 steps), `templates/`, `game-types/` (**24 genre guides**), `game-types.csv`, `checklist.md`
- `2-design/gds-edit-gdd/` — stub, `steps-e/`, `data/`
- `2-design/gds-validate-gdd/` — stub, `steps-v/`, `data/` (incl. `genre-complexity.csv`)

### Plan

1. Create `src/workflows/2-design/gds-gdd/` with the same compact shape as `gds-prd`: `SKILL.md` (intent detection: create / update / validate) + `customize.toml` + `assets/` + `references/` + `scripts/`.
2. Move the GDD's irreplaceable game assets into `gds-gdd/assets/` (or `references/`): the **24 genre guides** (`game-types/`), `game-types.csv`, `genre-complexity.csv`, the GDD template, and `checklist.md`.
3. Harvest game-design content from `gds-create-gdd/steps-c/` (14 steps) into the create mode; build real update/validate modes (the old stubs only delegated) modeled on `gds-prd`'s, adapted to GDD structure (genre-compliance, game-type validation instead of domain/project-type).
4. Delete `gds-create-gdd/`, `gds-edit-gdd/`, `gds-validate-gdd/`.
5. Update the designer agent menu — GDD trio codes collapse to one `gds-gdd` entry.

### Phase 2 verification

- `gds-gdd` resolves; the 24 genre guides are reachable from the create mode.
- update/validate modes are real (no delegation-to-PRD stub language remains).
- Grep confirms zero refs to the three old GDD dirs.

---

## Phase 3 — New skill: `gds-investigate`

### Upstream source

`src/bmm-skills/4-implementation/bmad-investigate/` — `SKILL.md` (forensic case-file workflow), `customize.toml`, `references/case-file-template.md`.

### Plan

1. Create `src/workflows/4-production/gds-investigate/` as a 1:1 port of `bmad-investigate`.
2. Light adaptation only: `_bmad/bmm/` → `_bmad/gds/`; `case_file_subdir` default left as `investigations`; no game-specific rewrite of the investigation methodology (it is domain-neutral).
3. Add menu code `IN` to `gds-agent-game-dev/customize.toml` (`[[agent.menu]]`, `skill = "gds-investigate"`).
4. Add a `module-help.csv` row.

### Phase 3 verification

- `gds-investigate` resolves; `case-file-template.md` reference resolves.
- `gds-agent-game-dev` menu renders the new `IN` entry.

---

## Phase 4 — Content ports into existing skills

For each, diff the upstream commit and port the *content* delta into BMGD's already-migrated skill (steps live in the new `customize.toml`-era structure).

1. **`gds-create-epics-and-stories`** ← `1ad1f91e` (#1826): brownfield epic-scoping — file-overlap detection between epics, the Implementation Efficiency principle, and the design-completeness gate.
2. **`gds-create-story`** ← `c29b72ec` (#2274): read every UPDATE-marked file before generating dev notes (brownfield stories preserve current behavior).
3. **`gds-game-architecture`** ← `815600e4` (#2347): `step-07` validation checklist no longer ships pre-checked; status field templated against actual completion. _(BMGD's architecture skill is `gds-game-architecture`; locate the equivalent validation step.)_
4. **Agents** ← `#2286`: ensure `team: software-development` (or a game-dev team value) is set on each agent in the `module.yaml` roster.

### Phase 4 verification

- Each ported change is present and game-context-correct (paths, agent refs, terminology).
- `npm test` (lint + lint:md + format:check) passes in BMGD.

---

## Phase 5 — Catalog + config + cleanup

1. **`module-help.csv`**: rename header columns `after` → `preceded-by`, `before` → `followed-by` (upstream #2360). Update all rows.
2. Update `module-help.csv` for the structural changes: 3 PRD rows → 1 (`gds-prd`); 3 GDD rows → 1 (`gds-gdd`); add `gds-investigate`.
3. **Phantom-skill cleanup**: remove the `gds-market-research`, `gds-technical-research`, `gds-quick-prototype` rows and the `QP` menu code from `gds-agent-game-dev`; record "build research + prototype skills" in `TODO.md`.
4. `module.yaml`: confirm `agents:` roster has `team` per agent; confirm `project_name` decision (locked: stays module-level).

### Phase 5 verification

- `module-help.csv` skill column matches the set of `SKILL.md` directories on disk exactly (no phantoms).
- Header is `preceded-by`/`followed-by`.

---

## Phase 6 — Validation + release

1. `npm test` in BMGD (lint + lint:md + format:check) — clean.
2. Run BMAD-METHOD's `tools/validate-skills.js` against BMGD `src/` (adapt path config) — exits 0.
3. Smoke test: `npx bmad-method install` into a scratch directory — confirm `gds-prd`, `gds-gdd`, `gds-investigate` install and resolve; confirm the `resolve_*.py` scripts land in `_bmad/scripts/` (validates Decision 4 — `core` supplies them).
4. `CHANGELOG.md` entry; version bump `0.4.0` → `0.5.0`.
5. Update root `CLAUDE.md` ("Last reviewed" note, PRD/GDD trio → consolidated description) and `TODO.md`.

---

## Execution order + checkpoints

Phase-by-phase with a commit between each; each phase independently revertible via `git revert`.

1. Phase 1 — PRD consolidation → `gds-prd` — commit
2. Phase 2 — GDD consolidation → `gds-gdd` — commit
3. Phase 3 — `gds-investigate` port — commit
4. Phase 4 — content ports (epics-and-stories, create-story, architecture, agent `team`) — commit
5. Phase 5 — catalog + config + phantom cleanup — commit
6. Phase 6 — full `npm test` + validate-skills + install smoke test → CHANGELOG + version bump → commit → push

## Out of scope (deferred)

- Building `gds-market-research`, `gds-technical-research`, `gds-quick-prototype` (phantom rows removed; logged to `TODO.md`).
- Porting `bmad-customize` (relies on `core`).
- `bmad-product-brief` refactor (`#2370`/`#2371`) — BMGD's `gds-create-game-brief` is substantially game-specific; evaluate in a later sync.
- Collapsing the `gds-quick-flow/` wrapper directory (existing `TODO.md` item).
- Installer/platform changes (v6.5.0 42-platform support, channel resolution) — BMGD ships no installer; consumed via the `bmad-method` package.
