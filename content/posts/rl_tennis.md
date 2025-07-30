+++
title = 'RL Agents Playing Table Tennis'
date = 2025-07-05
draft = false
+++
<!-- <div style="text-align:center;">Author: Sayantani Bhattacharya</div>  -->

<!-- <div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/QuadrupedFleet.gif" alt="Robot Fleet" width="200" height="auto">
</div> -->



<div style="text-align: center; margin-bottom: 30px; margin-top: -30px;">
    🚀 <a href="https://github.com/Sayantani-Bhattacharya/Neural-Net-Netters" style="font-weight:;">Link to Project Github</a>
</div>



## Overview

Two AI players teach themselves to play tennis — no human rules, just pure learning through trial and error.
Behind the scenes, they train together, sharing what they learn, but once on the court, each plays on their own, reacting in real time.
It's like watching two smart robots invent the game of tennis from scratch — and get really good at it.

<div style="text-align: center; margin-bottom: 10px;">
    <img src="/images/projects/MARL_tennis/tennis.gif" alt="RL" width="400" height="auto">
</div>

Trained using centralized learning with shared critics, but playing independently through decentralized policies, the agents master coordination through self-play.
It's a showcase of how autonomous systems can learn complex, competitive-cooperative behaviors — like a doubles match, but with neural networks.
 
## Gymnasium Setup  

This project interfaces directly with Unity via the ML-Agents Toolkit, rather than using a traditional Gym-style API (such as OpenAI Gym, Gymnasium, or Isaac Gym). Unity was chosen to enable high-fidelity, 3D multi-agent simulations, ideal for modeling realistic ball dynamics and agent coordination. Since the learning algorithm was implemented from scratch in PyTorch, there was no need to rely on Gym-compatible libraries like Stable Baselines or RLlib.

## System Design

### Forward-Pass

<div style="text-align: center; margin-bottom: 10px;">
    <img src="/images/projects/MARL_tennis/bd.png" alt="RL" width="300" height="auto">
    <p style="font-size: 20px; color: gray; margin-top: 5px;">
        Decentralized Execution
    </p>
</div>

### Backpropagation

<div style="text-align: center; margin-bottom: 10px;">
    <img src="/images/projects/MARL_tennis/bd2.png" alt="RL" width="400" height="auto">
    <p style="font-size: 20px; color: gray; margin-top: 5px;">
        Centralized Training
    </p>
</div>


### Learning Dynamics of Models:

|        Model                 | Update Mechanism                                                                 |
|------------------------------|----------------------------------------------------------------------------------|
|         **Actor 1 (Local)**  | Gradient ascent using ∇Q/∇a from Critic (Local)                                  |
|         **Actor 2 (Local)**  | Gradient ascent using ∇Q/∇a from Critic (Local)                                  |
|         **Actor 1 (Target)** | Soft update from Actor 1 (Local): `τ * local + (1 - τ) * target`                 |
|         **Actor 2 (Target)** | Soft update from Actor 2 (Local): `τ * local + (1 - τ) * target`                 |
|         **Critic (Local)**   | TD Error loss: MSE between `Q(s,a)` and `r + γ * Q_target(s’, a’)`               |
|         **Critic (Target)**  | Soft update from Critic (Local): `τ * local + (1 - τ) * target`                  |

## MADDPG

I used Multi-Agent Deep Deterministic policy gradient (MADDPG) method along with expirienced replay  — a centralized training, decentralized execution framework ideal for cooperative and competitive multi-agent settings. 
The optimization of network is done using Adam optimizer. This environment has 2 agents that are to be trained. 

Each agent has: 
1. Individual actor local and actor target. 
2. Shared critic local and critic target 

A total of six neural networks are being trained. Ornstein-Uhlenbeck process of adding noise is used to increase exploratory observations. Each of the network consists of normalization layer, three fully connected layers with Rectified Linear Unit (ReLU) activation function between them.

### Actor (Local and Target)

Batch normalization is applied after the first fully connected layer, followed by the application of ReLU activation functions to the output of the first and second fc layer. Finally, the output is passed through a tan h activation function in the last layer to ensure that the action values are within the range [-1, 1].

- fc1: fully connected layer (linear layer) that transforms state_size of agent to fc1_units.
- bn1: batch normalisation layer, that is used to stabilize training and speed up convergence by normalizing the input to the neural network. Here the input and ouput dimention is the fc1_units.
- fc2: fully connected layer mapping from fc1_units to fc2_units.
- fc3: fully connected layer mapping from fc2_units to action_size


### Critic (Local and Target)

Batch normalization is applied after the first fully connected layer, followed by the application of ReLU activation functions to the output of the all fc layers. 

- fc1: fully connected layer (linear layer) that transforms complete (both agents) state_size of agent to fc1_units.
- bn1: batch normalisation layer, that is used to stabilize training and speed up convergence by normalizing the input to the neural network. Here the input and ouput dimention is the fc1_units.
- fc2: fully connected layer mapping from fc1_units+ action to fc2_units.
- fc3: fully connected layer mapping from fc2_units to 1.


### Observation Space

Each agent receives its own local observation, represented by an 8‑dimensional continuous vector. This vector includes:
- The position and velocity of the tennis ball
- The position and velocity of the agent's racket

This is what the agent sees at each timestep — for decision-making in the 2D tennis environment.

### Action Space 

Each agent’s action space is continuous with 2 dimensions:

- Horizontal movement (towards or away from the net)
- Jump action (vertical paddle movement)

These continuous controls are bounded (between –1 and +1) and are implemented via the Actor neural networks using a final tanh activation function. The z-axis (depth into/out of the screen) is fixed, so there's no forward-back movement.

### Reward

The reward function is designed to encourage longer rallies between agents:

- +0.1 reward is given to both agents for every timestep the ball remains in play.
- -0.01 penalty is given when the ball hits the ground, ending the rally.

This setup incentivizes both agents to cooperate indirectly — though they learn independently, both benefit from sustained volleys, fostering emergent teamwork.


<!-- ## Insights -->

## Results

<div style="text-align: center; margin-bottom: 20px;">
    <img src="/images/projects/MARL_tennis/plot.png" alt="RL" width="400" height="auto">
    <p style="font-size: 18px; color: gray; margin-top: 10px;">
        Blue line (score): Raw score per episode (i.e., total reward in that single episode).<br>
        Orange line (average score): Moving average over the past 100 episodes, <br>
        X-axis: No. of episodes.
    </p>
</div>

Trained two agents using the MADDPG algorithm over 2000 episodes in the simulated tennis environment. Early in training, the agents struggled — scoring near-zero as they failed to hit the ball consistently. Over time, with help from replay memory and centralized critic updates, they gradually learned to rally more effectively.

Performance was tracked using the episode score (sum of both agents' rewards), which directly reflects the rally length. And a moving average was computed to visualize long-term trends amid the high variance typical in multi-agent learning. Around episode 1400, a breakthrough occurred: scores began spiking rapidly as the agents discovered cooperative volleying strategies.

By episode 1500, the average score had reached the environment's “solved” threshold of 0.5 — demonstrating that the agents could sustain rallies with increasing consistency. Although I evaluated performance using the maximum of the two agents’ scores, in practice both agents evolved toward symmetric, balanced playstyles, reinforcing mutual success.

This result highlights the effectiveness of centralized training with decentralized execution (CTDE) in non-stationary, cooperative multi-agent settings.

<!-- 
Episode rewards vary a lot, especially in early training where exploration is high and agents are inconsistent. Moving average gives smoother fluctuations and is better for visualizing learning trends. -->




