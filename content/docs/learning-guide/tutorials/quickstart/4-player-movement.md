---
linkTitle: "4. Script Player Movement"
title: "Step 4: Script Player Movement with Script Canvas"
description: >
  Use Script Canvas — O3DE's visual scripting tool — to make the player
  move with WASD by reading input events and applying velocity each frame.
toc: true
weight: 400
---

**Script Canvas** is O3DE's visual scripting editor. Instead of writing code, you connect nodes with wires to describe behavior. In this step you'll build a graph that responds to keyboard input events and moves the player sphere by setting its physics velocity every frame.

**Estimated time:** ~35 minutes

## How the movement graph works

The logic is straightforward:

```
Input events (Held / Released)
  → Update per-direction Number variables (1.0 while held, 0.0 on release)

Every frame (On Tick)
  → Read those variables
  → Calculate a velocity vector (X = strafe, Y = forward/back)
  → Set that velocity on the player's Rigid Body
```

The sphere moves as long as a key is held, and stops the moment you release it.

---

## Part A: Set up input bindings

O3DE uses a named-event system for input. Before Script Canvas can read key presses, you map physical keys to meaningful event names in an **Input Bindings** asset.

### 1. Create an Input Bindings asset

1. Go to the **Asset Editor** panel. If it's not already open, you can open it by clicking **Tools** → **Asset Editor**.
1. Click **File** → **New** → **Input Bindings**.
1. Save the file as `PlayerInput`. The file `PlayerInput.inputbindings` appears in the Asset Browser.

### 2. Map keys to event names

You'll create four events — one per movement direction.

For each event, choose `+` to create a new input event group, then fill in the following:

| Event name | Key to map |
|------------|-----------|
| `MoveForward` | `keyboard_key_alphanumeric_W` |
| `MoveBack` | `keyboard_key_alphanumeric_S` |
| `MoveLeft` | `keyboard_key_alphanumeric_A` |
| `MoveRight` | `keyboard_key_alphanumeric_D` |

For each event:
1. Set the **Event Name** field to the name in the table above.
1. Under **Event Generators**, choose **+** to add a generator.
1. Set the Input Device Type to **Keyboard**.
1. Set the **Input Name** to the key value from the table (for example, `keyboard_key_alphanumeric_W`).
1. Set **Event Value Multiplier** to `1.0` (returns 1.0 while held, 0.0 while released).

When done, save the asset: **File** → **Save** in the Asset Editor panel.

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

The **On Tick** node fires every frame and drives the velocity calculation.

1. Right-click in the canvas → **Add Node**.
1. Search for `On Tick` and select it from the **Timing** category.

   This node has an **On Tick** execution output pin that fires every frame, plus **Delta** and **Time** data outputs.

### 3. Create variables for each direction

Because **Input Handler** nodes are event-driven (they fire when a key changes state, not every frame), you need four Number variables to store whether each key is currently held.

1. Open the **Variables** panel in Script Canvas Editor (View → Variables, or the tab at the bottom).
1. Click `+` to add a variable, set the type to **Number**, name it `fwd`, and leave the default value as `0.0`.
1. Repeat for `back`, `left`, and `right`.

### 4. Read input values with Input Handler nodes

For each of the four directions, add an **Input Handler** node:

1. Right-click the canvas → **Add Node** → search for `Input Handler`.
1. Select it from the **Input** category.
1. In the node's **Event Name** field, type `MoveForward`.
1. Drag from the **Held → Out** execution pin, release on an empty area, and search for `set fwd`. Select it to place the setter node. Connect the **Value** data pin to the setter's input, if O3DE has not done so automatically.
1. Drag from the **Released → Out** execution pin, search for `set fwd`, and place a second setter. Type `0.0` into its value field.

Repeat for the remaining three directions:

| Input Handler Event Name | Variable |
|--------------------------|----------|
| `MoveBack` | `back` |
| `MoveLeft` | `left` |
| `MoveRight` | `right` |

