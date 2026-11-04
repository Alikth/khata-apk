# Architecture

## 1. Architectural goals

- Modular systems with clear responsibilities.
- Data-driven design for units, buildings, resources, weapons, and missions.
- Loose coupling through signals and small interfaces rather than hard
  dependencies.
- Deterministic and testable core behavior where practical.
- Mobile-first performance awareness, especially for unit count, pathfinding,
  and input.

## 2. High-level layout

```text
res://
├── project.godot
├── icon.svg
├── main.tscn
├── main.gd
├── autoloads/
│   ├── game_manager.gd
│   ├── resource_manager.gd
│   ├── save_manager.gd
│   ├── input_manager.gd
│   ├── audio_manager.gd
│   └── event_bus.gd
├── scenes/
│   ├── game/
│   ├── ui/
│   ├── units/
│   ├── buildings/
│   └── world/
├── scripts/
│   ├── core/
│   ├── entities/
│   ├── systems/
│   └── utils/
├── data/
│   ├── resources/
│   └── scripts/
├── content/
│   └── (sprites, audio, fonts, tiles, terrain assets)
└── tests/
    └── (GUT or manual test scenes)
```

The exact tree will be refined during implementation. This is the initial
intended layout.

## 3. Scene and node hierarchy

### 3.1 Main scene

`main.tscn` is the entry point. It should assemble:

- World/level root.
- UI layer.
- Global managers as autoloads or owned children.
- Camera controller.
- Input routing for selection, commands, and UI.

The main scene should not contain gameplay logic itself. It should compose
systems and hand off control to the active game context.

### 3.2 Game scene

A game context scene owns the active match or mission. Expected children:

- Terrain/map root.
- Building instances.
- Unit containers/managers.
- Selection system.
- Command executor.
- Objective/campaign controller.
- Save/load hooks.

### 3.3 UI layer

UI should be mostly separate from simulation. UI reads state through
resources, signals, or lightweight queries. UI should not own gameplay
simulation.

## 4. Core gameplay systems and responsibilities

### 4.1 Game manager

Responsibilities:

- Own high-level game state.
- Start, pause, resume, end gameplay.
- Coordinate transitions between menus, gameplay, and results.
- Host mission/campaign flow ownership.

### 4.2 Resource/data manager

Responsibilities:

- Provide access to data resources.
- Validate and expose definitions for units, buildings, resources,
  weapons, and missions.
- Keep definitions separate from runtime state.

### 4.3 Save/load manager

Responsibilities:

- Serialize and restore game state.
- Version saves and handle migration concerns.
- Avoid saving transient view state; save only meaningful simulation state.

### 4.4 Input manager

Responsibilities:

- Abstract touch and mouse/keyboard input.
- Translate screen gestures into gameplay commands.
- Support selection, camera control, context actions, and UI navigation.
- Keep input interpretation separate from simulation reactions.

### 4.5 Audio manager

Responsibilities:

- Play sound effects and music through a controlled interface.
- Manage categories, volume, and playback lifecycle.
- Avoid spreading audio calls across gameplay scripts.

### 4.6 Event bus / signals

Responsibilities:

- Provide typed or well-named global signals for cross-system events.
- Be used sparingly; prefer direct dependencies or interfaces when clean.
- Avoid making every system globally coupled through one mega-event bus.

## 5. Entity and component model

Units and buildings should be composed of small responsibilities instead of
one giant script.

Recommended components for units:

- `UnitState`: identity, stats, current resource needs, training state.
- `UnitMover`: movement requests, navigation integration, collision behavior.
- `UnitCombat`: targeting, attacking, damage handling, death.
- `UnitSelection`: selection membership, group links, command handling.

Recommended components for buildings:

- `BuildingState`: type, ownership, health, construction state.
- `BuildingProduction`: resource output, training, storage interaction.
- `BuildingPlacement`: placement validation, footprint, orientation.
- `BuildingCombat`: defensive capabilities if applicable.

Components should be scripts or small node subgraphs with clear interfaces.
Avoid putting all behavior into one node.

