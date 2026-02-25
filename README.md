# Reinforcement Learning-Based Packet Scheduling for QoS-Aware Routers

This project studies packet scheduling in a single-router setting with three traffic classes:
- `Q1`: video (delay-constrained)
- `Q2`: voice (delay-constrained)
- `Q3`: best-effort (fairness-focused)

A tabular Q-learning agent is trained to select which queue to serve at each time slot and is compared against standard baselines.

## Repository Contents
- `training_analysis.ipynb`: main implementation, training loop, and analysis plots
- `requirements.txt`: core Python dependencies
- `README.md`: project documentation

## Problem Setup
- Time-slotted router model with at most one packet served per slot
- Independent Bernoulli arrivals per queue with configurable rates
- Delay requirements:
  - `R1 = 6` for `Q1`
  - `R2 = 4` for `Q2`
  - no hard delay bound for `Q3`

Two scenarios are evaluated:
1. **Scenario 1 (no switch cost):** selected queue is served immediately
2. **Scenario 2 (one-slot switch cost):** switching queues consumes one slot without service

## RL Formulation
- **State:** 6-dimensional discrete vector built from `Q1` and `Q2` features
  - queue-length bin
  - deadline-violation flag
  - delay bin from rolling mean delay
- **Action:** `0/1/2` to serve `Q1/Q2/Q3`
- **Algorithm:** tabular Q-learning with epsilon-greedy exploration
- **Reward:** combines delay-compliance terms for `Q1/Q2`, a service bonus for `Q3`, and a backlog-reduction term

## Baselines
The RL policy is compared with:
- Random
- EDF (Earliest Deadline First)
- Priority (`Q1 > Q2 > Q3`)
- FIFO (oldest packet first across queues)

## Setup
Use Python 3.9+.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install pandas jupyter
```

Note: the notebook imports `pandas`, which is not currently listed in `requirements.txt`.

## Run the Experiments
Launch Jupyter and run all cells in `training_analysis.ipynb`:

```bash
jupyter notebook training_analysis.ipynb
```

The notebook will:
- train for both scenarios (`EPISODES=50000`, `MAX_STEPS=1000`)
- plot training rewards
- print balanced-load delay comparisons vs baselines
- evaluate robustness under `light`, `moderate`, `heavy`, and `extreme` traffic profiles
- plot RL delay trends across traffic profiles

## Reproducibility
The notebook sets a fixed seed (`SEED = 42`) for Python `random` and NumPy.

## Notes and Limitations
- The full workflow currently lives in a notebook (no separate Python package/module yet).
- Queue 3 fairness is encouraged by reward shaping rather than an explicit formal guarantee.
- Long training runs may take significant time on CPU.
