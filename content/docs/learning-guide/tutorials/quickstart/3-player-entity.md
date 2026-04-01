---
linkTitle: "3. Create the Player Entity"
title: "Step 3: Create the Player Entity"
description: >
  Build the player: a sphere with a visible mesh, a physics rigid body,
  and a Tag component so other scripts can identify it.
toc: true
weight: 300
---

In O3DE, every game object — a character, a platform, a gem — is an **entity** made up of **components**. Each component adds one piece of functionality. In this step you'll build the player entity by stacking several components on a single entity.

**Estimated time:** ~20 minutes

## 1. Create the Player entity

1. Right-click in the viewport (or in the **Entity Outliner**) and choose **Create Entity**.
1. Rename the new entity `Player`.
1. In the **Transform** component (always present at the top of the Entity Inspector), set the position so the player starts above the ground plane:

   | Property | Value |
   |----------|-------|
   | **Translate** X | `0` |
   | **Translate** Y | `0` |
   | **Translate** Z | `1.5` |

   {{< note >}}
   In O3DE's coordinate system, **Z is the up axis**. Translating to Z=1.5 places the center of the sphere just above the ground surface (which has its top face at Y=0.5 after scaling in the previous step).
   {{< /note >}}

---

## 2. Add a visible mesh

1. With `Player` selected, choose **Add Component** → search for `Mesh` → select **Mesh**.
1. In the **Mesh** component, click the folder icon and browse to:

   ```
   objects/primitive_sphere.azmodel
   ```

   The sphere appears in the viewport at the player's position.

---

## 3. Add a physics rigid body

A **PhysX Rigid Body** makes the entity respond to gravity and collisions. Without it, the sphere would stay frozen in space.

1. Choose **Add Component** → search for `PhysX Dynamic Rigid Body` → select it.
1. In the **PhysX Dynamic Rigid Body** component, configure the following settings to prevent the sphere from rolling and tumbling:

   **Angular Axis Locking**:

   | Axis | Setting |
   |------|---------|
   | **Lock Angular X** | Checked |
   | **Lock Angular Y** | Checked |
   | **Lock Angular Z** | Checked |

   {{< important >}}
   Locking all angular axes is **required**. Without it, the sphere spins and tumbles whenever it touches anything, making it impossible to control. Gravity remains enabled — the sphere will fall onto the ground plane and stay there through normal collision.
   {{< /important >}}

---

## 4. Add a physics collider

A PhysX Collider defines the physical shape the physics engine uses for collision detection. It doesn't have to match the visual mesh exactly — for a sphere, use a sphere shape.

1. Choose **Add Component** → search for `PhysX Shape Collider` → select it.
1. In the **PhysX Shape Collider** component, click `Add Required Component` and select `Sphere Shape`.
1. In the **Sphere Shape** component, ensure:

   | Property | Value |
   |----------|-------|
   | **Radius** | `0.5` |
   | **Translation Offset** X | 0 |
   | **Translation Offset** Y | 0 |
   | **Translation Offset** Z | 0.5 |

   A radius of `0.5` matches the unit sphere mesh's size, while a translation offset of `0.5` aligns it with the entity's mesh.

---

## 5. Add a Tag

Tags let scripts identify entities without hardcoding names. The gem collection script (Step 5) uses a tag to check whether it's the player entering the trigger volume.

1. Choose **Add Component** → search for `Tag` → select it.
1. In the **Tag** component, choose the `+` button and type `Player`.

---

## 6. Save

**File → Save** (or **Ctrl+S**).

---

## Checkpoint

Select the `Player` entity and press **Ctrl+G** to enter game mode. The sphere should:

- Fall and land on the ground plane
- Sit still after landing

Press **Escape** to exit game mode.

{{< note >}}
If the sphere falls through the ground, the `Ground` entity is missing its **PhysX Mesh Collider** — go back to [Step 2](../2-create-a-level) and verify both PhysX components are on the Ground entity.

If the sphere keeps bouncing or sinking, verify that **Lock Angular X/Y/Z** are all checked in the PhysX Rigid Body component.
{{< /note >}}

Your entity setup should now look like this in the Entity Inspector:

| Component | Purpose |
|-----------|---------|
| Transform | Position, rotation, scale |
| Mesh | Visual sphere |
| PhysX Rigid Body | Gravity + physics simulation |
| PhysX Collider | Collision shape (sphere, radius 0.5) |
| Tag | Identifies this entity as `Player` |

---

**Next: [Step 4 — Script Player Movement →](../4-player-movement)**
