---
linkTitle: "4. Script Player Movement"
title: "Step 4: Script Player Movement with Script Canvas"
description: >
  Use Script Canvas — O3DE's visual scripting tool — to make the player
  move with WASD by reading input events and applying velocity each frame.
toc: true
weight: 400
---

**Script Canvas** is O3DE's visual scripting editor. Instead of writing code, you connect nodes with wires to describe behavior. In this step you'll build a graph that reads keyboard input every frame and moves the player sphere by setting its physics velocity.

**Estimated time:** ~35 minutes

## How the movement graph works

The logic is straightforward:

```
Every frame (On Tick)
  → Read W/A/S/D input values
  → Calculate a velocity vector (X = strafe, Z = forward/back)
  → Set that velocity on the player's PhysX Rigid Body
```

The sphere moves as long as a key is held, and stops the moment you release it.

---

## Part A: Set up input bindings

O3DE uses a named-event system for input. Before Script Canvas can read key presses, you map physical keys to meaningful event names in an **Input Bindings** asset.

### 1. Create an Input Bindings asset

1. In the **Asset Browser** panel, navigate to your project's root folder.
1. Right-click the folder → **New** → **Input Bindings**.
1. Name the file `PlayerInput` and press **Enter**. The file `PlayerInput.inputbindings` appears in the Asset Browser.
1. Double-click `PlayerInput.inputbindings` to open the **Input Bindings editor**.

### 2. Map keys to event names

You'll create four events — one per movement direction.

For each event, choose **Add (+)** to create a new input event group, then fill in the following:

| Event name | Key to map |
|------------|-----------|
| `MoveForward` | `keyboard_key_alphanumeric_W` |
| `MoveBack` | `keyboard_key_alphanumeric_S` |
| `MoveLeft` | `keyboard_key_alphanumeric_A` |
| `MoveRight` | `keyboard_key_alphanumeric_D` |

For each event:
1. Set the **Event Name** field to the name in the table above.
1. Under **Event Generators**, choose **+** to add a generator.
1. Set the generator type to **Input Device Keyboard**.
1. Set the **Input Name** to the key value from the table (for example, `keyboard_key_alphanumeric_W`).
1. Set **Event Value Range** to `1.0` (returns 1.0 while held, 0.0 while released).

When done, save the asset: **Ctrl+S**.

### 3. Add an Input component to the Player entity

The Input component activates the bindings for the entity that owns it.

1. Select the `Player` entity in the Entity Outliner.
1. **Add Component** → search for `Input` → select **Input**.
1. In the **Input** component, set the **Input to event bindings** field to `PlayerInput.inputbindings`.

---

## Part B: Build the movement Script Canvas graph

### 1. Create a Script Canvas asset

1. In the **Asset Browser**, right-click your project root → **New** → **Script Canvas**.
1. Name it `PlayerMovement` → press **Enter**.
1. Double-click `PlayerMovement.scriptcanvas` to open **Script Canvas Editor**.

### 2. Add the On Tick node

The **On Tick** node fires every frame, which is where you'll poll input and update velocity.

1. Right-click in the canvas → **Add Node**.
1. Search for `On Tick` and select it from the **Timing** category.

   This node has a single output execution pin (**Out**) that fires every frame.

### 3. Read input values

For each of the four directions, add an **Input: Get Input Event Value** node:

1. Right-click the canvas → **Add Node** → search for `Get Input Event Value`.
1. Select the node from the **Input** category.
1. In the node's **Event Name** field, type `MoveForward`.
1. Connect the **On Tick → Out** pin to the **In** pin of this node.

Repeat to add three more nodes with event names `MoveBack`, `MoveLeft`, `MoveRight`. Connect all four **In** pins to the same **On Tick → Out** pin.

{{< note >}}
One execution wire can fan out to multiple nodes. All four input nodes will execute in sequence whenever On Tick fires.
{{< /note >}}

The **Value** output pin of each node returns:
- `1.0` while the key is held
- `0.0` when the key is released

### 4. Calculate the velocity components

