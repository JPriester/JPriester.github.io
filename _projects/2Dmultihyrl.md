---
layout: page
title: MultiHyRL
description: Robust Hybrid RL for Obstacle Avoidance against Adversarial Attacks on the Observation Space
img: assets/img/multihyrl/hyrlsets4.png
importance: 2
category: work
related_publications: true
---

Reinforcement learning (RL) holds incredible promise for the next generation of autonomous vehicles. However, standard RL lacks formal robustness guarantees against adversarial attacks in the observation space—especially for safety-critical tasks like obstacle avoidance. 

In this project, we introduce **MultiHyRL**, a new hybrid RL algorithm featuring hysteresis-based switching. MultiHyRL overcomes the limitations of traditional continuous policies and guarantees robustness against measurement noise for vehicles operating in environments with multiple random obstacles.

### The Topological Obstruction Problem

When a vehicle navigates past an obstacle to reach a target, the obstacle acts as a *topological obstruction*, forcing the vehicle to choose between passing clockwise or counterclockwise. 

<div class="row justify-content-sm-center">
    <div class="col-sm-5 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/bew_overview_crash.png" title="Bird's-Eye View Crash" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/customfeatureextractor_arch.png" title="Feature Extractor Architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Overview of the bird's-eye view obstacle avoidance setting. The red dashed line represents the decision boundary. Small measurement noise near this boundary tricks standard RL agents into rapidly alternating decisions (chattering). Right: Our closed-loop control architecture combining local (CNN) and global (MLP) features.
</div>

Standard continuous RL policies (like Proximal Policy Optimization) are highly vulnerable near these decision boundaries. 

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/multihyrl/example1_nonoise.png" title="Standard PPO Without Noise" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/multihyrl/example1_noise.png" title="Standard PPO With Noise" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Standard PPO steers the vehicle to the target under ideal conditions. Right: With an arbitrarily small amount of measurement noise ($\epsilon_p = 0.1$), the vehicle "chatters" at the critical boundary, failing to pass the obstacle and ultimately crashing or getting stuck.
</div>

### MultiHyRL: State Space Partitioning and Hysteresis

Rather than relying on a single vulnerable policy, MultiHyRL identifies *critical points* in the state space where chattering occurs. It then uses a Support Vector Machine (SVM) to generate a **relaxed Voronoi partition**, safely dividing the state space into overlapping sets ($\mathcal{M}_0$ and $\mathcal{M}_1$). 

We then train two separate policies: one that strictly steers clockwise ($\pi_0$) and one that strictly steers counterclockwise ($\pi_1$).

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/svm_dataset.png" title="SVM Regions" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/hyrlsets4.png" title="Voronoi Partitioning" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Regions used to create the SVM training dataset to identify optimal switching boundaries. Right: Visualization of the state space partition for multiple obstacles. The overlapping red areas act as hysteresis buffers, ensuring the vehicle commits to a single side of the obstacle and ignores adversarial measurement noise.
</div>

We introduce two logic variables into a hybrid system: $q$ to indicate the active policy and $\lambda$ to indicate the focused obstacle. These variables only switch when the system completely exits an overlapping set, effectively eliminating chattering.

### Robust Navigation & Capture the Flag

We tested MultiHyRL against a standard PPO agent across various adversarial noise conditions ($\epsilon_p = 0.2$):

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/normal_nonoise.png" title="Standard Agent (No Noise)" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/normal_noise.png" title="Standard Agent (Noise 0.2)" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/multihyrl_noise.png" title="MultiHyRL Agent (Noise 0.2)" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: The standard agent without noise. Middle: The standard agent suffers heavily from measurement noise, chattering and getting trapped in front of obstacles. Right: MultiHyRL successfully mitigates the exact same noise, consistently guiding the vehicle safely to the target.
</div>

MultiHyRL extends naturally without modification to environments with dynamic (moving) obstacles and varying obstacle sizes. To push the limits, we pitted the standard agent and the MultiHyRL agent against each other in a noisy, adversarial game of **Capture the Flag**.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/ctf_frame1.png" title="Capture the Flag Start" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/ctf_frame2.png" title="Capture the Flag Mid" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multihyrl/ctf_frame3.png" title="Capture the Flag End" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Still frames from the Capture the Flag simulation. The standard agent (red) gets stuck hesitating in front of obstacles due to noise, allowing the MultiHyRL agent (blue) to fluidly navigate the map and score.
</div>

For a deeper dive into the formal stability guarantees and the mathematics behind the hybrid switching logic, please refer to our full paper {% cite dePriester2024MultiHyRL %}.