{{< note >}}
**Input Handler** nodes are *graph entry points* — they don't connect to **On Tick**. They fire independently whenever the named input event changes state.
{{< /note >}}

### 5. Calculate the velocity components

You need two values:
- **Y velocity**: forward minus backward (`fwd` − `back`)
- **X velocity**: right minus left (`right` − `left`)

Add two **Subtract (-)** nodes:

**Y axis (forward/back):**
1. Right-click → search `Subtract` → select **Subtract (-)** from the **Math** category.
1. Right-click an empty area → search `get fwd` → place the getter. Connect its output to the first value input.
1. Right-click an empty area → search `get back` → place the getter. Connect its output to the second value input.
1. Connect the **On Tick → On Tick** execution pin to this node's **In** pin.

**X axis (left/right):**
1. Add a second **Subtract (-)** node.
1. Place `get right` and `get left` getters; connect them to the first and second value inputs respectively.
1. Connect the **On Tick → On Tick** execution pin to this node's **In** pin as well.

### 6. Scale by movement speed

Multiply each component by a speed value. `10.0` is a good starting point — you can tune it later.

**Y axis:**
1. Right-click → search `Multiply` → select **Multiply (*)** from the **Math** category.
1. Connect **Y Subtract → Out** execution → **Y Multiply In**.
1. Connect **Y Subtract → Result** data to the first value input. Type `10.0` into the second value input.

**X axis:**
1. Add a second **Multiply (*)** node.
1. Connect **X Subtract → Out** execution → **X Multiply In**.
1. Connect **X Subtract → Result** data to the first value input. Type `10.0` into the second value input.

{{< tip >}}
To set a constant in a node's input field, click the input pin's data field directly and type the number. You don't need a separate constant node.
{{< /tip >}}

### 7. Build the velocity Vector3

Combine the X and Y components into a single 3D vector. Z stays at `0.0` — you don't want to override the vertical velocity, which PhysX uses for gravity.

1. Right-click → **Add Node** → search `From Values` → select **Vector3: From Values** from the **Math/Vector3** category.
1. Connect **X Multiply → Result** → **X**.
1. Connect **Y Multiply → Result** → **Y**.
1. Leave **Z** as `0.0`.

### 8. Apply velocity to the Rigid Body

1. Right-click → **Add Node** → search `Set Linear Velocity` → select it from the **Rigid Body** category.
1. In the **Entity** input, right-click and choose **Set to Self** — this targets the entity that owns the script.
1. Connect **From Values → Vector3** → **Linear Velocity**.
1. Connect **Y Multiply → Out** execution pin → **Set Linear Velocity → In**.

   Your execution chains read:
   ```
   On Tick ─┬─→ Y Subtract (-) → Y Multiply (*) ─→ Set Linear Velocity
            └─→ X Subtract (-) → X Multiply (*)
   ```
   Data flows from the variable getters into the math nodes, then into **From Values**, then into **Set Linear Velocity**.

### 9. Save the graph

**File → Save** in the Script Canvas Editor.

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
4. Confirm the event names in the **Input Handler** nodes exactly match those in `PlayerInput.inputbindings` — including capitalisation (`MoveForward` ≠ `moveforward`).
{{< /caution >}}

---

## Summary of nodes

| Node | Category | Purpose |
|------|----------|---------|
| On Tick | Timing | Fires every frame; drives the velocity chain |
| Input Handler | Input | Sets a variable to 1.0 while held, 0.0 on release |
| Number variable getters/setters | Variables | Store and read per-direction key state |
| Subtract (-) | Math | Calculates directional axis value |
| Multiply (*) | Math | Scales by movement speed |
| Vector3: From Values | Math/Vector3 | Combines X, Y, Z into a velocity vector |
| Rigid Body: Set Linear Velocity | Physics | Applies velocity to the entity |

---

**Next: [Step 5 — Add Collectible Gems →](../5-collectibles)**
