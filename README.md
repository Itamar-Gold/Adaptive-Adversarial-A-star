# Adaptive Adversarial A* (AAA*) for Security Operations

## Overview
This project provides a procedural scenario generation framework designed to stress-test a deployment configurations of safety-critical robotic security systems. It operates as a deterministic Automated Red Team, exposing vulnerabilities in geometric and heuristic coverage strategies (e.g., Set Covering Problem solutions).

The framework introduces the Adaptive Adversarial A* (AAA*) algorithm. Unlike stochastic search testing (such as Monte Carlo or RRT), AAA* uses a spatial memory map that tracks successful capture events. It projects these past failures as dynamic, repulsive penalty fields onto a cost map. This forces the Red Team drones to systematically exploit the narrow topological corridors and temporal blind spots of the Blue Team's security architecture.

## Key Entities
1. **Blue Team (Security System):**
   - **Launch Pads:** Static stations that hold and deploy active response units (drones).
   - **Cameras:** Static surveillance cameras with defined fields of view.
   - **Blue Drones:** Active response drones launched from the pads that use Proportional Navigation (PN) to capture intruders.

2. **Red Team (Adversary):**
   - **Red Drones:** Adversarial units that attempt to navigate from their spawn points to designated Regions of Interest (RoI/Targets) while evading the Blue Team.
   - **AAA* Algorithm:** The pathfinding brain utilized by the red drones to learn from previous capture events and calculate optimal evasion trajectories.

## Curriculum of Difficulty
The simulation progresses through 6 levels of difficulty designed to test system adaptability, transitioning from predictable approaches to a dynamic Stackelberg Game where the Red Team possesses complete historical memory.
- **Levels 1-3:** Non-adaptive Red Team (Standard A* paths). Number of start points and target zones scale up.
- **Level 4:** Partial adaptability (Memory resets every 10 drones).
- **Levels 5-6:** Full adaptability (Memory accumulates indefinitely; Worst-Case Scenario).

## Project Structure
* `config.yaml`
  - Centralized configuration variables for the simulation physics, algorithms, and evaluation settings.
* `data/`
  - `roi.xlsx`: The Region of Interest dataset representing critical infrastructure to protect.
  - `placement.xlsx`: Output Excel file for generated Blue Team deployment solutions.
* `src/`
  - **`algorithms/`**: Contains the pathfinding algorithms (`aaa_star.py`) and the Blue Team's placement heuristics (`greedy_scp.py`).
  - **`simulation/`**: Core logic including the physics engine (`physics.py`) and simulation runner (`engine.py`).
  - **`utils/`**: Helper files for geometry and calculations (`geometry.py`).
  - **`data/`**: Data loading and pre-processing modules (`prep.py`).
* `scripts/`
  - `evaluate.py`: The main entry point to evaluate your Blue Team configurations across all difficulty levels.
  - `plot_heatmaps.py`: Creates color-coded Performance Matrices to visualize system performance.
  - `plot_res_by_levels.py`: Bar plots comparing algorithms at a single difficulty level for increasing swarm size.
  - `plot_res_by_num_drones.py`: Generates comparisons of algorithms across all levels for a specific swarm size.
* `results/`
  - JSON performance data and exported graphical analysis plots.

## Setup & Installation
1. Ensure you have Python 3.10+ installed.
2. Clone this repository.
3. Install the necessary dependencies by running:
   ```bash
   pip install -r requirements.txt
   ```

## Usage Workflow

### 1. Configure the Environment
All simulation parameters (timesteps, drone speeds, camera FOV, pure pursuit lookahead, AAA* stretch factors) are centralized. 
* Open `config.yaml` and adjust the variables to define the physical capabilities of your Blue Team and Red Team. 

### 2. Generate Blue Team Deployments
To solve the Set Covering Problem and deploy your cameras and launch pads based on the Region of Interest:
```bash
python src/algorithms/greedy_scp.py
```
*This will read `data/roi.xlsx`, calculate optimal placements, and output the results to `data/placement.xlsx`.*

### 3. Run the Automated Red Team Evaluation
To stress-test your deployment against the AAA* algorithm across all 6 curricula of difficulty and 5 different swarm sizes (30 to 110 drones):
```bash
python scripts/evaluate.py
```
*The simulation will run headlessly. Detailed statistics (Capture Rate, Simulation Time, configurations) will be saved as JSON files in auto-generated folders inside the `results/` directory.*

### 4. Analyze and Visualize the Results
Once the evaluation is complete, use the analysis scripts to generate publication-ready plots. These scripts automatically read the JSON outputs from `results/`.

**To generate bar chart comparisons across swarm sizes and difficulty levels:**
```bash
python scripts/plot_res_by_levels.py
python scripts/plot_res_by_num_drones.py
```

**To generate Performance Matrix Heatmaps:**
```bash
python scripts/plot_heatmaps.py
```
*All generated graphs are saved directly to `results/analysis_plots/`.*
