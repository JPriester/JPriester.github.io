---
layout: page
title: "Timer-Based Hybrid Supervisor for Robust Policy Switching"
description: "Preventing Indecision, Chattering, and Deadlocks in Safety-Critical Sequential Decision-Making"
img: assets/img/publication_preview/timerhyrl_timerapproach5.jpg
video: /assets/video/TimerHybridSupervisorLight.mp4
importance: 3
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Reinforcement Learning Conference (RLC 2025)</h4>
  <p>
    This article provides an accessible overview of our paper, <strong>"A Timer-Based Hybrid Supervisor for Robust, Chatter-Free Policy Switching"</strong> by <em>Jan de Priester and Ricardo G. Sanfelice</em>, published in the <strong>Reinforcement Learning Journal (RLC 2025)</strong>. For the complete mathematical definitions, hybrid Lyapunov analysis, and formal proofs, see the full paper and simulation code:
    <a href="{{ '/assets/pdf/timerHyrlpaper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 6px;">[Read Paper PDF →]</a>
    <a href="https://github.com/JPriester/Timer-Value-Supervisor" target="_blank" style="font-weight: 700; margin-left: 8px;">[Simulation Code →]</a>
  </p>
</div>

Across **robotics and sequential decision-making for safety-critical applications**, a single control policy is often not enough. Instead, an autonomous system relies on a **set of specialized policies or discrete decisions**, each trained with its own value function to handle a distinct maneuver, sub-goal, or operating mode.

A standard way to coordinate multiple policies at runtime is **greedy value-based switching**: at every step, the agent evaluates the learned value functions at the current state and selects whichever option promises the highest return. However, whenever two or more policies offer similar expected returns, **critical decision boundaries** ($\mathcal{M}^\ast$) emerge in the state space. Along these boundaries, small sensor perturbations or an adversary can easily flip the value ranking back and forth—causing a memoryless supervisor to rapidly oscillate between conflicting decisions (**chattering**) or stall in a deadlock.

Crucially, <span class="key-highlight">scaling up to larger AI models or richer policy banks does not remove this vulnerability</span>: in higher-dimensional state spaces and complex reward landscapes, critical boundaries of indecision persist in far more places than expected.

Existing approaches to prevent policy chattering face a fundamental trade-off:
- **Spatial Hysteresis Partitioning:** Constructing overlapping spatial regions around switching boundaries works well when critical regions have a structured geometry (as in our [Hysteresis-Based Reinforcement Learning](/projects/l_multihyrl_3d_avoidance/) framework), but becomes intractable when value-function boundaries are fragmented, non-convex, or unknown in advance.
- **Fixed-Timer Switching:** Holding each policy for a constant waiting time creates a rigid trade-off: a timer that is **too short** fails to stop chattering under perturbations, while a timer that is **too long** makes the agent unresponsive and traps it oscillating between decision regions.

In this work, we resolve both limitations by introducing a **predictive timer-based hybrid supervisor**:
> **Rather than relying on pre-computed spatial partitions or a fixed waiting time, our supervisor uses short-horizon state predictions and the learned value functions to adaptively determine how long to commit to the active policy.** At each switch, the supervisor forecasts when a competing policy will become significantly better—locking in an adaptive dwell timer $\delta_d$ that prevents chattering and deadlocks while preserving underlying policy properties such as stability.

---

### 1. Why Greedy Supervisors Chatter—and When Switching is Actually Beneficial

Why does selecting the policy with the highest value function $Q^\ast(z)$ cause catastrophic chattering in some regions of the state space, while working harmlessly in others?

In our paper, we show that switching boundaries between competing policies fall into two distinct categories:
1. **Exploitative Switching (Benign):**
   Far from a target, two policies may temporarily trade leadership even though both push the system in the **same general direction** (for example, when one policy saturates the actuators more aggressively toward the goal). Switching here actually accelerates convergence, and noise near this boundary does not stall the agent.
