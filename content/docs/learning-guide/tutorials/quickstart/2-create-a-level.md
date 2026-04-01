---
linkTitle: "2. Create a Level"
title: "Step 2: Create a Level"
description: >
  Create a new level in O3DE Editor and add a solid ground plane
  that the player can stand on.
toc: true
weight: 200
---

A **level** is O3DE's name for a scene — it holds all the entities, lighting, and environment settings for one area of your game. In this step you'll create a fresh level and add a ground plane, so the player has somewhere to stand.

**Estimated time:** ~20 minutes

## 1. Create a new level

1. In O3DE Editor, go to **File → New Level** and select the Basic Level.

1. Name the level `GemCollectorLevel` and choose **OK**.

   O3DE creates the level file inside your project's `Levels/` folder and opens it in the editor viewport.

1. Save immediately: **File → Save** (or **Ctrl+S**).

   {{< tip >}}
   Save frequently as you work. O3DE does not auto-save levels.
   {{< /tip >}}

---

## 2. Understand the viewport

Before adding anything, get comfortable with moving around:

| Action | Control |
|--------|---------|
| Fly-cam (move and look) | Hold **Right Mouse Button**, then use **W A S D** |
| Pan | Hold **Middle Mouse Button** and drag |
| Orbit around a selection | Hold **Alt**, then drag with the left mouse button |
| Focus on a selected entity | Press **Z** |
| Zoom in/out | Scroll wheel |

---

## 3. Create a ground entity

You'll build the ground from a primitive cube, scaled to create a flat arena.

**Create the entity:**

1. Right-click anywhere in the viewport (or in the **Entity Outliner** panel) and choose **Create Entity**.
1. In the **Entity Outliner**, double-click the new entity and rename it `Ground`.

**Add a visible mesh:**

1. With `Ground` selected, go to the **Entity Inspector** panel (right side) and choose **Add Component**.
1. Search for `Mesh` and select **Mesh**.
1. In the **Mesh** component, click the folder icon next to **Mesh Asset** and browse to:

   ```
   Objects/_Primitives/_Box_1x1.fbx
   ```

   This is a built-in box mesh provided by the **PrimitiveAssets** gem. If you don't see it, make sure the **Asset Processor** has finished its initial scan.

**Scale and position it:**

1. With `Ground` still selected, find the **Transform** component at the top of the Entity Inspector.
1. Click `Add non-uniform scale`, then set the following values:

   | Property | Value |
   |----------|-------|
   | **Translate** X, Y, Z | `0`, `0`, `0` |
   | **Scale** X, Y, Z | `50`, `50`, `1` |

   This creates a flat arena 50 units wide and 50 units deep, 1 unit tall.

**Add physics:**

Without a PhysX collider, the player sphere will fall straight through the ground. Add two components to make it solid:

1. Choose **Add Component** → search for `PhysX Static Rigid Body` → select it.
1. Choose **Add Component** → search for `PhysX Mesh Collider` → select it.
1. In the **PhysX Mesh Collider** component, set **PhysX Mesh** to the same asset: `Objects/_Primitives/_Box_1x1.fbx`.

   {{< note >}}
   **PhysX Static Rigid Body** tells the physics engine this object never moves. **PhysX Mesh Collider** gives it a physical shape. Both are required for a solid, stationary surface.
   {{< /note >}}

---

## 4. Adjust your view

Press **Z** with the `Ground` entity selected to frame it in view. Use the fly-cam controls to position your camera above the ground looking down — this gives you a good working view for placing objects in later steps.

---

## 5. Save the level

**File → Save** (or **Ctrl+S**).

Asset Processor will notice the level file and process it in the background. You can see its progress in the system tray icon.

---

## Checkpoint

Your level should now contain one entity, `Ground`, visible in both the viewport (a large flat cube) and the Entity Outliner.

Press **Ctrl+G** to enter **Game Mode** — the view should stay still since there's nothing interactive yet. Press **Escape** to exit game mode.

{{< note >}}
If the ground mesh doesn't appear, verify that the `Mesh` component's **Mesh Asset** field is set and that Asset Processor has finished processing (0 jobs remaining).
{{< /note >}}

---

**Next: [Step 3 — Create the Player Entity →](../3-player-entity)**
