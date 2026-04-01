---
linkTitle: Quickstart
title: "Quickstart: Build Your First Game in O3DE"
description: >
  Go from installation to a playable Gem Collector game in about 4 hours.
  No coding required — just entities, components, and visual scripting.
toc: true
weight: 50
---

**Open 3D Engine (O3DE)** is a free, open-source, real-time 3D engine for games and simulations. In this quickstart series you'll build **Gem Collector** — a top-down arena where you move a ball with WASD and collect glowing gems scattered across the level. By the end, you'll have a playable game and a working understanding of the core concepts every O3DE project uses.

```
┌─────────────────────────────┐
│   * Gem        * Gem        │
│                             │
│        [  Player  ]         │
│                             │
│   * Gem   * Gem   * Gem     │
└─────────────────────────────┘
   WASD to move · collect all gems to win
```

## What you'll learn

- **Entities and components** — how every object in an O3DE level is built
- **PhysX** — adding real physics and trigger volumes to entities
- **Script Canvas** — O3DE's visual scripting tool (no code required)
- **Input bindings** — mapping keyboard keys to named game events
- **Asset Processor** — how O3DE processes your assets automatically in the background

## Time map

| Step | Topic | Estimated time |
|------|-------|---------------|
| [1. Install and set up](1-install-and-setup) | Install O3DE and create your project | ~45 min (includes first build) |
| [2. Create a level](2-create-a-level) | Create a level and add a ground plane | ~20 min |
| [3. Create the player entity](3-player-entity) | Add a sphere with physics | ~20 min |
| [4. Script player movement](4-player-movement) | WASD movement in Script Canvas | ~35 min |
| [5. Add collectible gems](5-collectibles) | Trigger volumes and collection logic | ~35 min |
| [6. Test and play](6-test-and-play) | Run the game and troubleshoot | ~15 min |

Total: approximately **3–4 hours**, depending on download and build speed.

## Before you begin

Verify your system meets the [O3DE system requirements](/docs/welcome-guide/requirements/). The key minimums for this tutorial are:

| Component | Minimum |
|-----------|---------|
| OS | Windows 10 version 20H2 or later · Ubuntu 22.04 LTS or later |
| CPU | Quad-core 64-bit x86, 2.5 GHz |
| RAM | 16 GB |
| GPU | DirectX 12 or Vulkan-compatible, 2 GB VRAM |
| Disk | 40 GB free (for the pre-built installer) |

{{< note >}}
This quickstart uses the **pre-built O3DE installer** — the fastest way to get started. If you prefer to build from source (GitHub), follow the [source setup guide](/docs/welcome-guide/setup/setup-from-github/) and then rejoin at [Step 2](2-create-a-level) once you have the Editor open.
{{< /note >}}

Ready? Start with [Step 1: Install and Set Up →](1-install-and-setup)