You need two values:
- **Z velocity**: forward minus backward (`MoveForward` − `MoveBack`)
- **X velocity**: right minus left (`MoveRight` − `MoveLeft`)

Add two **Math: Subtract** nodes:

**Z axis (forward/back):**
1. Right-click → **Add Node** → search `Subtract` → select from **Math**.
1. Connect `MoveForward → Value` to the first input (**Value 1**).
1. Connect `MoveBack → Value` to the second input (**Value 2**).

**X axis (left/right):**
1. Add a second **Math: Subtract** node.
1. Connect `MoveRight → Value` to **Value 1**.
1. Connect `MoveLeft → Value` to **Value 2**.

### 5. Scale by movement speed

Multiply each component by a speed value. `10.0` is a good starting point — you can tune it later.

Add two **Math: Multiply** nodes:

1. Connect **Z Subtract → Result** → **Multiply Value 1** → set **Value 2** to `10.0`.
1. Connect **X Subtract → Result** → **Multiply Value 1** → set **Value 2** to `10.0`.

{{< tip >}}
To set a constant in a node's input field, click the input pin's data field directly and type the number. You don't need a separate constant node.
{{< /tip >}}

### 6. Build the velocity Vector3

Combine the X and Z components into a single 3D vector. Y stays at `0.0` — you don't want to override the vertical velocity, which PhysX uses for gravity.

1. Right-click → **Add Node** → search `Create Vector3` → select it.
1. Connect **X Multiply → Result** → **X**.
1. Set **Y** to `0.0` (leave the field blank or type 0 — the default).
1. Connect **Z Multiply → Result** → **Z**.

### 7. Apply velocity to the PhysX Rigid Body

1. Right-click → **Add Node** → search `Set Linear Velocity` → select **PhysX Rigid Body: Set Linear Velocity**.
1. In the **Entity** input of this node, right-click and choose **Set to Self** (or type `Self`) — this targets the entity that owns the script.
1. Connect **Create Vector3 → Vector3** → **Linear Velocity**.
1. Connect the execution chain: the last input node's **Out** pin → `Set Linear Velocity → In`.

   Your final execution chain should read:
   ```
   On Tick → MoveForward → MoveBack → MoveLeft → MoveRight → Set Linear Velocity
   ```

### 8. Save the graph

**Ctrl+S** in the Script Canvas Editor.

---

## Part C: Attach the script to the Player

1. Switch back to O3DE Editor.
1. Select the `Player` entity in the Entity Outliner.
1. **Add Component** → search for `Script Canvas` → select it.
1. In the **Script Canvas** component, set the **Script Canvas Asset** field to `PlayerMovement.scriptcanvas`.

---

## Test it

Press **Ctrl+G** to enter game mode.

- **W** should move the sphere in one horizontal direction
- **S** should move it in the opposite direction
- **A** and **D** should move it left and right
- Releasing all keys should stop the sphere

Press **Escape** to exit game mode.

{{< caution >}}
**If the sphere doesn't move**, check these in order:
1. The `Player` entity has both an **Input** component (pointing to `PlayerInput.inputbindings`) and a **Script Canvas** component (pointing to `PlayerMovement.scriptcanvas`).
2. Asset Processor has finished processing — look for **0 jobs remaining** in the system tray.
3. In Script Canvas Editor, verify the execution chain connects all nodes. Broken wires (disconnected pins) appear greyed out.
4. Confirm the event names in the Script Canvas nodes exactly match those in `PlayerInput.inputbindings` — including capitalisation (`MoveForward` ≠ `moveforward`).
{{< /caution >}}

---

## Summary of nodes

| Node | Category | Purpose |
|------|----------|---------|
| On Tick | Timing | Fires every frame |
| Get Input Event Value | Input | Returns 1.0 if the named event is active |
| Math: Subtract | Math | Calculates directional axis value |
| Math: Multiply | Math | Scales by movement speed |
| Create Vector3 | Math | Combines X, Y, Z into a vector |
| PhysX Rigid Body: Set Linear Velocity | Physics | Applies velocity to the entity |

---

**Next: [Step 5 — Add Collectible Gems →](../5-collectibles)**