2. **Critical Areas $\mathcal{M}^\ast$ (Catastrophic Chattering):**
   Along **critical areas**, two policies yield the exact same optimal value ($\hat V_1(z) = \hat V_2(z)$), but their control actions **pull the system in opposing directions**. When the true state $z$ approaches $\mathcal{M}^\ast$, small measurement noise of magnitude $\varepsilon$ pushes the perceived state across the boundary, activating the opposing policy and driving the state right back toward the boundary. Under alternating sensor noise, the critical boundary becomes an **artificial trap** that locks the system into high-frequency chattering.

#### Illustrative Example: Stabilizing Two Disconnected Targets
Consider a vehicle on a line tasked with stabilizing either of two disconnected target locations ($-1$ or $+1$) using two policies $\pi_1$ and $\pi_2$ with learned value functions $\hat V_1$ and $\hat V_2$:

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_valuefunction.png" title="Approximate Value Functions V1 and V2" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_policy.png" title="Piecewise Control Policy under Greedy Supervisor Q*" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left:</strong> Learned value functions $\hat V_1$ (blue) and $\hat V_2$ (green) for policies $\pi_1$ and $\pi_2$, with target setpoints at $-1$ and $+1$ marked by red stars. <strong>Right:</strong> Resulting piecewise control input $u$ under the greedy supervisor $Q^\ast(z)$, showing a critical opposing-action boundary at $z_c = 0$ and benign exploitative switches near $z = \pm 1.6$.
</div>

As shown in the closed-loop trajectories below, under bounded measurement noise ($\varepsilon = 0.3$), the greedy supervisor chatters rapidly across $z_c = 0$ and fails to stabilize trajectories starting near the origin (middle plot), whereas our **Timer-Based Hybrid Supervisor** eliminates chattering and smoothly stabilizes all initial states (right plot):

<div class="row mt-4 mb-2">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_valuesims_noise00_stepsahead1.png" title="Greedy Supervisor Q* without Noise" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_valuesims_noise03_stepsahead1.png" title="Greedy Supervisor Q* Chattering under Noise" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_valuesims_noise03_stepsahead10.png" title="Timer-Based Hybrid Supervisor under Noise" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Noise-Free Greedy $Q^\ast$):</strong> Without sensor noise, all trajectories converge to the target setpoints $\pm 1$. <strong>Middle (Noisy Greedy $Q^\ast$, $\varepsilon = 0.3$):</strong> Measurement noise causes trajectories starting near $z_c = 0$ to chatter indefinitely around the origin. <strong>Right (Proposed Timer-Based Hybrid Supervisor, $\varepsilon = 0.3$):</strong> Predictive dwell-time supervision eliminates chattering and restores smooth convergence to the targets.
</div>

---

### 2. How Predictive Dwell-Time Supervision Works

To prevent chattering without needing prior geometric maps of the critical areas $\mathcal{M}^\ast$, our supervisor augments the control loop with a **resettable timer** $\tau$, the **active policy index** $q$, and an **adaptive dwell time** $\delta_d$:

<div class="interactive-lab-card">
  <div class="direction-canvas-wrap">
    <div class="canvas-header">
      <span class="canvas-title">Animation: Predictive Dwell-Time Supervision vs. Memoryless Chattering</span>
      <span class="canvas-live-dot">● Three-Phase Hybrid Execution</span>
    </div>
    <video
      src="{{ '/assets/video/TimerHybridSupervisorLight.mp4' | relative_url }}"
      autoplay
      loop
      muted
      playsinline
      controls
    ></video>
    <div class="canvas-caption">
      <strong>Visualizing the Timer-Based Hybrid Supervisor in a Perturbed 2-Obstacle Arena:</strong>
      <strong>Phase 1 (Nominal Forecast):</strong> Using a nominal forward model, the supervisor rolls out a predicted trajectory (green dashed) under active policy $q = 2$ until alternative policy $q = 1$ offers a significantly higher value, locking in a predicted dwell time of $\delta_d = 5$.
      <strong>Phase 2 (Perturbed Execution):</strong> An environmental disturbance shifts the first obstacle. A memoryless agent without a timer (red) chatters across the critical boundary and crashes, whereas our supervisor holds $q = 2$ until the timer reaches $\tau = 5$, safely clearing the obstacle.
      <strong>Phase 3 (Timer Expiration &amp; Re-Forecast):</strong> Once $\tau = \delta_d = 5$ and policy $q = 1$ has higher value at the true state, the supervisor switches to $q^+ = 1$, resets $\tau^+ = 0$, and predicts a new dwell time $\delta_d^+ = 10$ to bypass the second obstacle.
    </div>
  </div>
