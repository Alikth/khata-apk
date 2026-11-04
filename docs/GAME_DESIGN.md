# Game Design Document

## 1. Vision and scope

A medieval real-time strategy game inspired by Stronghold Crusader.

- Core fantasy: raise a castle, gather resources, train troops, besiege a
  fortified enemy, and win through economy, fortifications, and force.
- Primary platform: Android.
- Development/testing: Godot desktop builds.

This document records the target design. Open questions are listed explicitly
and must be settled before implementation.

## 2. Core gameplay loop

1. Gather and store natural resources.
2. Expand population and workforce.
3. Build economy and production buildings.
4. Train and equip military units.
5. Expand and fortify the castle.
6. Attack or defend against the enemy.
7. Complete campaign objectives or win the skirmish.

This is the intended loop. Exact unit/building lists are not finalized yet.

## 3. Target audience and tone

- Audience: players who enjoy classic castle RTS gameplay.
- Tone: medieval fortress warfare with a focus on economy and siege,
  not a narrative-heavy experience.

## 4. World and setting

- Top-down or isometric medieval terrain.
- Terrain contains usable natural resources and obstructions.
- Water, trees, rocks, and other features should affect movement,
  building placement, or resource availability.

Open question: Is the perspective top-down, isometric, or hybrid?
Open question: What is the world scale in game units?

## 5. Core mechanics and systems

See the Persian roadmap outline provided by the user. The intended systems
are:

1. Core: scene management, settings, data, save/load.
2. Terrain and map: ground, features, water, trees, rocks, resources, camera.
3. Selection and control: unit, group, and building selection; player commands.
4. Movement and pathfinding: unit movement, group movement, collision, routing.
5. Building mechanics: placement, walls, towers, gates, houses, economic buildings.
6. Economy and resources: wood, stone, iron, food, gold, storage, production.
7. Population and labor: peasants/workers, population capacity, citizen morale.
8. Military: unit types, training, costs, equipment, formations.
9. Combat and siege: attacks, defense, shooting, catapults, wall damage, capture.
10. Enemy AI: economy, castle development, army building, defense, attack.
11. UI and audio: resource panel, menus, toolbar, sound, effects.
12. Campaign and game system: stages, objectives, victory, defeat, save/load.

These are target systems, not approved detailed mechanics.

## 6. Progression and objectives

Two expected modes:

- Campaign: staged missions with objectives and win/lose conditions.
- Skirmish/free play: open gameplay against AI or other players where
  applicable.

Open question: Is multiplayer in scope?
Open question: Are there multiple factions or a single player faction?

## 7. Combat and challenge

Expected combat elements:

- Ranged and melee units.
- Fortifications and gates.
- Siege weapons.
- Wall damage and structure capture.

Combat balance is not defined yet.

Open question: What win conditions define combat success in each mode?
Open question: How detailed should siege destruction be on mobile?

## 8. Player expression and customization

Expected expression:

- Choosing where to build.
- Choosing how to expand economy vs military.
- Choosing army composition and siege tactics.

Open question: Is unit or building customization in scope, or only tactical
choices?

## 9. Monetization and progression beyond gameplay

No monetization model is defined.

Open question: Are cosmetics, optional passes, or ads in scope?
Open question: Will there be content updates or multiple campaigns?

## 10. Technical constraints

- Android touch controls are first-class.
- The architecture should handle potentially large unit counts on mobile.
- Save/load compatibility should be planned early.
- Performance and battery concerns must be considered from the start.

## 11. Open design questions

1. Exact resource list and balance.
2. Exact unit roster and role split.
3. Exact building roster and tech/access rules.
4. Perspective and camera behavior.
5. World scale and map size limits.
6. Factions, if any.
7. Multiplayer scope.
8. Campaign objective language and mission structure.
9. Monetization, if any.
10. Art/atmosphere direction and asset pipeline.

These must be answered before detailed gameplay implementation begins.
