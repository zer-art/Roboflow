# Simulation Logic

This directory will contain the core "Digital Twin" of the railway network.

- `environment.py`: Defines the `RailwayEnv` class, a custom Gymnasium environment that simulates the Indian Railways network. It uses SimPy for discrete event simulation to model train physics, signaling, and scheduling.
- `agent.py`: The RL agent implementation (Future).
- `train.py`: Script to run the training loop (Future).
- `evaluate.py`: Script to test the agent against the "Digital Twin" (Future).

## Usage
The `RailwayEnv` allows agents to interact with the simulation by:
1. Observing the state of the network (train positions, signals).
2. Taking actions (adjusting speeds, changing tracks).
3. Receiving rewards based on efficiency and safety.

