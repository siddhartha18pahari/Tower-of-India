# Tower of India (Tower of Hanoi on DE1-SoC / CPUlator)

**Tower of India** is a digital recreation of the classic **Tower of Hanoi** puzzle designed to run on the **DE1-SoC** (ARM + FPGA) and the **CPUlator** simulator. The game uses memory-mapped I/O to drive **VGA graphics**, read a **PS/2 keyboard**, display stats on the **HEX seven-segment displays**, and run a **90-second hardware timer**.

---

## Gameplay

### Objective
Move the entire stack of disks from **Peg 1 (left)** to **Peg 3 (right)** before the timer reaches **0**.

### Rules
1. You may move **only one disk at a time**.
2. You may **never place a larger disk on top of a smaller disk**.
3. You must complete the puzzle within **90 seconds**.

### Minimum possible moves
- **Easy (3 disks):** 7 moves  
- **Medium (4 disks):** 15 moves  
- **Hard (5 disks):** 31 moves  

---

## Difficulty Modes
Choose a difficulty on the start screen using the PS/2 keyboard:

| Mode | Key | Disks |
|------|-----|-------|
| Easy | **E** | 3 |
| Medium | **M** | 4 |
| Hard | **H** | 5 |

---

## Controls (Aligned with the latest code)

### 1) Select which disk to move (DE1-SoC switches)
Disks are selected using the board **switches**:

- **SW0** = Disk 0 (smallest)
- **SW1** = Disk 1
- **SW2** = Disk 2
- **SW3** = Disk 3 (Medium/Hard only)
- **SW4** = Disk 4 (Hard only)

✅ The selected disk is **outlined** on the VGA display.

> Tip: Select *only one* switch at a time.  
> If multiple switches are on, the game will use the switch mask logic (not recommended for clean play).

### 2) Move the selected disk (PS/2 arrow keys)
Press an arrow key to send the selected disk to a target peg:

- **Left Arrow** → Peg 1 (Left)
- **Down Arrow** → Peg 2 (Center)
- **Right Arrow** → Peg 3 (Right)

Moves that break the Tower of Hanoi rules are rejected.

### 3) Restart
- Press **R** at any time on the end screen (and supported during play) to restart.

---

## Displays & UI

### VGA Output
The VGA scene includes:
- 3 vertical pegs + base platform
- Colored disks (size indicates disk order)
- A live HUD including:
  - **Move counter**
  - **Time remaining**
  - **Minimum score possible** for the chosen mode
  - Arrow hints under each peg (left / center / right)

### HEX Displays
- **HEX1–HEX0**: Move count (00–99)
- **HEX5–HEX4**: Time remaining in seconds (00–99)

> The game starts at **90 seconds** once a mode is selected.

### End Screen
At the end of the game, a summary screen shows:
- Your current move count (score)
- Best score achieved per difficulty (tracked in runtime)
- Win/Loss state (and optional audio)

---

## Running in CPUlator (Quick Start)

1. Open **CPUlator** (ARMv7 / DE1-SoC configuration).
2. Go to **File → Open** and select:
   - `Tower_of_India_optimized.c` (or your final `.c` file name)
3. Click **Compile and Load** (or press **F5**).
4. Press **Continue** (or **F3**) to start execution.
5. In the CPUlator UI:
   - Expand the VGA pixel buffer window if needed.
   - Under **PS/2 Keyboard**, click inside the field labeled **“type here:”** so keyboard input is captured.
6. Press **E**, **M**, or **H** to start.

### Restarting in CPUlator
- Use **Reload** in CPUlator, then click back into **“type here:”**, OR
- Use the in-game restart key **R** on the end screen.

---

## Running on DE1-SoC Hardware (Overview)

Minimum required peripherals:
- VGA output connected (monitor)
- PS/2 keyboard connected to the board
- Switches available for disk selection
- HEX displays active

> The program uses memory-mapped addresses for VGA pixel control, PS/2 input, HEX displays, timer, and audio.

---

## Implementation Notes (What the optimized code does)

### Performance / Efficiency upgrades (latest version)
- **Reduced duplicated logic** using shared helper functions for:
  - Column/rod selection
  - Disk index selection from switch masks
  - Move validation & column updates
- **More efficient rendering**
  - Draw each disk **once per frame**
  - Add an outline only for the selected disk
  - Faster screen clearing by writing directly to the framebuffer
- **Smoother audio streaming**
  - Writes audio samples in batches based on FIFO space instead of one-by-one blocking writes

### Game state model
- Disks are represented as structs holding:
  - `x, y` position, disk `size`, and `column`
- Each peg is represented by an integer array storing disk sizes (top-to-bottom logic)
- Legal move checking is performed using disk sizes and peg contents

---

## Files
This project can be kept extremely simple:

```text
/
├─ Tower_of_India_optimized.c
└─ README.md
