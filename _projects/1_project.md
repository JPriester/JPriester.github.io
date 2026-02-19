---
layout: page
title: Chatter-Free Policy Switching
description: A Timer-Based Hybrid Supervisor for Robust Policy Switching in Reinforcement Learning
img: assets/img/twoline_policy.png
importance: 1
category: work
related_publications: true
---

We address the challenge of switching among multiple learned policies in reinforcement learning control systems, where conventional value function-based methods can lead to **chattering** in the presence of small measurement noise. Chattering occurs when an agent rapidly switches its decision—often due to environmental perturbations—resulting in inefficient or destabilizing behavior.

To solve this, we propose a novel predictive **timer-based hybrid supervisor** that integrates a resettable timer to enforce a minimum dwell time on the active policy. This dwell time is adaptively adjusted by predicting the evolution of the system's state, ensuring that a switch occurs only when a significantly better alternative is predicted.

### The Chattering Problem (Two-Line Environment)

To illustrate the issue, consider a system evolving on a 1D line where the goal is to robustly stabilize two disconnected setpoints. We train two policies, $\pi_1$ and $\pi_2$, to stabilize their respective setpoints. A straightforward supervisory policy $Q^*$ selects the logic variable corresponding to the highest approximate value function $\hat{V}_q$. 

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/twoline_valuefunction.png" title="Value Functions" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/twoline_policy.png" title="Resulting Control Policy" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: The approximate value functions $\hat{V}_1$ and $\hat{V}_2$. Right: The resulting control policy by applying the supervisory policy $Q^*$. The setpoints are denoted by red stars.
</div>

While $Q^*$ preserves the properties of the individual policies under ideal conditions, it is highly vulnerable to measurement noise. Near the critical state where the value functions intersect, a small perturbation tricks the supervisor into rapidly alternating between policies.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/twoline_valuesims_noise00_stepsahead1.png" title="No Noise, Standard Supervisor" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/twoline_valuesims_noise03_stepsahead1.png" title="Chattering Under Noise" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/twoline_valuesims_noise03_stepsahead10.png" title="Hybrid Supervisor" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Convergence without noise ($\epsilon=0$). Middle: Bounded measurement noise ($\epsilon=0.3$) causes the standard $Q^*$ supervisor to chatter, preventing convergence. Right: Our proposed Hybrid Supervisor successfully mitigates chattering and stabilizes the target under the same noise.
</div>

### Multi-Target Application

We extend this to a 2D planar system aiming to stabilize four disconnected setpoints. We introduce periodic variations into the reward function, creating local minima and maxima across the state space. As a result, following the shortest Euclidean path is not necessarily optimal, leading to highly complex optimal switching boundaries.

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/multitargs_qmax_rewarddistFalse.png" title="Optimal Regions Without Periodic Variations" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/multitargs_qmax_rewarddistTrue.png" title="Optimal Regions With Periodic Variations" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Visualization of the optimal policy regions $Q^*$ without (left) and with (right) periodic variations. The variations create an intricate and fragmented decision boundary among the four policies.
</div>

We compare the closed-loop system solutions under three different supervisors under an adversarial noise magnitude of $\epsilon=0.1$:
1. **Vanilla ($Q^*$):** Highly sensitive to noise; chatters and frequently fails to reach the target set.
2. **Fixed-Timer:** Uses a static dwell time. While more robust, it can become trapped bouncing between adjacent boundaries.
3. **Timer-Based Hybrid Supervisor:** Our proposed method successfully mitigates chattering and consistently guides the system to the setpoints for all initial conditions.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multitargs_valuesims_noise01_stepsahead1rewardmod_False_vanilla.png" title="Vanilla Supervisor" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multitargs_valuesims_noise01_stepsahead20rewardmod_False_fixed.png" title="Fixed Timer Supervisor" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/multitargs_valuesims_noise01_stepsahead20rewardmod_False_hybrid.png" title="Hybrid Supervisor" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Closed-loop solutions under measurement noise. Left (Vanilla): Highly sensitive to noise, failing to reach the target set due to chattering. Middle (Fixed-Timer): Avoids chattering but can become trapped between decision regions. Right (Hybrid): Successfully mitigates chattering and consistently guides the system to the setpoints.
</div>

Our theoretical analysis and simulation results prove that the hybrid supervisor maintains a robustness margin, preventing rapid switching under bounded measurement noise and rendering a compact set robustly globally asymptotically stable.

For full mathematical proofs on the hybrid basic conditions, non-Zeno behavior, and the robust asymptotic stability of the hybrid closed-loop system, please refer to our full paper {% cite main %}.