## 6. Data-driven models

Everything that is tunable should be a resource or structured data file,
not hardcoded constants scattered across scripts.

Expected resource types:

- `ResourceDefinition`: wood, stone, iron, food, gold, etc.
- `UnitDefinition`: cost, build time, stats, move/combat behavior hints,
  asset references.
- `BuildingDefinition`: footprint, cost, construction time, produced
  resources/units, abilities.
- `WeaponDefinition`: damage, range, fire rate, ammo/resource cost if any.
- `MissionDefinition`: objectives, starting state, win/lose conditions,
  spawns, AI config.
- `GameSettings`: input, audio, camera, and gameplay tuning values.

Data resources should be loadable by editor tools and testable in isolation.

## 7. System communication

Use a mix of these, in order of preference:

1. Direct typed calls for tightly related behavior.
2. Signals for "something happened" events that several systems may care about.
3. Small interfaces for dependencies that should be swappable or testable.
4. Event bus for occasional cross-cutting events, not as the default spine.

Examples:

- Selection system emits `selection_changed`.
- Combat system emits `unit_died`, `structure_damaged`, `resource_changed`.
- Economy system emits `resources_updated`.
- UI listens but does not mutate simulation directly where possible.

Avoid circular dependencies between systems. If a system needs another, it
should depend on an interface or manager, not on many unrelated systems.

## 8. Dependency relationships

Likely dependencies:

- Game flow depends on: input, UI, save, audio, economy, combat results.
- Economy depends on: buildings, population, resource definitions.
- Combat depends on: unit/weapon definitions, health, targeting, path state.
- AI depends on: economy observations, military state, map observations,
  mission goals.
- Pathfinding depends on: navigation mesh or grid, dynamic blockers,
  unit formation needs.

Keep dependencies mostly one-directional where possible:

- Data -> Definitions
- Definitions -> Runtime state
- Runtime state -> Systems
- Systems -> UI through signals/queries

## 9. World and terrain representation

The world needs:

- A navigable representation for pathfinding.
- A placement representation for buildings and unit movement.
- A resource/feature representation for trees, rocks, water, and similar.

Possible representations:

- NavigationPolyMesh for open movement.
- Grid-based navigation for tighter RTS control.
- Hybrid: mesh for long-range routing plus local avoidance behavior.

This is an architectural decision point, not a finalized choice.

## 10. Selection and control model

Selection should support:

- Single unit selection.
- Multi-selection, possibly with rectangle or tap-based grouping.
- Group creation and command broadcast to group members.
- Building selection with context actions.
- A selected-entity command interface shared by units and buildings where
  appropriate.

Selection state should be owned by a selection system, not scattered across
entities.

## 11. Pathfinding and movement

Pathfinding is a critical system. It should be designed for:

- Many units requesting paths.
- Group movement with reasonable local coordination.
- Dynamic obstacles such as buildings, walls, and enemies.
- Avoidance behavior for nearby units without full global replanning for
  every unit every frame.

Likely needs:

- A global path planner for route generation.
- A local movement/avoidance layer for crowded movement.
- Cached or throttled path requests for performance.
- Formation logic if groups should move in a structured shape.

This is one of the highest-risk areas. See risks below.

## 12. Combat model

Combat should be modeled around:

- Attack source, target, and weapon definition.
- Ranged fire resolution.
- Melee engagement behavior.
- Structure damage and possible capture.
- Siege behavior where applicable.
- Death, destruction, and resource/population consequences.

Avoid embedding balancing numbers directly in combat code; keep them in
weapon/unit definitions.

## 13. AI model

AI should be treated as a separate system with its own decision loop.

Likely responsibilities:

- Observe economy, production, and military state.
- Decide expansion, building, and troop priorities.
- Manage base layout and defense.
- Decide attack timing and target selection.

AI should read game state through a controlled interface so behavior can be
tested and adjusted without entangling it with player logic.

## 14. Save/load model

Save data should include enough to restore meaningful state:

