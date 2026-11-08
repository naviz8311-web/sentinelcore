# SentinelCore

**Backend engineering & networking showcase mod for Minecraft 1.20.1 Forge**

---

## Overview

SentinelCore is a professional chunk-claiming and protection system. Players claim 16×16 chunk regions, set trust tiers for other players, and rely on server-authoritative enforcement to prevent griefing. All claim data persists across server restarts using Forge's `WorldSavedData` API.

---

## Features

| Feature | Status |
|---|---|
| Chunk claiming / unclaiming | ✅ |
| Claim ownership (per-player) | ✅ |
| Trust tiers: VISITOR / MEMBER / MANAGER / OWNER | ✅ |
| Block break/place protection | ✅ |
| Container / interactive block protection | ✅ |
| Entity interaction protection | ✅ |
| Explosion blocking (per-claim flag) | ✅ |
| Persistent world storage (NBT WorldSavedData) | ✅ |
| Admin commands (force unclaim, info, limit) | ✅ |
| Multiplayer server-side enforcement | ✅ |
| Operator bypass (permission level ≥ 2) | ✅ |

---

## Commands

| Command | Description |
|---|---|
| `/sentinel claim` | Claim the chunk you're standing in |
| `/sentinel unclaim` | Release your claim on the current chunk |
| `/sentinel info` | Show owner and trust info for the current chunk |
| `/sentinel list` | List all chunks you own |
| `/sentinel trust <player> <VISITOR\|MEMBER\|MANAGER>` | Grant trust |
| `/sentinel untrust <player>` | Remove a player's trust |
| `/sentinel admin unclaim` | Force-unclaim any chunk (op only) |
| `/sentinel admin info` | Show raw claim data (op only) |
| `/sentinel admin limit <n>` | Set max claims per player (op only) |

---

## Trust Levels

| Level | Can Enter | Can Build | Can Open Chests | Can Manage Trust |
|---|---|---|---|---|
| VISITOR | ✅ | ❌ | ❌ | ❌ |
| MEMBER | ✅ | ✅ | ✅ | ❌ |
| MANAGER | ✅ | ✅ | ✅ | ✅ (VISITOR/MEMBER only) |
| OWNER | ✅ | ✅ | ✅ | ✅ (all levels) |

---

## Installation

1. Install **Minecraft Forge 1.20.1-47.3.0**
2. Place `sentinelcore-1.0.0.jar` in your `mods/` folder
3. Start the server or client
4. Claim data saves to `[world]/data/sentinelcore_claims.dat`

---

## Building

```bash
./gradlew build
# Output: build/libs/sentinelcore-1.0.0.jar
```

Run in dev environment:
```bash
./gradlew runServer   # Dedicated server
./gradlew runClient   # Client with mod loaded
```

---

## Architecture

```
com.navdeep.sentinelcore/
├── SentinelCore.java           # Mod entry point, event bus registration
├── claim/
│   ├── ChunkPos2D.java         # Immutable chunk+dimension key
│   ├── ChunkClaim.java         # Claim data model (owner, trust, flags)
│   ├── ClaimManager.java       # Singleton in-memory registry
│   └── TrustLevel.java         # Permission tier enum
├── command/
│   └── SentinelCommands.java   # /sentinel command tree (Brigadier)
├── event/
│   └── ProtectionEventHandler.java  # Forge event hooks (block/entity/explosion)
└── storage/
    └── ClaimStorageManager.java     # WorldSavedData persistence
```

**Key design decisions:**
- `ClaimManager` is a thread-safe singleton, separate from persistence. This cleanly separates data access logic from storage.
- `WorldSavedData` is used instead of custom file I/O — it hooks into Minecraft's existing save system, ensuring data is never lost on crashes (auto-save).
- All protection is server-side only. No client-side mod is required.
- Operators (permission level ≥ 2) bypass all restrictions, maintaining server admin control.

---

## Known Limitations

- No per-player claim map GUI (chat-based only)
- Claim limits are per-session; setting changes don't survive server restarts (would require config file in a future version)
- No chunk visualization (no block highlight for claim boundaries)
