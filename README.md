# CSCN8020-RL-Warehouse-Robot-Optimization
# Warehouse Robot Path Optimization Using Deep Q-Networks (DQN)

This repository contains the implementation and experimental results for our project on Warehouse Robot Path Optimization using Deep Q-Networks (DQN). 
---

## Table of Contents

- [Introduction](#introduction)
- [Application Overview](#application-overview)
- [Environment Description (RWARE)](#environment-description)
- [Reinforcement Learning Algorithms](#reinforcement-learning-algorithms)
  - [Tabular Q-Learning Baseline](#tabular-q-learning-baseline)
  - [Deep Q-Network (DQN)](#deep-q-network-dqn)
- [System Architecture](#system-architecture)
- [Integration with Graphical Environment and API](#integration-with-graphical-environment-and-api)
- [Experimental Results and Analysis](#experimental-results-and-analysis)
- [Project Structure](#project-structure)
- [Usage](#usage)
- [References](#references)

---

## Introduction

Modern warehouses require autonomous robots that can adaptively navigate complex, dynamic environments. Traditional pre-defined routing approaches fall short when warehouse layouts change or when multi-agent coordination is required. This project utilizes Deep Q-Networks (DQN) to address warehouse robot path optimization by enabling the agent to **learn** effective navigation strategies in a grid-based simulation, handling collisions, partial observability, and dynamic task assignments.
---

## Application Overview

The application focuses on optimizing the path of a warehouse robot to:
- Efficiently deliver requested shelves to designated goal locations.
- Avoid collisions with obstacles (shelves, walls, and other robots).
- Adapt to both single-agent and multi-agent scenarios.

Our solution is designed to operate in a simulated warehouse environment where the state is represented by a local (3×3) grid around the robot and the reward system is shaped to encourage timely and safe deliveries.

---

## Environment Description

We use the **RWARE** (Robotic Warehouse Environment) for simulating the warehouse. Key features include:
- **Grid-Based Layout:** Configurable sizes (tiny, small, medium, large) and custom layout definitions.
- **Partial Observability:** Each robot observes a 3×3 grid, capturing nearby shelves, goals, and obstacles.
- **Action Space:** Discrete actions such as turning left/right, moving forward, and loading/unloading a shelf.
- **Reward Structure:** +10 for successful delivery, –10 for collisions, and a time penalty (–1 per step).

The environment is fully Gym-compatible, allowing seamless integration with our RL agents.

---

## Reinforcement Learning Algorithms

### Tabular Q-Learning Baseline

A traditional Q-learning algorithm was implemented as a baseline.  
- **Approach:** A table of Q-values for each state-action pair with updates based on the Bellman equation:  
  \[
  Q(s, a) \gets Q(s, a) + \alpha \left[ r + \gamma \max_{a'} Q(s', a') - Q(s, a) \right]
  \]
- **Limitations:** Scalability issues arise due to the large state space and partial observability in a dynamic warehouse environment.

### Deep Q-Network (DQN)

The DQN agent replaces the Q-table with a neural network that approximates the Q-value function:
- **Components:**
  - **Online Q-Network:** Estimates Q-values for actions based on the current state.
  - **Target Q-Network:** A periodically updated copy of the online network used to stabilize training.
  - **Experience Replay Buffer:** Stores past transitions \((s, a, r, s')\) to break correlations and improve learning efficiency.
  - **ε-Greedy Exploration:** Balances exploration and exploitation during training.
- **Training:** The network minimizes the loss defined as:  
  \[
  L(\theta) = \mathbb{E}_{(s,a,r,s') \sim D} \left[ \left( r + \gamma \max_{a'} Q(s', a'; \theta^-) - Q(s, a; \theta) \right)^2 \right]
  \]
- **Outcome:** DQN converges faster and generalizes better to unseen layouts compared to Q-learning.

---

## System Architecture

The overall system architecture includes:
- **RWARE Environment:** Simulates the warehouse scenario, providing observations, rewards, and enforcing dynamics (e.g., collision handling).
- **DQN Agent:** Consists of the online Q-network, target network, and experience replay. The agent processes the local observation, selects an action using ε-greedy policy, and learns from the environment’s feedback.
- **Data Flow:**
  - The agent receives a state (3×3 grid) from RWARE.
  - The online network computes Q-values, and an action is selected.
  - The action is passed to the environment, which returns the next state and reward.
  - The transition is stored in the replay buffer for network training.
- **Visualization Module:** Uses RWARE’s `render()` function to display the current state of the warehouse, enabling a real-time view of robot navigation and behavior.

See the detailed system diagrams in the [Documentation](docs/) folder.

---

## Integration with Graphical Environment and API

Our integration leverages the Gym interface provided by RWARE:
- **API Functions:** `reset()`, `step()`, and `render()` are used to interact with the environment.
- **Observation Encoding:** The local 3×3 grid is encoded into a feature vector that is fed into the DQN.
- **Reward Shaping:** Custom logic is applied after receiving the environment’s reward to penalize collisions and incentivize timely deliveries.
- **Real-Time Visualization:** A dedicated module renders the warehouse grid in real time, allowing users to observe the agent’s decisions and the dynamic environment changes.

---

## Experimental Results and Analysis

**Targets Achieved:**
- **Learning Efficiency:** DQN learned effective navigation strategies in significantly fewer episodes compared to Q-learning.
- **Path Optimality:** The DQN agent found near-optimal routes, reducing the average steps-to-goal.
- **Collision Avoidance:** With properly tuned reward penalties, the agent minimized collisions, even in multi-agent settings.
- **Generalization:** The DQN policy showed moderate generalization to unseen warehouse layouts.

**Challenges Encountered:**
- **Sparse Rewards:** Balancing the time penalty and collision penalties was challenging.
- **Multi-Agent Non-Stationarity:** Independent DQN agents sometimes interfered with each other’s learning.
- **Hyperparameter Tuning:** Convergence depended heavily on careful selection of learning rate, ε-decay, and target network update frequency.

These findings and metrics are detailed further in our final report.

---
## Usage

### Prerequisites
- Python 3.7+  
- [Gymnasium](https://gymnasium.farama.org/)  
- [PyTorch](https://pytorch.org/)  
- Other dependencies listed in `requirements.txt`

