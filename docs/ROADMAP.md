# Roadmap

## 0. Pre-development

Done in this phase:

- Game design outline.
- Architecture document.
- Technical decisions document.
- Open questions catalog.
- Initial folder and scene structure.
- Tooling and testing approach agreed.

Outcome: a plan, not gameplay.

## 1. Foundation

Goals:

- Project setup and basic Godot structure.
- Autoloads or managers for game flow, input, audio, save, and resource access.
- Main scene composition.
- Basic camera control.
- Basic UI shell for resource display and menus.
- Data resource definitions for resources, units, buildings, weapons, and missions.
- Touch and desktop input abstraction.

Exit criteria:

- The project opens, loads data resources, and shows a usable empty game shell.
- Input abstraction works for at least one control scheme.
- Save/load interfaces exist even if minimal.

## 2. World and terrain

Goals:

- Terrain representation.
- Resource/feature placement: water, trees, rocks, gatherable points if applicable.
- Camera movement and framing for RTS play.
- Placement preview and terrain interaction basics.

Exit criteria:

- The player can view and navigate the map.
- Terrain features exist and are queryable.
- Building placement interaction is representable, even if not finished.

## 3. Selection and control

Goals:

- Unit and building selection.
- Multi-selection and group creation.
- Command interface: move, attack-move, stop, gather, build, or similar core commands.
- Selection feedback in UI.

Exit criteria:

- The player can select entities and issue basic commands.
- Selection state is owned by a dedicated system.
- UI reflects selection and command availability.

## 4. Movement and pathfinding

Goals:

- Pathfinding integration.
- Unit movement.
- Group movement behavior.
- Local collision/avoidance.
- Performance-aware path request handling.

Exit criteria:

- Units can move to targets along navigable routes.
- Groups move with acceptable behavior.
- Movement remains stable at small-to-moderate unit counts.

## 5. Economy and population

Goals:

- Resource gathering, storage, and consumption.
- Workforce/population model.
- Building production logic.
- Economy UI updates.

Exit criteria:

- Resources change in response to defined gameplay actions.
- Population and labor affect production meaningfully.
- Economy state is representable and testable.

## 6. Buildings and construction

Goals:

- Building placement validation.
- Construction flow.
- Building types: defensive, economic, housing, or similar according to design.
- Building interactions with economy and combat.

Exit criteria:

- Buildings can be placed and constructed under defined rules.
- Buildings contribute to economy/defense/population as designed.
- Placement rules are consistent and testable.

## 7. Military and combat

Goals:

- Unit training and costs.
- Unit stats and weapons from definitions.
- Ranged and melee combat behavior.
- Structure damage and siege behavior where applicable.
- Death and destruction consequences.

Exit criteria:

- Units can be trained and engage in combat.
- Combat results are derived from definitions, not hardcoded in combat code.
- Combat systems are testable in isolation and in scene flow.

## 8. AI

Goals:

- Enemy observation interface.
- Basic AI decision loop: economy, buildup, defense, attack.
- Target selection and attack behavior.
- Tunable AI difficulty or behavior parameters.

Exit criteria:

- AI can play a basic match without crashing or blocking the player.
- AI uses the same definition system where practical.
- AI behavior is adjustable and testable.

## 9. Campaign and game modes

Goals:

- Mission/campaign controller.
- Objectives, victory, and defeat conditions.
- Start and end state handling.
- Save/resume support where applicable.

Exit criteria:

- At least one playable mission flow exists end to end.
- Win/lose outcomes are handled cleanly.
- Save/load is integrated where needed.

## 10. Polish and Android readiness

Goals:

- Touch control refinement.
- UI polish and screen-adaption work.
- Audio integration and feedback.
- Performance profiling and optimization passes.
- Save compatibility checks.

Exit criteria:

- The game is playable and controlled comfortably on the target platform.
- Performance is acceptable for the intended unit/feature count.
- Major control and UX issues are resolved.

## 11. Post-vertical-slice work

Possible later work, depending on decisions:

- More units, buildings, weapons, and maps.
- Additional missions or campaigns.
- Balancing passes.
- Additional content, factions, or modes if approved.
- Localization, accessibility, or further mobile refinement.

## Open questions affecting the roadmap

- Which faction/unit/building set is in the first slice?
- Is multiplayer in scope?
- What campaign length and objective complexity are intended?
- What is the acceptable mobile performance target?
- Which art style and asset pipeline will be used?