</div>

At runtime, the closed-loop hybrid system operates through a clean, two-part mechanism:

- **Continuous Execution (Commitment While Timer Runs):**
  The agent applies the locked-in policy $\pi_q$ and increments the timer $\tau$ continuously as long as **either** the adaptive dwell time has not yet elapsed ($\tau \le \delta_d$) **or** the active policy remains optimal under the value functions. This guarantees that measurement noise cannot trigger premature switching while the agent is crossing a vulnerable critical boundary.
- **Predictive Dwell-Time Update at Each Switch:**
  When the dwell timer has expired ($\tau \ge \delta_d$) *and* a better policy $q'$ emerges, the supervisor switches to $q'$, resets the timer ($\tau^+ = 0$), and simulates a short nominal trajectory forward in time (up to a maximum horizon $\bar\delta_d$). The new dwell time $\delta_d^+$ is set to the first predicted time at which another policy beats $q'$ by a **relative value margin of at least $\mu > 0$** (or defaults to $\bar\delta_d$ if no policy beats $q'$ within the horizon).

<div class="callout-box">
  <h4>🔍 Key Theoretical Guarantees (In Plain Language)</h4>
  <ul style="margin-bottom: 0; padding-left: 1.2rem;">
    <li><strong>Guaranteed Chattering Elimination (Non-Zeno Execution):</strong> Because every switch enforces a strictly positive minimum dwell period, the closed-loop hybrid system is provably free of high-frequency chattering and Zeno executions.</li>
    <li><strong>Robust Global Asymptotic Stability (Theorem 1):</strong> When each individual policy stabilizes its target set, the timer-based hybrid supervisor preserves global asymptotic stability of the combined target set and provides a formal robustness margin $\varepsilon > 0$ against measurement noise.</li>
    <li><strong>Principled Tuning of the Horizon $\bar\delta_d$ and Threshold $\mu$:</strong> The paper provides explicit, intuitive bounds showing how to choose the maximum horizon $\bar\delta_d$ (large enough for the system to cross the noise band around $\mathcal{M}^\ast$) and relative margin $\mu$ (large enough to reject value-function fluctuations caused by noise $\varepsilon$).</li>
  </ul>
</div>

---

### 3. Application: Multi-Target Navigation in a Rugged Reward Landscape

To demonstrate the impact of predictive dwell-time supervision in environments where spatial partitioning is impractical, we evaluate a 2D autonomous agent navigating toward four target setpoints across a **rugged, spatially varying reward landscape**:

$$R(z) = -c_1 \lvert z \rvert_{\mathcal{Z}^\ast} \Big(1 + 0.5\sin(c_2 z_x)\cos(c_3 z_y)\Big)$$

Because the periodic peaks and valleys in the reward landscape make straight-line paths suboptimal, the greedy policy regions $Q^\ast(z)$ fracture from clean geometric quadrants (left figure below) into **highly fragmented, non-convex decision islands** (right figure below):

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_qmax_rewarddistTrue.png" title="Optimal Policy Regions Q* Without Periodic Variations" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_qmax_rewarddistFalse.png" title="Optimal Policy Regions Q* With Periodic Variations" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Uniform Distance Reward):</strong> The greedy policy regions $Q^\ast(z)$ form four clean quadrants around the target setpoints (red stars). <strong>Right (Rugged Periodic Reward Landscape):</strong> Local reward peaks and valleys fracture $Q^\ast(z)$ into intricate, non-convex switching boundaries where pre-computing spatial hysteresis partitions is intractable.
</div>

