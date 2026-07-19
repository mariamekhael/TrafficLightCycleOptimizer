# Traffic Light Cycle Optimizer

A desktop simulation built with **Python** and **Tkinter** that compares two traffic light scheduling strategies — **Greedy** and **Round-Robin** — across four intersection lanes, with both a live visual simulation and a numerical comparison table.

## Overview

The application simulates traffic arriving at a four-way intersection over 20 signal cycles. On each cycle, a random number of cars (between 5 and 15) arrives in each lane. A scheduling algorithm then selects one lane to serve, clearing up to 5 cars from it. The application supports:

- A **visual simulation** (canvas-based) showing car queues per lane, updating live, always driven by the Greedy strategy
- A **comparison table** (both in the console via `tabulate` and in a separate window via a `ttk.Treeview`) that runs both algorithms independently and compares their performance

## Requirements

- Python 3.7 or later
- `tkinter` (included with most standard Python installations)
- `tabulate`

Install the external dependency with:

```bash
pip install tabulate
```

## How to Run

```bash
python traffic_light_optimizer.py
```

A window will open titled **Traffic Light Cycle Optimizer**, containing:

1. A canvas showing four lanes: **Smouha**, **Mandara**, **Sidi Gaber**, and **El Awayed**
2. A **Show Comparison Table** button
3. A **Start Greedy Simulation** button

## Scheduling Algorithms

### Greedy
On every cycle, the lane with the largest number of waiting cars is selected and served first, clearing up to 5 cars from it.

### Round-Robin
Lanes are served in strict rotating order (lane `cycle % 4`), regardless of queue size, clearing up to 5 cars from the selected lane each cycle.

## Features

### Visual Simulation
Clicking **Start Greedy Simulation** starts a live, animated 20-cycle simulation on the canvas:
- Each lane is drawn as a bar whose height is proportional to its current number of waiting cars
- The lane currently being served is highlighted in green; all other lanes are shown in red
- The cycle number and current queue lengths are displayed above the canvas
- The simulation advances automatically every 750 milliseconds until cycle 20 is reached, at which point a "Simulation Finished" message is shown

### Comparison Table
Clicking **Show Comparison Table** runs both algorithms independently (each with the same random seed, so arrivals are identical for a fair comparison) over 20 cycles and reports, per lane:
- **Average waiting time** (cumulative waiting cars divided by total arrivals)
- **Total cars cleared** across all lanes
- **Maximum queue length** observed during the simulation

The results are printed to the console as a formatted grid table and also displayed in a separate popup window using a table widget.

## Reproducibility

The random seed is fixed to `42` before generating arrivals in both the algorithm comparison function and the visual simulation, so results are reproducible across runs, and the two algorithms are evaluated on the same sequence of arrivals for a fair comparison.

## Known Limitations

- The visual simulation (`visual_updates`) is hardcoded to run the Greedy strategy only; there is no button to visually simulate Round-Robin.
- The `lane_width` and `gap` module-level constants are unused inside `visual_updates`, which redefines its own local `lane_w` and `gap` values instead.
- The canvas resets its internal lane state only when `cycle_num == 1`; restarting the simulation before it finishes (by clicking the start button again mid-run) can lead to overlapping scheduled updates.
- There is no error handling for a division by zero in the average waiting time calculation (`waiting_time[i] / total_arrivals[i]`), though this is unlikely in practice since cars arrive every cycle.

## Project Structure

- Single-file script containing:
  - `Algorithm(Algorithm_name)` — runs a full 20-cycle simulation for either `"greedy"` or `"round-robin"` and returns average waiting times, total cars cleared, and the maximum queue length observed
  - `visual_updates(...)` — recursive, Tkinter-`after`-driven function that animates the Greedy simulation on the canvas
  - `show_comparison_table()` — runs both algorithms and displays results in the console and in a popup window
  - Main window setup (label, canvas, buttons) and the Tkinter main loop
