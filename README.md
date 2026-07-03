# Specialized Fieldwork Tree Network Simulator

## Overview

This project is an asynchronous, browser-based simulation engine designed to model the logistics, time-tracking, and pathfinding of a forestry or arboriculture fieldwork crew. Using HTML5 Canvas and vanilla JavaScript, it visually maps out the process of a specialized crew measuring tree metrics (Height, Crown Spread, and Trunk DBH) across a spatial network.

By physically scaling walking distances and tying them to real-world velocity variables, this tool serves as a sandbox for optimizing field layouts, node placements, and workforce allocations.

---

## Educational Lab Worksheet
This simulator includes a companion **Student Lab Worksheet** designed for high school computer science, geometry, or environmental science students. By stepping into the role of a field operations manager, students can use the simulator as an interactive sandbox to test hypotheses about routing, labor, and geometry. 

**[Click Here to Make a Copy of the Student Lab Worksheet](https://docs.google.com/document/d/1j-dpoMazgDfuubcuNe6mywUsZYxDWoBgSY5x5-vGpug/copy)**

The lab guides students through hands-on experiments broken down into five core concepts:
* **Establishing a Baseline:** Running control tests to understand baseline operational times.
* **Workforce Dynamics & Bottlenecks:** Learning why doubling the workforce doesn't always halve the time if a single slow task holds up the entire system.
* **Spatial Topology & Algorithm Flaws:** Observing the "Greedy Algorithm" in action to understand the difference between local optimization and global optimization (introducing the classic Traveling Salesperson Problem).
* **Physical Tree Dimensions:** Understanding how the physical height of an object changes the geometric footprint of the required fieldwork.
* **Angle of Approach & Directionality:** Strategically rotating layout nodes to minimize walking distances based on the crew's starting origin.

*(Note: The worksheet is designed to be completed alongside the simulator and encourages students to reset, manipulate nodes, and observe agent behavior to answer analytical questions).*

## Key Features

* **Physically Scaled Time & Distance:** The simulation operates on a strict scale where **2.5 pixels = 1 meter**. Workers move at a realistic, mathematically calculated speed of **80 meters per minute**.
* **Tool Specialization:** The crew is split evenly into two distinct roles:
* **Tape Specialists:** Exclusively target tree trunks to simulate wrapping a Diameter at Breast Height (DBH) measuring tape.
* **Wheel Specialists:** Exclusively target offset layout nodes to walk the baseline height calculation and pace out horizontal canopy spread crosshairs.


* **Interactive Drag-and-Drop Mapping:** Users can click to spawn trees, drag the green central trunk nodes to reposition them, and drag the yellow layout nodes to optimize the Wheel Specialists' walking distances.
* **Real-Time Telemetry Dashboard:** A live UI tracks active operations, crew status, physical scaling constraints, and a true-to-life elapsed operational stopwatch.

---

## How to Run

This simulation has zero external dependencies other than a CDN link to Tailwind CSS for styling.

1. Save the code as an `.html` file (e.g., `simulator.html`).
2. Double-click the file to open it in any modern web browser (Chrome, Firefox, Safari, Edge).

### Controls

* **Click on Canvas:** Spawns a new tree at the cursor's location.
* **Click & Drag (Green Node):** Moves the entire tree.
* **Click & Drag (Yellow Node):** Changes the distance and angle of the height-calculation baseline.
* **UI Panel Inputs:** Adjust tree spawn templates (Height/Spread) and crew composition (Total Fleet Size).
* **Scatter 10 Test Trees:** Instantly generates a random layout for quick testing.

---

## How it Works (Under the Hood)

The engine runs on a standard "game loop" architecture using `requestAnimationFrame`.

* **The Entities:** * `Tree` objects store spatial coordinates, physical dimensions, and track completion states.
* `Worker` objects act as autonomous agents that navigate the canvas to complete sub-tasks.


* **The Tick Loop:** * Every frame, the `tick()` function updates the position of all moving workers based on the calculated X/Y vector toward their target.
* Once a worker arrives, they transition into a measuring state and physically walk the layout paths assigned to their sub-task.


* **The Global Clock:** Time only progresses during the active `tick()` cycle. It increments by **0.02 minutes** per frame, translating the pixel-based movement on the screen into true elapsed operational time.

---

## Agent Pathfinding & Decision Logic

The workers act as autonomous agents using a **Greedy Algorithm** to navigate the site. Whenever a worker finishes a task and becomes idle, they evaluate their next move based entirely on the most immediate, short-term benefit.

### The Decision Sequence

1. **Filter by Completion:** The worker ignores any tree that has already been fully mapped.
2. **Filter by Specialty & Claim:** The worker checks the remaining trees to see if their specific sub-task (Tape or Wheel) is incomplete and unclaimed by another worker of the same specialty.
3. **Calculate Euclidean Distance:** The worker calculates the straight-line distance from their current pixel coordinates to the required node for every valid tree.
4. **Make the Greedy Choice:** The worker locks onto the target with the absolute shortest distance, reserves it, and begins walking.

### The Flaw in the Logic

Because greedy algorithms only look one step ahead, the agents lack "global awareness." They do not plan macro-routes for the whole map.

> **How this impacts efficiency:**
> If a worker finishes a tree and there is a single, isolated tree 50 pixels to their left, and a dense cluster of five trees 52 pixels to their right, the agent will choose the isolated tree. They will walk 50 pixels away from the cluster just because it was slightly closer at that exact moment. This often results in crossing paths, backtracking, and suboptimal total completion times, perfectly demonstrating the difference between *local optimization* (picking the closest next step) and *global optimization* (planning the most efficient total route, similar to the Traveling Salesperson Problem).

---

## Assumptions & Constraints

To translate physical field labor into a 2D digital simulation, the engine makes the following baseline assumptions:

* **Unobstructed Pathfinding:** Workers walk in perfectly straight lines (Euclidean distance) between nodes. The simulation assumes flat terrain with no physical obstacles.
* **Constant Velocity:** Workers maintain a perfectly constant walking speed of **80 meters per minute** (**4.8 km/h**) without fatigue, acceleration, or deceleration phases.
* **Zero Tool Handoff:** Workers are permanently assigned their specialized tools upon initialization. They do not trade tools or switch roles mid-simulation to clear bottlenecks.
* **Strict Task Completion:** A tree is only marked "Done" once both a Tape Specialist and a Wheel Specialist have fully completed their respective physical layout walks for that specific tree.
