# Codex Handoff (BetterUI)

## Context Summary
Goal: Add a BetterUI keybinds category in ESO Controls, make BetterUI keybinds remappable, and add a dedicated shortcut to toggle Junk in Inventory. Also fix placement under "General" ("Общие"), ensure initial bindings inherit default gamepad mappings, and resolve binding XML script errors.

## Current State (as of this handoff)
- BetterUI now defines its own binding actions in `Bindings.xml` (in General category) and uses a no-op handler `BETTERUI_EmptyKeybind()` to satisfy the XML requirement for Down/Up scripts.
- Inventory and Banking keybinds were updated to use BetterUI action names (via `BETTERUI.CIM.CONST.KEYBINDS`).
- Initial load now copies default `UI_SHORTCUT_*` bindings into BetterUI actions **after** CIM runtime setup, and can repair if bindings are missing.
- "Down" binding was removed (not remappable) to avoid users breaking D-pad behavior. Search uses stock `UI_SHORTCUT_DOWN` again.
- A new remappable action exists: `BETTERUI_UI_MARK_JUNK`, exposed in the keybinds list and used in Inventory to toggle junk.
- Binding labels are localized; Russian labels are translated (including Toggle Junk).

## Key Files Updated
- `Bindings.xml` (BetterUI actions, General category, Down scripts)
- `BetterUI.txt` (Bindings.xml load order after localization files)
- `BetterUI.lua` (binding defaults copy/repair, `BETTERUI_EmptyKeybind`)
- `Modules/CIM/Constants.lua` (BetterUI keybind action names)
- `Modules/CIM/Keybinds/GenericKeybinds.lua` (uses BetterUI keybind names)
- `Modules/CIM/UI/HeaderSortIntegration.lua`
- `Modules/CIM/UI/HeaderSortController.lua`
- `Modules/CIM/Lists/TabBarScrollList.lua`
- `Modules/CIM/Lists/SubList.lua`
- `Modules/CIM/Core/SearchManager.lua` (Down uses stock shortcut)
- `Modules/CIM/Core/MultiSelectMixin.lua` (abort binding uses BetterUI TERTIARY)
- `Modules/CIM/Core/WindowClass.lua` (Back uses BetterUI binding)
- `Modules/Inventory/Keybinds/InventoryKeybinds.lua` (uses BetterUI bindings + Toggle Junk action)
- `Modules/Inventory/Actions/SlotActions.lua` (primary uses BetterUI binding)
- `Modules/Banking/Keybinds/KeybindManager.lua` (uses BetterUI bindings + Back)
- `lang/en.lua`, `lang/de.lua`, `lang/es.lua`, `lang/fr.lua`, `lang/jp.lua`, `lang/ru.lua`, `lang/zh.lua` (binding label strings)

## Notable Behaviors
- BetterUI binding defaults are copied from stock `UI_SHORTCUT_*` actions only if the BetterUI actions are unbound.
- If bindings go missing, `ShouldRepairBindings()` will copy defaults again.
- BetterUI keybinds appear under Controls > General ("Общие") > BetterUI.
- Search "Down" exit is not remappable (uses stock UI shortcut).

## Recent User Requests
- Add a quick keybind to toggle junk.
- Remove remappable "Down" binding.
- Translate "Toggle Junk" to Russian.

## Known Issues / Open Questions
- Git status showed clean on this machine even after edits; if you need a diff, run `git diff` or `git status` locally on your machine to verify.
- If users already started with the old build and bindings remain empty, setting `bindingsInitialized = false` in SavedVariables forces a repair on next load.

## Suggested Next Steps
1. Validate in-game: ensure BetterUI category appears under Controls > Общие and bindings are populated.
2. Verify LB/RB work inside BetterUI UI with default bindings.
3. Confirm Toggle Junk works on Inventory items and respects lock/companion rules.