When deployed on this rugged landscape under adversarial measurement noise ($\varepsilon = 0.1$), the three supervisory strategies exhibit dramatically different behavior:

<div class="row mt-4 mb-2">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_valuesims_noise01_stepsahead1rewardmod_False_vanilla.png" title="Greedy Baseline Supervisor Q* under Noise" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_valuesims_noise01_stepsahead20rewardmod_False_fixed.png" title="Fixed-Timer Supervisor under Noise" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_valuesims_noise01_stepsahead20rewardmod_False_hybrid.png" title="Proposed Timer-Based Hybrid Supervisor under Noise" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Closed-loop trajectories (black dashed lines; circles mark initial states, crosses mark terminal states) and target distance over time under measurement noise ($\varepsilon = 0.1$). <strong>Left (Greedy $Q^\ast$):</strong> Severe chattering across fragmented boundaries stalls multiple trajectories far from the targets. <strong>Middle (Fixed-Timer Supervisor, $\delta_d = 0.5$):</strong> Stops rapid chattering, but trajectories starting near $(-0.25, \pm 0.25)$ become trapped bouncing indefinitely between decision regions. <strong>Right (Proposed Timer-Based Hybrid Supervisor):</strong> Adaptively predicts dwell times to eliminate chattering and smoothly guides every initial condition to the target set.
</div>

- **Greedy Supervision Stalls (Left Plot):** Without temporal memory, sensor noise along the fragmented boundaries triggers rapid chattering among the four policies, trapping several trajectories far from the targets.
- **Fixed-Timer Supervision Gets Trapped (Middle Plot):** Holding every policy for a fixed duration ($\delta_d = 0.5$) prevents high-frequency chattering, but because a fixed timer ignores where the trajectory is heading, agents starting near $(-0.25, \pm 0.25)$ repeatedly overshoot into neighboring regions and switch back—getting **trapped in a persistent oscillation**.
- **Predictive Timer Supervision Succeeds (Right Plot):** By forecasting when a competing policy will offer at least a $\mu = 15\%$ relative value gain, our supervisor switches at the right moments and <span class="key-highlight">drives every evaluated initial condition smoothly to the target set</span>.

<div class="callout-box callout-emerald">
  <h4>🛠️ Broader Impact: A Foundation for Robust Commitment in Safety-Critical AI &amp; Robotics</h4>
  <p>
    While the two-target and multi-target navigation examples above provide visual testbeds to analyze fragmented value boundaries, the underlying vulnerability—<strong>indecision along critical boundaries where competing options appear equally optimal</strong>—arises across sequential decision-making for safety-critical applications. Because scaling up to larger models does not eliminate these critical regions in the state space, our methods provide a foundational layer of robustness against perturbation- and adversary-induced deadlocks:
  </p>
  <ul style="margin-bottom: 0; padding-left: 1.2rem;">
    <li><strong><a href="/projects/l_multihyrl_3d_avoidance/">Hysteresis-Based RL (HyRL / MultiHyRL / L-MultiHyRL)</a>:</strong> Uses learned latent features to construct overlapping spatial partitions directly from onboard perception—ideal for 2D/3D obstacle avoidance and $\text{SO}(3)$ attitude control without forward dynamics rollouts.</li>
    <li><strong>Predictive Timer Supervision (TimerHyRL):</strong> Enforces adaptive dwell-time commitment via short-horizon state predictions—ideal for hierarchical multi-policy control, tactical routing, and sequential decision-making where value boundaries are fragmented or unknown in advance.</li>
  </ul>
</div>

For the complete mathematical formulation—including outer semicontinuity of the predictive jump map, non-Zeno hybrid time domains, and the hybrid Lyapunov stability proofs—please refer to our RLC 2025 paper {% cite dePriester2025TimerHyRL %}.
