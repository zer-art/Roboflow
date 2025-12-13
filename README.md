# Roboflow AI Simulation

This project is designed to simulate and test the effectiveness of Roboflow AI metrics. The goal is to perform comprehensive testing, compare results with the current situation, and analyze performance across various scenarios.

## Project Structure

The project is organized as follows:

- **src/**: General source code utilities.
- **simulation/**: The core "Digital Twin" environment and RL agents.
- **knowledge_base/**: Static database for G&SR rules, network topology, and train specs.
- **constraints/**: MILP/OR logic for enforcing safety and validity constraints.
- **models/**: Directory for storing trained RL model checkpoints.
- **config/**: Configuration files for simulation parameters and environment settings.
- **data/**: Directory for storing datasets.
  - `raw/`: Original, immutable data.
  - `processed/`: Data that has been cleaned and transformed for the simulation.
- **notebooks/**: Jupyter notebooks for exploratory data analysis (EDA), prototyping, and visualization of results.
- **tests/**: Unit and integration tests to ensure the reliability of the simulation code.
- **results/**: Output directory for simulation logs, generated metrics, and comparison reports.

## 5. ML Strategy & Tech Stack
For a detailed breakdown of why we use **Reinforcement Learning (PPO)** instead of LLMs (LangChain), and how we handle **G&SR Rules**, please see [ML_STRATEGY.md](ML_STRATEGY.md).

## Getting Started

### Prerequisites

- Python 3.8+
- [List other dependencies here, e.g., NumPy, Pandas, SimPy]

### Installation

1. Clone the repository:
   ```bash
   git clone <repository_url>
   cd <repository_directories>
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

## Usage

1. **Configuration**: Modify the configuration files in `config/` to set up your simulation parameters.
2. **Run Simulation**: Execute the main simulation script (to be implemented in `src/`).
3. **Analyze Results**: Check the `results/` folder for logs and metric reports. Use the notebooks in `notebooks/` for detailed analysis.

## Metrics to be Tested

- Effectiveness metrics
- Performance comparisons (Current vs. Proposed)
- [Add specific metrics here]

## Reference Documents

- `RailFlow-AI-2.pdf`: Reference document for AI capabilities and metrics (located in root).

## Contributing

Please read the contributing guidelines before submitting pull requests.

## License

[License Name]
# RailFlow AI Architecture & Implementation Strategy

## 1. Architecture Evaluation
**Verdict:** The proposed architecture (Hybrid Intelligence: MILP + RL) is **excellent** and represents the state-of-the-art for railway scheduling and dispatching systems.

*   **Why it works:**
    *   **Reinforcement Learning (RL):** Excellent for pattern recognition and speed. It learns "heuristics" from historical data to make fast decisions (e.g., "fast passenger train usually passes freight here").
    *   **Operations Research (MILP):** Essential for **safety and hard constraints**. RL can sometimes hallucinate or suggest unsafe moves. The MILP layer acts as a "safety shield" ensuring no G&SR rules are violated.
    *   **Constraint Modeling:** Absolutely critical. The exact track layout, signal distances, and gradient rules must be modeled physically.

## 2. Refined Workflow
The user proposed: *Data -> Fine-tuning -> Rules Database*.
**Recommended Refined Workflow:**

1.  **Data Ingestion (Historical)**: Load 3-6 months of "Control Charts" (train movements). Clean anomalies.
2.  **Digital Twin Construction (The Simulation)**:
    *   This is the most critical step. You must build a simulation strictly based on the **G&SR (General & Subsidiary Rules)** database.
    *   The "Static Database" isn't just looked up; it **defines the physics** of the world.
3.  **Offline RL Training (Imitation Learning)**:
    *   Instead of starting from zero, train the RL agent to *imitate* what the best Section Controllers did in the historical data. This is faster than "fine-tuning".
4.  **Online RL Training (Improvement)**:
    *   Let the agent play in the Digital Twin to find *better* moves than the humans did, optimized for Punctuality and Energy.
5.  **Safety Shield (Deployment)**:
    *   Output of RL -> MILP Solver -> Final Command.

## 3. Model Training Parameters
For a typical Railway RL environment (e.g., using PPO or DQN):

| Parameter | Recommended Value | Purpose |
| :--- | :--- | :--- |
| **Algorithm** | PPO (Proximal Policy Optimization) | Stable, handles continuous/discrete action spaces well. |
| **Learning Rate** | `3e-4` (starts high, decays) | Step size for updates. |
| **Gamma (Discount)** | `0.99` | Prioritizes long-term rewards (avoiding delays hours later). |
| **Batch Size** | `64` to `256` | Number of experiences to learn from at once. |
| **Replay Buffer** | `100,000` steps | (For DQN/SAC) Stores history to learn from. |
| **Reward Function** | `+1` per train arrival, `-0.1` per min delay | **Crucial**: Needs careful tuning. |

## 4. Project Structure Updates
- **simulation/**: Source code for the "Digital Twin", RL agents, and constraint validators.
- **config/**: Configuration files for parameters and settings.
- **data/**:
  - `rules/`: Static G&SR rules and network topology.
  - `models/`: Trained RL model checkpoints.
  - `raw/` & `processed/`: Simulation datasets.
- **notebooks/**: Experiments and analysis.
- **tests/**: Unit and integration tests.
- **results/**: Logs and metric outputs.
