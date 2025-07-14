# Reinforcement Learning-Based Packet Scheduling for QoS-Aware Network Routers

## Project Overview
This project implements an end-to-end reinforcement learning (RL) solution to optimize packet scheduling in a simulated network router. The RL agent is designed to make real-time queue selection decisions that respect the Quality of Service (QoS) requirements of multiple traffic classes: video, voice, and best-effort data.

## Problem Statement
We address the problem of packet scheduling in a router handling three traffic classes:
- **Video (Q1)**, 
- **Voice (Q2)**, and 
- **Best-effort (Q3)**

Each queue receives fixed-length packets independently based on a Bernoulli process. The router transmits at most one packet per time-slot. Queues Q1 and Q2 have strict **mean delay constraints** (R1 and R2), while Q3 has no formal deadline but should be served fairly to prevent starvation.

The scheduler selects a queue to serve at every time-slot. The **RL policy** is learned to:
- Meet delay constraints for Q1 and Q2
- Minimize average delay for Q3
- Adapt to two scheduling scenarios:
  - No switch penalty
  - One-slot switch penalty

This mirrors real-world QoS-aware scheduling requirements in modern routers.

## Technical Approach

### Environment Simulation
Implemented a custom OpenAI Gym environment simulating a router with three queues. Features include:
- Queue state tracking and reward calculation
- Scenario support for instant or delayed switching
- Configurable traffic rates and seeds

### State Space
The environment state is modeled as a 6-dimensional vector capturing:
- Queue lengths
- Delay violations
- Rolling average delays

This provides a comprehensive snapshot of system conditions at each time step.

### Action Space
The agent can:
- Select one of the three queues to transmit from
- Stay in the current queue or switch (cost applied if scenario 2)

### Reward Function
Reward design includes:
- Penalty for delay violations in Q1 and Q2
- Positive reward for servicing Q3 to promote fairness
- Encouragement for reducing queue backlog

The function is tuned to balance strict delay compliance and fairness.

### RL Algorithm
Used **Tabular Q-learning** with ε-greedy exploration. The agent was trained over multiple episodes with:
- Learning rate tuning
- Discount factor optimization
- Decay control for exploration

## Evaluation and Baseline Comparison
The RL policy was evaluated against:
- FIFO (First-in, First-out)
- EDF (Earliest Deadline First)
- Sequential Priority

Metrics analyzed:
- Mean delay per queue
- Packet drop rate
- Queue fairness index

### Results
- Q1 and Q2 delays consistently below R1 and R2
- RL showed better delay balancing than FIFO/SP, especially for Q3
- RL policy adapts to bursty traffic and queue penalties efficiently

## Independent Evaluation
- Tested performance under unseen traffic rates and patterns
- Compared results to literature-defined thresholds
- Discussed practical deployment feasibility on real-time router hardware

## Tech Stack
- Python
- OpenAI Gym (custom environment)
- NumPy, Matplotlib
- GitHub for version control

## Outcome
This project demonstrates the application of reinforcement learning to solve a challenging network-level scheduling problem. The solution maintains delay constraints, adapts to dynamic loads, and provides fair queue servicing — all in a resource-efficient and interpretable framework.