- Resources, storage, population.
- Buildings and their state.
- Units and their state/location/ownership.
- Mission progress and objectives if relevant.
- Settings relevant to persistence.

Save format should be versioned. If resource definitions change, migration or
compatibility rules may be required.

## 15. UI model

UI should be organized into:

- Resource panel.
- Selection info panel.
- Build/command toolbar.
- Menus: main, pause, mission, settings.
- Feedback: alerts, toasts, tooltips where needed.

UI should stay thin against simulation. Prefer reading state and listening to
events over polling many systems every frame.

## 16. Android and mobile strategy

### 16.1 Performance

- Keep per-frame work bounded.
- Avoid updating large numbers of nodes unnecessarily.
- Batch where practical.
- Use object pooling for frequent entities/effects.
- Profile early on real device if possible.

### 16.2 Touch

- Design touch input first, not as an afterthought.
- Support tap, drag, pinch-zoom, and contextual actions.
- Ensure the control scheme is usable on smaller screens.
- Provide adequate feedback for selection and commands.

### 16.3 Thermal and battery

- Limit pathfinding and AI workload where possible.
- Scale update frequency with load.
- Avoid constant heavy computation in idle moments.

### 16.4 Screen and UI

- Support aspect-ratio and safe-area considerations.
- Keep critical controls accessible.
- Test UI legibility at mobile resolutions.

## 17. Testing strategy

Testing should happen at several levels:

- Data tests for definitions and tuning values.
- Unit tests for isolated logic: path request handling, command parsing,
  economy arithmetic, selection helpers.
- Component tests for movement, combat resolution, placement validation.
- Scene/integration tests for flows: build, train, select, command, combat,
  save/load.
- Manual play tests for feel, controls, and mobile usability.

Automated tests are strongly preferred for systems with tricky behavior, especially:

- Pathfinding integration.
- Group movement.
- Combat resolution.
- Save/load round-trips.

## 18. Risks

### 18.1 Large unit counts

Risk: Many units can cause CPU pressure in movement, combat, and rendering.

Mitigations:

- Object pooling.
- Coalesced updates.
- Capped or LOD-reduced logic for far/idle entities.
- Efficient spatial queries.

### 18.2 RTS pathfinding

Risk: Pathfinding for many units is expensive and behaviorally hard to tune.

Mitigations:

- Throttle path requests.
- Share paths where possible.
- Use a local avoidance layer.
- Keep the navigation representation simple and testable.

### 18.3 Group movement

Risk: Groups can clump, block each other, or behave inconsistently.

Mitigations:

- Define clear group command semantics early.
- Separate group pathing from individual collision behavior.
- Test crowded movement early with small scenarios.

### 18.4 Building placement

Risk: Placement rules can become inconsistent across terrain, existing
buildings, and movement paths.

Mitigations:

- Centralize placement validation.
- Define footprint, snapping, and obstruction rules clearly.
- Provide immediate placement feedback.

### 18.5 Combat simulation

Risk: Combat can become either too simple or too expensive.

Mitigations:

- Keep combat resolution data-driven.
- Limit active calculations to relevant entities.
- Avoid per-frame heavy combat simulation for distant or inactive units.

### 18.6 AI decision-making

Risk: AI can become brittle or expensive.

Mitigations:

- Build AI on top of the same definitions as the player where possible.
- Limit AI update frequency.
- Keep AI goals explicit and testable.

### 18.7 Save/load compatibility

Risk: Saves can break after data changes.

Mitigations:

- Version saves.
- Avoid storing fragile derived state where possible.
- Test save/load during data changes.

## 19. Open architectural questions

1. Navigation model: navigation mesh, grid, or hybrid?
2. Movement model: individual pathfinding, group pathing, or both?
3. Formation system: needed now, or later?
4. Local avoidance responsibility: part of movement component or separate?
5. How much AI behavior should be scripted vs data-driven?
6. How should multiplayer be handled if it is ever added?
7. How should art assets be organized for units, buildings, and terrain?
8. What is the minimum viable unit/building set for the first vertical slice?
