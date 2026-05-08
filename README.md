# Reinforcement Learning for Sim-to-Real Transfer

This project investigates the use of Reinforcement Learning (RL) for robotic locomotion and focuses on the challenge of transferring policies learned in simulation to environments with different dynamics. The experiments are based on the Hopper environment, where an agent learns to perform stable forward hopping while remaining robust to changes in physical parameters.

The main goal of the project is to study the **Sim-to-Real transfer problem** through a controlled **Sim-to-Sim** setup. A policy is trained in a source environment and evaluated in a target environment where the torso mass is modified. This allows the project to measure how well different RL methods generalize when the environment dynamics change.

## Authors

- Fatemeh Saleh  
- Hossein Ahmadzadeh  

Politecnico di Torino

## Project Overview

Reinforcement Learning is commonly used in robotics because it enables agents to learn complex behaviors through trial-and-error interaction with an environment. However, training directly on real robots is expensive, slow, and potentially unsafe. A practical solution is to train policies in simulation, but policies trained only in a fixed simulator often fail when transferred to real-world conditions because of the **reality gap**.

This project studies that problem using the Hopper robot. Several RL algorithms are implemented and compared, including:

- REINFORCE
- REINFORCE with baseline
- Actor-Critic
- Proximal Policy Optimization (PPO)
- PPO with Uniform Domain Randomization (UDR)

The report shows that PPO performs well when training and testing are done in the same environment, but its performance drops significantly when the source-trained policy is evaluated in the modified target environment. Uniform Domain Randomization is then used to improve robustness by randomizing selected body-part masses during training. :contentReference[oaicite:0]{index=0}

## Environment

The experiments use the **Hopper** environment from OpenAI Gym. Hopper is a planar one-legged robot composed of four main body parts:

- Torso
- Thigh
- Leg
- Foot

The agent controls the robot by applying continuous torques to the actuated joints. The objective is to learn a stable locomotion policy that maximizes forward movement while avoiding falls and excessive control effort.

Two environments are considered:

| Environment | Description |
|---|---|
| Source | Original Hopper dynamics |
| Target | Same as source, but with modified torso mass |

The target environment changes only the torso mass, creating a controlled domain shift for evaluating transfer performance.

## Methods

### REINFORCE

REINFORCE is implemented as a Monte Carlo policy-gradient algorithm. It collects full episodes, computes discounted returns, and updates the policy using gradient ascent.

A baseline version is also tested to reduce variance. The baseline improves training stability, although it slightly increases training time.

### Actor-Critic

The Actor-Critic method uses two neural networks:

- **Actor:** selects actions using a policy network.
- **Critic:** estimates the state-value function and guides policy updates.

Compared with REINFORCE, Actor-Critic achieves more stable learning and higher reward performance because the critic reduces variance in the policy-gradient estimate.

### Proximal Policy Optimization

PPO is used as the main algorithm for transfer evaluation. The implementation uses PPO-Clip from Stable-Baselines3. PPO stabilizes policy updates by clipping the probability ratio between the old and new policies, preventing overly large destructive updates.

Hyperparameter tuning is performed for:

- Learning rate
- Clip range
- Entropy coefficient
- Discount factor
- GAE lambda
- Number of steps
- Batch size

## Sim-to-Real Transfer Evaluation

The PPO model is evaluated under three scenarios:

| Scenario | Mean Reward | Standard Deviation |
|---|---:|---:|
| Source → Source | 1731.82 | ±103.26 |
| Source → Target | 756.93 | ±84.23 |
| Target → Target | 1649.66 | ±97.24 |

The results show a clear transfer gap. The source-trained PPO policy performs well in the source environment but drops sharply when evaluated in the target environment. This confirms that even a small physical change, such as modifying the torso mass, can significantly reduce policy performance.

## Uniform Domain Randomization

Uniform Domain Randomization is applied to improve generalization. During training, selected body-part masses are sampled from uniform distributions. This exposes the policy to a wider range of dynamics and makes it more robust to unseen environment variations.

In this project, UDR randomizes the masses of the thigh, leg, and foot while testing transfer to a target environment with a modified torso mass.

The UDR policy achieves:

| Scenario | Mean Reward | Standard Deviation |
|---|---:|---:|
| Source → Target with UDR | 1620.1 | 370.3 |

This result is much closer to the target-trained PPO upper bound of 1649.66 and significantly better than direct source-to-target transfer. Therefore, UDR successfully reduces the transfer gap.

## Key Results

- REINFORCE can learn Hopper locomotion but suffers from high variance.
- Adding a baseline improves REINFORCE stability.
- Actor-Critic achieves better and more stable learning than REINFORCE.
- PPO performs strongly when training and testing are done in the same environment.
- Direct PPO transfer from source to target fails significantly because of domain shift.
- Uniform Domain Randomization improves robustness and brings source-to-target performance close to target-trained PPO performance.

## Main Conclusion

The experiments confirm that policies trained in a fixed simulation do not necessarily generalize well when the environment dynamics change. Even a small modification in the Hopper robot’s physical parameters can create a large performance drop.

Uniform Domain Randomization provides an effective solution by exposing the policy to varied dynamics during training. In this project, UDR improves the source-to-target reward from 756.93 to 1620.1, making the transferred policy perform close to a policy trained directly in the target environment.

## Technologies Used

- Python
- OpenAI Gym
- Stable-Baselines3
- PyTorch
- NumPy
- Matplotlib
- Reinforcement Learning
- Proximal Policy Optimization
- Domain Randomization

## Repository Structure

```text
.
├── src/                  # Reinforcement learning implementations
├── models/               # Trained models and checkpoints
├── results/              # Evaluation results and plots
├── figures/              # Training curves and visualizations
├── report/               # Final project report
└── README.md
