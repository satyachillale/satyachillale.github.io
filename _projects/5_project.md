---
layout: page
title: Reinforcement Learning in Rocket League (RL²) Multi reward system
description: A deep dive into how reward design shapes the instincts of AI agents on the Rocket League field
img: assets/img/rl.png
importance: 2
category: Graduate Studies
giscus_comments: false
---

[[Project Website]](https://aiden-frost.github.io/RocketLeagueGym-Rewards/) [[Presentation]](https://docs.google.com/presentation/d/1Losh2EeNvpRQ31Fi9mhvghKpchoFJYR-8D2LuFRhyDY/edit?usp=sharing) [[Code]](https://github.com/satyachillale/RocketLeagueGym-Rewards)

### Introduction

As part of my deep dive into reinforcement learning (RL) and its application to game environments, I worked on the Rocket League Gym Rewards project. An open-source framework that allows for flexible and modular reward function design when training AI agents to play Rocket League.

This project builds on top of RLGym, which provides a lightweight interface between Rocket League and Python based RL training libraries. While RLGym offers a solid foundation, we noticed that reward design the piece that tells the agent what behavior is “good” was still very open-ended and minimal. This made it difficult to test out different strategies or iterate quickly, also to make the robot play the game well. So we set build a system that would make reward tuning easier, more transparent, and highly customizable.

### RLGym Framework
At its core, the Rocket League Gym Rewards framework breaks down complex rewards into smaller, modular pieces. These can be mixed and matched depending on what kind of behavior you want to train.

For example, you might want to:

- Reward the agent for moving towards the ball

- Give bonus points for touching the ball (especially first touches)

- Penalize boost waste or incentivize boost pickups

- Encourage staying behind the ball (a good defensive posture)

These reward "components" can be stacked together using a simple config setup, and each one contributes independently to the final reward at every timestep. This makes it super easy to tweak behaviors and understand which components are making a difference.

### Our work

- Reward Design & Evaluation: We tested different reward configurations to see how they impacted the agent’s performance and gameplay style. For example, some setups led to more aggressive ball chasing, while others encouraged more structured play.

- Training with PPO: We used PPO (Proximal Policy Optimization) from the Stable-Baselines3 library to train the agents. We ran multiple training sessions, each with different reward sets, and monitored how they performed over time.

- Experiments: To better understand the role of each reward component, we experimented by removing one reward at a time and observing the resulting behavior and performance metrics.

- Visual Analysis: We created visualizations such as frame by frame reward breakdowns. These helped in understanding which rewards were driving agent decisions and where improvements could be made.


For more details and cool videos checkout the [[Project Website]](https://aiden-frost.github.io/RocketLeagueGym-Rewards/) 