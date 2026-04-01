---
linkTitle: "5. Add Collectible Gems"
title: "Step 5: Add Collectible Gems"
description: >
  Create gem entities with PhysX trigger volumes and a Script Canvas graph
  that deactivates each gem when the player touches it.
toc: true
weight: 500
---

In this step you'll create a collectible gem using a **trigger volume** — a special PhysX shape that detects overlap without blocking movement. When the player enters the trigger, a Script Canvas script deactivates the gem (hiding and disabling it). You'll then duplicate the gem five times to populate the level.

**Estimated time:** ~35 minutes

## How trigger volumes work

A normal PhysX collider blocks other objects — the player can't pass through it. A **trigger collider** (with **Is Trigger** checked) does the opposite: it lets things pass through, but it fires an event when something enters or exits. That event is what the collection script listens for.

---

## Part A: Create one Gem entity

### 1. Create the entity

1. Right-click in the viewport → **Create Entity** → rename it `Gem_01`.
1. In the **Transform** component, set an initial position away from the player's start:

   | Property | Value |
   |----------|-------|
   | **Translate** X | `5` |
   | **Translate** Y | `1.5` |
   | **Translate** Z | `5` |

### 2. Add a visible mesh

1. **Add Component** → search `Mesh` → select **Mesh**.
1. Set **Mesh Asset** to `objects/primitive_cube.azmodel`.
1. In the **Transform** component, set **Scale** X, Y, Z to `0.5` each. This makes the gem noticeably smaller than the player.

### 3. Add a trigger collider

1. **Add Component** → search `PhysX Static Rigid Body` → select it.
1. **Add Component** → search `PhysX Collider` → select it.
1. In the **PhysX Collider** component, set:

   | Property | Value |
   |----------|-------|
   | **Shape** | Box |
   | **Dimensions** X, Y, Z | `0.5`, `0.5`, `0.5` |
   | **Is Trigger** | **Checked** |

   {{< important >}}
   **Check "Is Trigger"** — this is the most important setting on this component. Without it, the gem will block the player like a solid wall instead of registering a collection. The checkbox is in the **PhysX Collider** component, near the top.
   {{< /important >}}

---

## Part B: Create the collection Script Canvas graph

### 1. Create the asset

1. In the **Asset Browser**, right-click your project root → **New** → **Script Canvas**.
1. Name it `GemCollector` → **Enter**.
1. Double-click `GemCollector.scriptcanvas` to open Script Canvas Editor.

### 2. Add the trigger enter node

This node fires when another physics body enters the trigger volume.

1. Right-click → **Add Node** → search `On Trigger Enter` → select it from the **Physics** category.
1. This node has two outputs:
   - **Out** — execution pin (fires when triggered)
   - **Trigger Entity** — the entity that owns the trigger (the gem itself)
   - **Other Entity** — the entity that entered the trigger (hopefully the player)

### 3. Check for the Player tag

You only want to collect the gem if it's the *player* that entered — not another gem or a physics prop.

1. Right-click → **Add Node** → search `Has Tag` → select **Tag: Has Tag**.
1. Connect **On Trigger Enter → Other Entity** → **Tag: Has Tag → Source Entity**.
1. In the **Tag** field of the node, type `Player` (exactly as you set it in Step 3).
1. Connect **On Trigger Enter → Out** → **Tag: Has Tag → In**.

   The **Has Tag** node outputs:
   - **Result** — `true` if the entity has the tag, `false` otherwise

### 4. Branch on the result

1. Right-click → **Add Node** → search `Branch` → select **Logic: Branch** (or **Flow Control: Branch**).
1. Connect **Has Tag → Out** → **Branch → In**.
1. Connect **Has Tag → Result** → **Branch → Condition**.

   - The **True** execution pin fires if the entering entity is the player.
   - The **False** execution pin fires otherwise (do nothing).

### 5. Deactivate the gem

When the player is confirmed, deactivate the gem entity (this hides it and stops all its components).

1. Right-click → **Add Node** → search `Deactivate` → select **Entity: Deactivate**.
1. In the **Entity** input, right-click → **Set to Self**. This targets the gem entity that owns the script.
1. Connect **Branch → True** → **Entity: Deactivate → In**.

Your complete graph reads:

```
On Trigger Enter
  → Has Tag (check if Other Entity has tag "Player")
    → Branch (if true)
      → Entity: Deactivate (Self)
```

### 6. Save

**Ctrl+S** in Script Canvas Editor.

---

## Part C: Attach the script and test one gem

1. Switch back to O3DE Editor.
1. Select `Gem_01` in the Entity Outliner.
1. **Add Component** → search `Script Canvas` → select it.
1. Set **Script Canvas Asset** to `GemCollector.scriptcanvas`.
1. Save the level: **Ctrl+S**.

**Test:**
Press **Ctrl+G** and move the player into `Gem_01`. The gem cube should disappear on contact. Press **Escape**.

{{< caution >}}
If the gem doesn't disappear:
- Check **Is Trigger** is checked on the gem's PhysX Collider.
- Confirm the `Player` entity has the **Tag** component with value `Player`.
- Verify the Script Canvas component on the gem is pointing to `GemCollector.scriptcanvas`.
- Make sure Asset Processor has finished (0 jobs remaining).
{{< /caution >}}

---

## Part D: Duplicate and place the remaining gems

Once one gem works, duplicate it to create the rest. Duplicates inherit all components and settings.

1. Select `Gem_01` in the Entity Outliner.
1. Press **Ctrl+D** to duplicate. Rename the copy `Gem_02`.
1. In the **Transform** component, move it to a different position — for example: X=`-5`, Y=`1.5`, Z=`5`.

Repeat for `Gem_03` through `Gem_05`, placing each at a distinct location around the arena. Suggested positions:

| Entity | X | Y | Z |
|--------|---|---|---|
| Gem_01 | 5 | 1.5 | 5 |
| Gem_02 | -5 | 1.5 | 5 |
| Gem_03 | 0 | 1.5 | -7 |
| Gem_04 | 8 | 1.5 | -3 |
| Gem_05 | -8 | 1.5 | -3 |

{{< tip >}}
**Make gems visually distinct:** You can apply a custom material to give gems a different colour. Select a gem, open **Material → Assign New Material** in the Mesh component, and adjust the **Base Color** in the **Material Editor**. For a step-by-step walkthrough, see the [Create a StandardPBR material](/docs/learning-guide/tutorials/rendering/create-standardpbr-material/) tutorial.
{{< /tip >}}

---

## Save the level

**File → Save** (or **Ctrl+S**).

---

## Checkpoint

Press **Ctrl+G**. You should be able to:

1. Move the player with WASD
2. Collect each gem by touching it — it disappears on contact
3. Collect all five gems

Press **Escape** when done.

Your level's Entity Outliner should list: `Ground`, `Player`, and `Gem_01` through `Gem_05`.

---

**Next: [Step 6 — Test and Play →](../6-test-and-play)**
