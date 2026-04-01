---
linkTitle: "6. Test and Play"
title: "Step 6: Test and Play"
description: >
  Run a final check, play the game in the editor, diagnose common issues,
  and discover what to build next.
toc: true
weight: 600
---

You've built all the pieces. In this final step you'll do a pre-flight check, play the game, troubleshoot anything that doesn't work, and see where to take the project next.

**Estimated time:** ~15 minutes

---

## Pre-flight checklist

Before pressing play, verify the key parts are in place:

| Entity | Required components | Check |
|--------|---------------------|-------|
| `Ground` | Mesh, PhysX Static Rigid Body, PhysX Mesh Collider | ☐ |
| `Player` | Mesh, PhysX Rigid Body (Angular X/Y/Z locked), PhysX Collider, Tag (`Player`), Input, Script Canvas (`PlayerMovement`) | ☐ |
| `Gem_01` – `Gem_05` | Mesh, PhysX Static Rigid Body, PhysX Collider (**Is Trigger** = on), Script Canvas (`GemCollector`) | ☐ |

Select each entity in the **Entity Outliner** and scan its components in the **Entity Inspector** to confirm.

---

## Play the game

Press **Ctrl+G** to enter game mode.

| Action | What to expect |
|--------|---------------|
| **W / S** | Sphere moves forward/backward |
| **A / D** | Sphere moves left/right |
| Walk into a gem | Gem disappears |
| Collect all five gems | Level remains active (no win screen yet — that's a future step!) |

Press **Escape** to exit game mode at any time.

---

## Troubleshooting

### Player sinks through the ground

The ground is missing its physics. Select `Ground` → Entity Inspector and verify it has both **PhysX Static Rigid Body** and **PhysX Mesh Collider**, with the **PhysX Mesh** field in the collider set to `objects/primitive_cube.azmodel`.

### Player spins or tumbles

**Lock Angular X, Y, Z** is not fully checked on the `Player`'s **PhysX Rigid Body** component. Open it and check all three lock axes.

### Player doesn't move

Work through this checklist:
1. `Player` has an **Input** component with `PlayerInput.inputbindings` assigned.
1. `Player` has a **Script Canvas** component with `PlayerMovement.scriptcanvas` assigned.
1. In `PlayerMovement.scriptcanvas`, all four **Get Input Event Value** nodes have event names that exactly match those in `PlayerInput.inputbindings` (case-sensitive).
1. Asset Processor shows **0 jobs remaining**.

### Gems don't disappear on contact

1. On each gem's **PhysX Collider**, confirm **Is Trigger** is checked.
1. On the `Player` entity, confirm the **Tag** component contains `Player` (capital P).
1. In `GemCollector.scriptcanvas`, confirm the **Has Tag** node's Tag field is `Player`.
1. Each gem has a **Script Canvas** component pointing to `GemCollector.scriptcanvas`.

### Movement feels too fast or slow

Open `PlayerMovement.scriptcanvas` in Script Canvas Editor. Find the two **Math: Multiply** nodes. Change the constant `10.0` to a higher value (faster) or lower value (slower). Save and test.

---

## Build a standalone Game Launcher

So far you've been playing inside the editor. To create a standalone game executable:

1. In **Project Manager**, open your `GemCollector` project card.
1. From the dropdown, choose **Build Now** again. This produces the **Game Launcher** binary alongside the Editor binary.
1. The launcher is at: 

   {{< tabs name="launcher-path" >}}
   {{% tab name="Windows" %}}
   ```
   <project-folder>\build\windows\bin\profile\GemCollectorLauncher.exe
   ```
   {{% /tab %}}
   {{% tab name="Linux" %}}
   ```
   <project-folder>/build/linux/bin/profile/GemCollectorLauncher
   ```
   {{% /tab %}}
   {{< /tabs >}}

For bundling assets and packaging for distribution, see [Packaging](/docs/user-guide/packaging/).

---

## What to build next

You have a working game. Here are natural next steps:

| Goal | Where to learn |
|------|---------------|
| **Add a score counter and win screen** using LyShine UI | [Scripting Gameplay — UI](/docs/user-guide/scripting/) |
| **Add obstacles** — static physics objects to navigate around | [Create Wind Forces with PhysX](/docs/learning-guide/tutorials/physx/wind-provider/) |
| **Improve visuals** — custom materials and lighting | [Create a StandardPBR material](/docs/learning-guide/tutorials/rendering/create-standardpbr-material/) |
| **Build a full game with UI, states, and menus** | [Create Pong — video series](/docs/learning-guide/samples/follow-along/pong/) |
| **Understand the engine more deeply** | [Key Concepts](/docs/welcome-guide/key-concepts/) and the [User Guide](/docs/user-guide/) |

---

## Join the community

Got stuck, made something cool, or have questions? The O3DE community is active and welcoming.

[O3DE Discord](https://{{< links/o3de-discord >}}) — join the conversation to share tips and get help from other O3DE users.

You can also file issues and discuss features on [GitHub](https://github.com/o3de/o3de/discussions).

---

**Congratulations — you've built your first O3DE game!**
