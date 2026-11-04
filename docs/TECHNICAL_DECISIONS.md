# Technical Decisions

This document records chosen technical directions and open questions. Chosen
directions are initial preferences, not irreversible commitments.

## 1. Engine and language

- Engine: Godot 4.
- Language: GDScript, with typed annotations where practical.
- Primary target: Android.
- Desktop used for development and testing.

## 2. Architecture style

- Modular systems owned by managers or components.
- Data-driven definitions for tunable gameplay content.
- Signals and small interfaces for cross-system communication.
- Avoid monolithic scripts.

Decision: Use component-style separation for units and buildings.
Open question: How granular should components be? Fine-grained components
improve flexibility but increase wiring overhead.

## 3. Scene organization

- Separate scene roots for world, UI, units, and buildings.
- Use autoloads for cross-cutting managers where appropriate.
- Keep gameplay scenes composable rather than one giant root.

Decision: Main scene composes managers and the active game context.
Open question: Should managers be autoloads or owned by the main scene? The
current preference is autoloads for true global services and owned children
for game-context state.

## 4. Data representation

- Use Godot `Resource` types for definitions where practical.
- Keep definitions separate from runtime state.
- Put balancing numbers in data, not in behavior scripts.

Decision: Resource-based definitions for units, buildings, weapons, resources,
and missions.
Open question: Should definitions be loaded entirely at startup or lazily?
Startup loading is simpler; lazy loading may help large content sets.

## 5. Navigation and pathfinding

This is the most important technical decision area.

Options:

- NavigationServer-based navigation mesh.
- Grid-based navigation.
- Hybrid mesh + local avoidance.

Decision: No final navigation model chosen yet. The architecture should allow
a switchable navigation backend.
Open question: Should pathfinding be global mesh-based, local grid-based, or a
hybrid? A hybrid is common in RTS games, but it adds complexity.
Open question: Should groups share a path or each compute one?

## 6. Movement

Decision: Movement should be split into:

- Path request and path representation.
- Movement execution.
- Local avoidance/collision where needed.

Open question: Is a formation system required in the first slice, or should
groups move as aggregated entities initially?
Open question: Should avoidance be simple spacing behavior or a more structured
local steering system?

## 7. Selection model

Decision: Selection is owned by a dedicated selection system.
- Single selection.
- Multi-selection.
- Group recall.
- Building selection with context actions.

Open question: Should selection be built around entity IDs, node references, or
a mixed approach? Entity IDs are safer for save/load and decoupled behavior.

## 8. Combat

Decision: Combat behavior should be defined by weapon and unit data, with
combat systems operating on those definitions.
- Damage calculation uses weapon/unit data.
- Ranged and melee behavior are separate concerns.
- Structure damage and siege are explicit systems.

Open question: Should combat be event-driven, polling-based, or hybrid?
Open question: How granular should combat resolution be on mobile?
Open question: Should siege weapons be separate unit types or a building/unit
category?

## 9. Economy

Decision: Economy is a system with:

- Resource states.
- Production from buildings and population.
- Consumption and storage rules.

Open question: Should resources be unified under one economy service, or split
by resource type?
Open question: Should population be a single pool or split into roles?
Open question: Should morale be a numeric stat, a simple state flag, or omitted
from the first slice?

## 10. AI

Decision: AI should read game state through a controlled interface, not by
directly reaching into player internals.
- AI owns its decision loop.
- AI uses the same definition types where possible.
- AI behavior should be tunable.

Open question: Should AI be behavior-tree-based, utility-based, scripted
priority-based, or state-machine-based?
Open question: Should AI simulate a full economy or only a simplified decision
model?
Open question: Should AI difficulty be expressed as parameters or as separate
AI variants?

## 11. Save/load

Decision: Save system should be versioned and should persist meaningful
simulation state, not transient UI or view state.
- Saves should round-trip through load/save tests.
- Definitions changes may require migration handling.

Open question: Should saves be JSON, Godot resources, or a custom format?
Open question: Should partial/slot saves be supported?
Open question: Should campaign progress and mission state be saved separately
from in-match state?

## 12. Input

Decision: Input should be abstracted so gameplay commands are separate from
how they are issued.
- Support touch and desktop input.
- Translate gestures into commands.
- Keep command execution independent of input source.

Open question: Should the control scheme use tap-to-select, drag-to-select,
both, or another scheme?
Open question: Should camera control and unit command input share a unified
input manager or be separate?

## 13. Performance approach

Decision: Performance measures should be planned early and validated with
profiling, not assumed.
- Object pooling for frequently created entities and effects.
- Bounded per-frame work.
- Throttled or batched expensive operations.
- Mobile-aware update strategy.

Open question: What unit count is the real target for the first released slice?
Open question: Which systems are most likely to need LOD-like simplification
on mobile?

## 14. UI

Decision: UI should be mostly read-only against simulation and updated through
signals or queries.
- Resource panel.
- Selection panel.
- Command/build toolbar.
- Menus and feedback overlays.

Open question: Should UI be Godot Control-node-based, or partially custom?
Open question: Should localization be supported from the start?

## 15. Audio

Decision: Audio should go through a small manager interface rather than ad hoc
playback across scripts.
- Sound effects for selection, movement, combat, building, and feedback.
- Music and ambient audio as appropriate.

Open question: Should audio be fully implemented now, or stubbed behind an
interface until content is ready?

## 16. Testing

Decision: Use a mix of automated tests and manual playtesting.
- Automated tests for core logic and data.
- Flow tests for build, train, move, combat, and save/load.
- Manual tests for control feel and mobile usability.

Open question: Should the project adopt a Godot unit-testing framework such as
GUT, or use a lighter custom test approach?
Open question: Which systems must have automated tests before being marked
complete?

## 17. Asset pipeline

Open question: What art style is intended?
Open question: Are tiles, sprites, or both required?
Open question: Are animated units required in the first slice?
Open question: What audio assets are needed, and who provides them?

## 18. Scope boundaries

Open question: What is the minimum viable slice?
Open question: Which features are explicitly deferred?
Open question: Should the first slice focus on one skirmish opponent, one map,
and a small unit/building set?

## 19. Known technical risks

- Pathfinding cost and behavior with many units.
- Group movement quality.
- Building placement consistency.
- Combat cost and clarity.
- Save compatibility after data changes.
- Mobile performance under sustained load.

These risks should be validated with small prototype tests before heavy
development depends on them.
