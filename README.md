# Multiplayer Loot Arena

A small multiplayer Roblox arena where players collect loot and fight with a projectile and a dash ability.
All gameplay decisions (loot, cooldowns, damage, kills) are made on the server.

## Approach

- **Server authority.** The client only sends input (ability name + aim direction). The server validates the request, enforces the cooldown, spawns the projectile, applies damage, and owns the loot and kill counts.
- **Services pattern.** Server logic is split into small services, each with one job, started from `Main.server.lua`:
  - `PlayerDataService`: per-player Loot and Kills (single source of truth, mirrored to Attributes and `leaderstats`)
  - `LootService`: spawn, collect, respawn
  - `AbilityService`: validates remote requests and runs abilities from a registry
  - `HealthService`: damage and kill credit
- **Ability system built for expansion.** Every ability inherits from `AbilityBase` (cooldown, alive check, direction sanitizing) and only implements `Execute()`. Adding a new ability means one new module plus one `register(...)` line in `AbilityService`.
- **Config in one place.** All tuning numbers live in `Shared/Config.lua`.
- **Loot.** Each spawn point is a "slot". On touch, the server clears `slot.part` before doing anything else. There is no yield in between, so two players touching the same loot at the same time cannot both collect it.
- **Projectile.** Spawned by the server (so every player sees it), moved with `LinearVelocity`, and hit detection is a per-frame raycast from the last position to the current one, so fast projectiles do not tunnel through walls or players.
- **UI.** Built from code in `UIController`. It shows Loot, Kills and ability cooldowns. The HUD reads Attributes set by the server.

## What I Completed

- Multiplayer arena (default Roblox characters, simple map)
- Server-side loot spawning, collection, per-player loot count, respawn
- Primary ability: Projectile (visible trail, hit effect, damage)
- Extra: Dash ability
- Extra: server-enforced cooldown system with HUD feedback
- Extra: health and damage system with kill tracking (kill credit window)
- Loot and kills HUD plus `leaderstats`
- Validation of remote input (type checks, NaN and zero-vector rejection, unknown ability names ignored)
- Cleanup when a player leaves
- Flower snow (extra visual effect)
- Music (background music)

## What I Did Not Complete

- No saved data (DataStore). Loot and kills reset when the server closes.
- No ability upgrades using loot (structure allows it, not implemented).
- No mobile or gamepad controls (keyboard and mouse only).
- No automated tests.
- Loot has no spin or bob animation, and there is no pickup sound.
- No teams or friendly-fire rules.

## What I Would Improve With More Time

- Spend loot on ability upgrades (e.g. damage, cooldown) through a small upgrade service.
- Client-side visual prediction for the projectile so it feels instant at high ping, while the server stays authoritative.
- Per-player rate limiting on the remote, beyond the cooldown.
- Pool projectile and loot parts instead of creating and destroying them.
- Proper match flow: rounds, scoreboard, winner screen.
- DataStore for progression.

## Setup / Run Instructions

**Option A: open the `.rblx` file**
1. Open `LootArena.rblx` in Roblox Studio.
2. To test multiplayer: **Test > Clients and Servers**, set players to 2 or more, and click Start.

