# Agent instructions (scope: this directory and subdirectories)

## Scope and layout
- **This AGENTS.md applies to:** repo root and all subdirectories.
- **Project focus:** ESO addon that reshapes the **gamepad UI**. The game has two UI modes (gamepad vs keyboard/mouse). BetterUI targets gamepad; do not regress KBM flows.
- **Key directories:**
- `Modules/` feature modules (CIM, Inventory, Banking, ResourceOrbFrames, WritUnit).
- `Source/` core/shared Lua/XML assets and templates.
- `lang/` localization tables.
- `tools/` scripts and standalone Lua tests.
- `docs/` contributor and architecture documentation (start at `docs/README.md`).

## Modules / subprojects
| Module | Type | Path | What it owns | How to run | Tests | Docs | AGENTS |
|--------|------|------|--------------|------------|-------|------|--------|
| core | addon | `BetterUI.lua`, `BetterUI.txt`, `Source/` | Entry point, load order, shared XML/templates | Install addon, `/reloadui` | `luac -p <file>` | `docs/reference/architecture.md` | `AGENTS.md` |
| CIM | addon module | `Modules/CIM/` | Shared UI patterns, lists, tooltips, feature flags | Loaded via `BetterUI.txt` | `lua tools/tests/run_all_tests.lua` (shared utils) | `docs/reference/architecture.md` | `AGENTS.md` |
| Inventory | addon module | `Modules/Inventory/` | Gamepad inventory UX, lists, keybinds | Loaded via `BetterUI.txt` | `lua tools/tests/test_*.lua` (as needed) | `docs/guides/testing-guide.md` | `AGENTS.md` |
| Banking | addon module | `Modules/Banking/` | Bank/house bank UX, lists, keybinds | Loaded via `BetterUI.txt` | `lua tools/tests/test_*.lua` (as needed) | `docs/guides/testing-guide.md` | `AGENTS.md` |
| ResourceOrbFrames | addon module | `Modules/ResourceOrbFrames/` | Gamepad resource orb UI + skill bar | Loaded via `BetterUI.txt` | N/A | `docs/guides/resource-orb-texture-guide.md` | `AGENTS.md` |
| WritUnit | addon module | `Modules/WritUnit/` | Writ quest tracking | Loaded via `BetterUI.txt` | N/A | `docs/reference/architecture.md` | `AGENTS.md` |
| Docs | docs | `docs/` | Contributor guides, architecture, planning, release notes | N/A | N/A | `docs/README.md` | `AGENTS.md` |

## Core workflows and invariants
- **Load order matters:** files load in `BetterUI.txt`; CIM must load before Inventory/Banking. Update the manifest when adding files.
- **Scene lifecycle symmetry:** match `SHOWING`/`HIDING`/`HIDDEN` cleanup to avoid stuck inputs. Always deactivate directional input on hide.
- **Keybinds:** remove before add, then update: `RemoveKeybindButtonGroup` -> `AddKeybindButtonGroup` -> `UpdateKeybindButtonGroup`.
- **Shared code location:** shared utilities belong in `Modules/CIM/` only (do not create new "Shared" folders).
- **Namespace:** everything lives under the global `BETTERUI` table; avoid unintended globals (`local` at file scope).
- **XML constants:** in Lua use canonical `BETTERUI.CIM.CONST.*`; XML must use global aliases (see `Modules/CIM/Constants.lua`).

## Conventions (Lua)
- **File headers required** for every Lua file (see `docs/guides/contributing-guide.md`).
- **Function docs required** for important functions (see contributing guide).
- **TODO format:** `TODO(refactor|cleanup|fix|optimization): ...` and remove when done.
- **SafeExecute:** use for external API calls; do not use to mask bugs.
- **Guard clauses:** nil/type checks before deep access; avoid stale global captures at file load time.

## Testing and validation
- **Syntax:** `luac -p <changed file>` (also for XML-adjacent Lua).
- **Standalone tests:** `lua tools/tests/run_all_tests.lua` or targeted `lua tools/tests/test_*.lua`.
- **Manual in-game checks:** follow `docs/guides/testing-guide.md` (inventory/banking/orbs regressions).

## Planning and documentation hygiene
- **When behavior/workflow changes:** update `docs/publishing/changelog.txt` and relevant guide sections.
- **When adding durable feature work:** record in `docs/planning/feature-requests.md` and promote P0/P1 to `docs/planning/priority-backlog.md`.
- **Current plan tracker:** `docs/planning/project-improvements.md`.
- **Session handoff:** keep `docs/planning/continuity-ledger.md` current.
- **Tribal knowledge:** add new gotchas to `docs/reference/tribal-knowledge.md`.

## Resource Orb Frames assets
- Textures live in `Modules/ResourceOrbFrames/Textures` and must match required filenames.
- Use `tools/ConvertPngToDds.ps1` with the ResourceOrbFrames profile for conversions.

## Do not
- Do not introduce new shared folders outside `Modules/CIM/`.
- Do not leave `d()` debug statements in code.
- Do not call `IsKeyDown` or use `ZO_DirectionalInput` directly; use ethereal keybinds.
- Do not call quest item APIs via `CallSecureProtected` or hide scenes before quest item use.
- Do not modify tooltip methods after controls are created; hook instance methods instead.

## Docs index
- Start at `docs/README.md` for full documentation map and links.