**Option B: build from the repo with Rojo**
1. Install [Rojo](https://rojo.space) and run `rojo serve` in the repo root.
2. In a new empty place, connect with the Rojo plugin.
3. Open the Command Bar (View > Command Bar), paste the contents of `tools/ArenaBuilder.lua`, and run it. This builds the map and creates `Workspace.Arena` with `Loot`, `LootSpawns` and `Projectiles` folders.
4. Press Play.

**Controls**
- `E` or Left Click: Projectile
- `Q`: Dash

## Assumptions

- Projectile and Dash are horizontal only (`HorizontalOnly = true` in Config), which suits a simple arena.
- Free-for-all: no teams, and a player cannot hurt themselves.
- One loot item per spawn point. Respawn happens at the same point after 5 seconds.
- Spawn points have no ForceField (`Duration = 0`) so damage can be tested right away.
- Loot count persists through death (it is not lost on respawn) but not between sessions.

## Testing

Update this section with what you actually tested before submitting.

- [ ] 2+ players: loot counts stay separate
- [ ] Projectile visible to all players and deals damage
- [ ] Cooldown holds when spam-clicking
- [ ] Kill count increases for the killer
- [ ] Player leaving mid-game causes no errors in the Output window# Multiplayer Loot Arena

A small multiplayer Roblox arena where players collect loot and fight with a projectile and a dash ability.
All gameplay decisions (loot, cooldowns, damage, kills) are made on the server.

## Approach

- **Server authority.** The client only sends input (ability name + aim direction). The server validates the request, enforces the cooldown, spawns the projectile, applies damage, and owns the loot and kill counts.
- **Services pattern.** Server logic is split into small services, each with one job, started from `Main.server.lua`:
  - `PlayerDataService`: per-player Loot and Kills (single source of truth, mirrored to Attributes and `leaderstats`)
  - `LootService`: spawn, collect, respawn
  - `AbilityService`: validates remote requests and runs abilities from a registry
  - `HealthService`: damage and kill credit
- **Ability system built for expansion.** Every ability inherits from `AbilityBase` (cooldown, alive check, direction sanitizing) and only implements `Execute()`. Adding a new ability means one new module plus one `register(...)` line in `AbilityService`.
- **Config in one place.** All tuning numbers live in `Shared/Config.lua`.
- **Loot.** Each spawn point is a "slot". On touch, the server clears `slot.part` before doing anything else. There is no yield in between, so two players touching the same loot at the same time cannot both collect it.
- **Projectile.** Spawned by the server (so every player sees it), moved with `LinearVelocity`, and hit detection is a per-frame raycast from the last position to the current one, so fast projectiles do not tunnel through walls or players.
- **UI.** Built from code in `UIController`. It shows Loot, Kills and ability cooldowns. The HUD reads Attributes set by the server.

## What I Completed

- Multiplayer arena (default Roblox characters, simple map)
- Server-side loot spawning, collection, per-player loot count, respawn
- Primary ability: Projectile (visible trail, hit effect, damage)
- Extra: Dash ability
- Extra: server-enforced cooldown system with HUD feedback
- Extra: health and damage system with kill tracking (kill credit window)
- Loot and kills HUD plus `leaderstats`
- Validation of remote input (type checks, NaN and zero-vector rejection, unknown ability names ignored)
- Cleanup when a player leaves
- Flower snow (extra visual effect)
- Music (background music)

## What I Did Not Complete

- No saved data (DataStore). Loot and kills reset when the server closes.
- No ability upgrades using loot (structure allows it, not implemented).
- No mobile or gamepad controls (keyboard and mouse only).
- No automated tests.
- Loot has no spin or bob animation, and there is no pickup sound.
- No teams or friendly-fire rules.

## What I Would Improve With More Time

- Spend loot on ability upgrades (e.g. damage, cooldown) through a small upgrade service.
- Client-side visual prediction for the projectile so it feels instant at high ping, while the server stays authoritative.
- Per-player rate limiting on the remote, beyond the cooldown.
- Pool projectile and loot parts instead of creating and destroying them.
- Proper match flow: rounds, scoreboard, winner screen.
- DataStore for progression.

## Setup / Run Instructions

**Option A: open the `.rblx` file**
1. Open `LootArena.rblx` in Roblox Studio.
2. To test multiplayer: **Test > Clients and Servers**, set players to 2 or more, and click Start.

**Option B: build from the repo with Rojo**
1. Install [Rojo](https://rojo.space) and run `rojo serve` in the repo root.
2. In a new empty place, connect with the Rojo plugin.
3. Open the Command Bar (View > Command Bar), paste the contents of `tools/ArenaBuilder.lua`, and run it. This builds the map and creates `Workspace.Arena` with `Loot`, `LootSpawns` and `Projectiles` folders.
4. Press Play.

**Controls**
- `E` or Left Click: Projectile
- `Q`: Dash

## Assumptions

- Projectile and Dash are horizontal only (`HorizontalOnly = true` in Config), which suits a simple arena.
- Free-for-all: no teams, and a player cannot hurt themselves.
- One loot item per spawn point. Respawn happens at the same point after 5 seconds.
- Spawn points have no ForceField (`Duration = 0`) so damage can be tested right away.
- Loot count persists through death (it is not lost on respawn) but not between sessions.

## Testing

Update this section with what you actually tested before submitting.

- [ ] 2+ players: loot counts stay separate
- [ ] Projectile visible to all players and deals damage
- [ ] Cooldown holds when spam-clicking
- [ ] Kill count increases for the killer
- [ ] Player leaving mid-game causes no errors in the Output window
