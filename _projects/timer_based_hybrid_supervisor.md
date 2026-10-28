---
layout: page
title: "Timer-Based Hybrid Supervisor for Policy Switching"
description: "Preventing Rapid Decision Chattering Under Measurement Noise via Predictive Dwell-Time Supervision"
img: assets/img/publication_preview/timerhyrl_timerapproach5.jpg
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

In many complex control and decision-making problems—ranging from **autonomous obstacle avoidance** and **multi-target robotic navigation** to **tactical strategy selection**—an agent must choose among a bank of specialized policies $\Pi = \{\pi_1, \pi_2, \dots, \pi_N\}$, each paired with a learned approximate value function $\hat{V}_q(z)$ for $q \in \mathcal{Q} := \{1, \dots, N\}$.

A natural way to orchestrate these policies is **greedy value-maximizing supervision**, which continuously selects the policy with the highest expected return at the current state:

$$Q^*(z) := \Big\{ q \in \mathcal{Q} : \hat{V}_q(z) = \max_{\bar{q} \in \mathcal{Q}} \hat{V}_{\bar{q}}(z) \Big\}$$

While greedy supervision preserves the stabilizing properties of the individual policies under ideal noise-free conditions, it suffers from a critical vulnerability in the real world: near boundaries where multiple policies appear equally optimal, small measurement noise or adversarial perturbations trick the supervisor into rapidly oscillating between conflicting decisions (**chattering**), resulting in stalls, limit-cycle traps, or collisions.

Existing chattering-mitigation strategies in reinforcement learning face two main limitations:
- **Spatial Partitioning (e.g., Overlapping Hysteresis Regions):** Partitioning the state space into overlapping regions around switching boundaries works well when the geometry of the critical states is structured and known *a priori* (as in our [2D MultiHyRL](/projects/2Dmultihyrl/) and [3D L-MultiHyRL](/projects/l_multihyrl_3d_avoidance/) work), but becomes intractable when decision boundaries are fragmented or unknown in advance.
- **Fixed-Timer Switching:** Enforcing a static minimum waiting time between policy switches creates a rigid dilemma: setting the timer **too short** fails to prevent chattering under measurement noise, while setting it **too long** yields overly conservative behavior that clings to suboptimal policies or traps the system bouncing between decision regions.

In this work, we resolve both limitations by introducing a **predictive timer-based hybrid supervisor**:
> **Rather than relying on prior spatial partitions or a static dwell timer, our supervisor predicts how long to hold the active policy.** Using the learned value functions and a nominal forward model, the supervisor adaptively sets a resettable dwell timer $\delta_d$ at each switch—committing to the active policy until both the timer expires *and* a significantly better alternative emerges.

---

### 1. Why Value-Maximizing Supervisors Chatter Near Critical Areas

Consider a continuous-time system $\dot{z} = f(z, u)$ controlled by selecting a policy $\pi_q \in \Pi$ from a bank of Lipschitz continuous policies, each globally asymptotically stabilizing a target set $\mathcal{Z}_q^*$ with a continuously differentiable neural value approximation $\hat{V}_q(z)$.

Why does selecting $q \in Q^*(z)$ chatter under small measurement noise $m(t)$? Chattering originates around **critical areas** $\mathcal{M}^* \subset \mathcal{Z}$, which we formally characterize by three value-function conditions between competing policies $q, p \in Q^*(z)$ ($q \neq p$):
1. **Equal Optimal Value:** Multiple policies yield the exact same maximal return at state $z$:
   $$\hat{V}_q(z) = \hat{V}_p(z)$$
2. **Local Improvement Under the Chosen Policy:** Following either policy locally improves its own value function along the closed-loop trajectory:
   $$\langle \nabla \hat{V}_q(z), f(z, \pi_q(z)) \rangle > 0 \quad \text{and} \quad \langle \nabla \hat{V}_p(z), f(z, \pi_p(z)) \rangle > 0$$
3. **Local Degradation Under the Opposing Policy:** Applying the competing policy locally *decreases* the other policy's value function:
   $$\langle \nabla \hat{V}_q(z), f(z, \pi_p(z)) \rangle < 0 \quad \text{and} \quad \langle \nabla \hat{V}_p(z), f(z, \pi_q(z)) \rangle < 0$$

Together, these three conditions mean that near $\mathcal{M}^*$, the state space is split into adjacent regions $\mathcal{Z}_q$ and $\mathcal{Z}_p$ where the two policies **pull the system in opposing directions**. When the state $z$ lies within $\delta$ of the boundary $\mathcal{M}^*$, a small sensor perturbation $m$ of magnitude $\varepsilon \ge \delta$ shifts the measured state $z + m$ across the boundary into $\mathcal{Z}_p$. Selecting $\pi_p$ then drives the true state $z$ back toward the boundary $\mathcal{M}^*$, making the critical area *attractive* under alternating noise and trapping the system in high-frequency chattering.

#### Motivating Example: Stabilizing Two Disconnected Setpoints on a Line
To see this mechanism clearly, consider a 1D system $\dot{z} = u \in [-1, 1]$ tasked with stabilizing two disconnected setpoints $\mathcal{Z}^* = \{z_1^*, z_2^*\} = \{-1, +1\}$ using two policies $\pi_1(z) = z_1^* - z$ and $\pi_2(z) = z_2^* - z$.

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_valuefunction.png" title="Approximate Value Functions V1 and V2" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/twoline_policy.png" title="Piecewise Control Policy under Greedy Supervisor Q*" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left:</strong> Approximate value functions $\hat{V}_1$ (blue) and $\hat{V}_2$ (green) for policies $\pi_1$ and $\pi_2$, with setpoints $\mathcal{Z}^* = \{-1, +1\}$ marked by red stars. <strong>Right:</strong> Resulting piecewise-continuous control input $u$ under the greedy supervisor $Q^*(z)$.
</div>

Inspecting the value functions and resulting control law above reveals two distinct types of switching boundaries:
- **Exploitative Switching at $z \approx \pm 1.6$ (Benign):** Far from the origin ($z < -1.6$ or $z > 1.6$), $Q^*(z)$ temporarily selects the *opposing* policy ($\pi_2$ for $z < -1.6$, and $\pi_1$ for $z > 1.6$) because it produces a larger control input in the same direction toward $\mathcal{Z}^*$, accelerating convergence. Because both policies have the same sign here, chattering does not stall the system.
- **Critical State at $z_c = 0$ (Catastrophic Chattering):** At $z_c = 0$, $\hat{V}_1(0) = \hat{V}_2(0)$, while $\pi_1(0) = -1 < 0$ and $\pi_2(0) = +1 > 0$ oppose each other. Under bounded measurement noise $m(t)$ of magnitude $\varepsilon = 0.3$ (middle panel below), $Q^*(z + m)$ chatters rapidly between $q=1$ and $q=2$, trapping trajectories at $z = 0$ and preventing convergence to $\mathcal{Z}^*$.

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
  <strong>Left ($\varepsilon = 0$, Greedy $Q^*$):</strong> Without noise, all trajectories converge to $\mathcal{Z}^* = \{-1, +1\}$. <strong>Middle ($\varepsilon = 0.3$, Greedy $Q^*$):</strong> Bounded measurement noise causes trajectories starting near $z_c = 0$ to chatter around the origin and fail to stabilize. <strong>Right ($\varepsilon = 0.3$, Proposed Timer-Based Hybrid Supervisor):</strong> With predictive dwell-time supervision ($\mu = 0.1, \bar{\delta}_d = 0.5$), chattering is eliminated both near $z_c = 0$ and near $z = \pm 1.5$, restoring smooth convergence.
</div>

---

### 2. How the Predictive Timer-Based Hybrid Supervisor Works

To prevent chattering without requiring prior spatial partitions of $\mathcal{M}^*$, we augment the system state with three supervisory variables to form the hybrid state $x := (z, \tau, q, \delta_d) \in \mathcal{X}$:
1. **Timer $\tau \in \mathbb{R}_{\ge 0}$:** Counts the elapsed time since the most recent policy switch ($\dot{\tau} = 1$).
2. **Active Policy Index $q \in \mathcal{Q}$:** Locks in the currently deployed policy $\pi_q$ during continuous flow ($\dot{q} = 0$).
3. **Adaptive Dwell Time $\delta_d \in \mathbb{R}_{> 0}$:** Specifies the minimum time that must elapse before the supervisor is allowed to switch policies ($\dot{\delta}_d = 0$).

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
      <strong>Phase 1 (Nominal Forecast at $\tau = 0, q = 2$):</strong> Using a noise-free model, the supervisor propagates a nominal state forecast $\chi(\eta)$ (green dashed) under active policy $q = 2$ until alternative policy $q = 1$ becomes significantly better ($V_1(\chi(5)) > V_2(\chi(5))$), locking in a predicted dwell time of $\delta_d = 5$.
      <strong>Phase 2 (Perturbed Flow while $\tau < \delta_d = 5$):</strong> An environmental shift perturbs the first obstacle. A memoryless agent without a timer (red) chatters across the critical boundary and crashes, whereas our supervisor holds $q = 2$ until $\tau = 5$, safely clearing the obstacle to reach actual state $z(5)$.
      <strong>Phase 3 (Timer Expiration $\tau = 5 \to$ Switch &amp; Re-Forecast):</strong> Once $\tau = \delta_d = 5$ and $V_1(z(5)) > V_2(z(5))$, the supervisor jumps to $q^+ = 1$, resets $\tau^+ = 0$, and predicts a new dwell time $\delta_d^+ = 10$ from $z(5)$ to safely bypass the second obstacle.
    </div>
  </div>
</div>

#### The Closed-Loop Hybrid System $\mathcal{H} = (C, F, D, G)$
The interaction between continuous control execution and discrete policy switching is governed by the hybrid system $\mathcal{H}$:

- **Continuous Flow ($x \in C := C_0 \cup C_1$):**
  While the state evolves continuously under the active policy $\dot{z} = f(z, \pi_q(z))$ and the timer increments at unit rate $\dot{\tau} = 1$, the supervisor keeps $q$ and $\delta_d$ constant as long as **either** the dwell timer has not yet expired **or** the active policy is still optimal:
  $$C_0 := \{x \in \mathcal{X} : \tau \le \delta_d\}, \qquad C_1 := \{x \in \mathcal{X} : q \in Q^*(z)\}$$
- **Discrete Policy Switch ($x \in D$):**
  A policy switch is triggered **only when both** the dwell timer has expired **and** the active policy is no longer optimal:
  $$D := \big\{x \in \mathcal{X} : \tau \ge \delta_d,\; q \in \mathcal{Q} \setminus Q^*(z)\big\}$$
- **Predictive Dwell-Time Update ($x^+ \in G(x)$):**
  At a jump, the physical state remains unchanged ($z^+ = z$), the timer resets ($\tau^+ = 0$), the supervisor selects a newly optimal policy $q^+ = q' \in Q^*(z)$, and the new dwell time $\delta_d^+$ is computed via forward prediction:
  $$\delta_d^+ = \min\Big(\mathcal{T}(z, q') \cup \{\bar{\delta}_d\}\Big)$$
  where $\mathcal{T}(z, q')$ collects all future horizons $\eta \in [0, \bar{\delta}_d]$ along the nominal noise-free trajectory $\dot{\chi} = f(\chi, \pi_{q'}(\chi))$ (initialized at $\chi(0) = z$) at which a competing policy $\bar{q} \neq q'$ achieves a **significant relative value improvement** of at least $\mu > 0$:
  $$\mathcal{T}(z, q') := \left\{ \eta \in [0, \bar{\delta}_d] : \frac{\max_{\bar{q} \in \mathcal{Q} \setminus \{q'\}} \hat{V}_{\bar{q}}\big(\chi(\eta)\big) - \hat{V}_{q'}\big(\chi(\eta)\big)}{\big\lvert \hat{V}_{q'}\big(\chi(\eta)\big) \big\rvert + \epsilon} \ge \mu \right\}$$

Crucially, normalizing by $\lvert \hat{V}_{q'}(\chi(\eta)) \rvert + \epsilon$ (with $0 < \epsilon \ll 1$ preventing division by zero) ensures that $\mu > 0$ acts as a consistent **fractional improvement threshold** whether the value functions are positive or negative. If no alternative policy beats $q'$ by a relative margin of $\mu$ within the horizon $\bar{\delta}_d$ (i.e., $\mathcal{T}(z, q') = \varnothing$), the dwell time defaults to the upper bound $\bar{\delta}_d$.

<div class="callout-box">
  <h4>🔍 Formal Guarantees &amp; Principled Parameter Selection</h4>
  <ul style="margin-bottom: 0; padding-left: 1.2rem;">
    <li><strong>Well-Posed &amp; Non-Zeno Execution (Propositions 1 &amp; 2):</strong> Under continuous $\hat{V}_q$ and Lipschitz continuous $f$ and $\pi_q$, the hybrid closed-loop system $\mathcal{H}$ satisfies the <em>Hybrid Basic Conditions</em>. Every maximal solution is complete, non-Zeno, and exhibits jump times separated by a uniform minimum dwell period $t_{j+1} - t_j \ge \beta > 0$.</li>
    <li><strong>Stability Preservation &amp; Chattering Robustness (Theorem 1):</strong> When each $\pi_q$ globally asymptotically stabilizes $\mathcal{Z}_q^*$, $\mathcal{H}$ renders the composite target set $\mathcal{Z}^* := \bigcup_{q \in \mathcal{Q}} \mathcal{Z}_q^*$ <strong>robustly globally asymptotically stable</strong> and guarantees a nonzero robustness margin $\varepsilon > 0$ against measurement noise.</li>
    <li><strong>Principled Tuning of $\bar{\delta}_d$ and $\mu$ (Section 3.3):</strong>
      To guarantee that the state escapes the $\varepsilon$-neighborhood of critical areas before reconsidering a switch, choose the maximum prediction horizon $\bar{\delta}_d$ and relative threshold $\mu$ to exceed the worst-case noise perturbations:
      $$\bar{\delta}_d > \frac{\varepsilon}{\max_{(z,u) \in X} \lvert f(z,u) \rvert}, \qquad \mu > \frac{\varepsilon L_{\hat{V}}}{\lvert \hat{V}_{\text{ref}} \rvert}$$
      where $L_{\hat{V}} := \max_{q \in \mathcal{Q}} L_{\hat{V}_q}$ is the Lipschitz constant of the value networks and $\lvert \hat{V}_{\text{ref}} \rvert$ is a characteristic value scale on the operational domain $X$.
    </li>
  </ul>
</div>

---

### 3. Multi-Target Navigation in a Rugged 2D Reward Landscape

To test our supervisor in a setting where spatial state-space partitioning is difficult to apply, we consider a 2D planar system $\dot{z} = u \in [-1, 1]^2$ with state $z = (z_x, z_y) \in \mathbb{R}^2$ tasked with stabilizing four disconnected setpoints $\mathcal{Z}^* = \{z_1^*, z_2^*, z_3^*, z_4^*\}$ using four policies $\pi_1, \dots, \pi_4$. We train the approximate value functions $\hat{V}_1, \dots, \hat{V}_4$ on a reward landscape featuring periodic spatial variations:

$$R(z) = -c_1 \lvert z \rvert_{\mathcal{Z}^*} \left(1 + \frac{1}{2}\sin(c_2 z_x)\cos(c_3 z_y)\right)$$

These periodic variations create local minima and maxima across the plane, meaning that following the shortest Euclidean path to a setpoint is no longer optimal. As shown below, this transforms the greedy policy regions $Q^*(z)$ from clean geometric partitions (left) into **highly fragmented, non-convex decision islands** (right):

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_qmax_rewarddistTrue.png" title="Optimal Policy Regions Q* Without Periodic Variations" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/timerhyrl/multitargs_qmax_rewarddistFalse.png" title="Optimal Policy Regions Q* With Periodic Variations" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Without Periodic Variations):</strong> The value-maximizing regions $Q^*(z)$ form clean, predictable partitions around the four setpoints (red stars). <strong>Right (With Periodic Variations):</strong> Local reward peaks and valleys fracture $Q^*(z)$ into intricate, non-convex switching boundaries where pre-computing overlapping spatial partitions is nontrivial.
</div>

We evaluate closed-loop trajectories on this rugged landscape under adversarial measurement noise $m(t) = [\varepsilon, -\varepsilon]^\top m_{\text{sgn}}(t)$ of magnitude $\varepsilon = 0.1$ across three supervisory strategies:

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
  Closed-loop trajectories (black dashed lines; circles mark initial states, crosses mark terminal states) and distance to target $\lvert z^* - z \rvert$ over time under measurement noise ($\varepsilon = 0.1$). <strong>Left (Greedy $Q^*$):</strong> Severe chattering across fragmented boundaries prevents multiple trajectories from reaching $\mathcal{Z}^*$. <strong>Middle (Fixed-Timer Supervisor, $\delta_d \equiv 0.5$):</strong> Prevents rapid chattering, but trajectories near $(-0.25, \pm 0.25)$ become trapped bouncing indefinitely between decision regions. <strong>Right (Proposed Timer-Based Hybrid Supervisor, $\bar{\delta}_d = 0.5, \mu = 0.15$):</strong> Adaptively predicts dwell times to eliminate chattering and smoothly guides all initial conditions to $\mathcal{Z}^*$.
</div>

- **Failure of Greedy Supervision (Left Plot):** Without temporal protection, measurement noise along the fragmented boundaries triggers rapid switching among $q \in \{1, 2, 3, 4\}$, stalling several trajectories far from the target setpoints.
- **Why a Fixed Timer is Not Enough (Middle Plot):** Imposing a constant dwell time of $\delta_d \equiv 0.5$ eliminates high-frequency chattering, but because the timer ignores where the trajectory is heading, solutions starting near $(-0.25, 0.25)$ and $(-0.25, -0.25)$ repeatedly overshoot into neighboring regions and switch back—getting **trapped in a persistent oscillation** and failing to converge.
- **Success of Predictive Dwell-Time Supervision (Right Plot):** By forecasting when a competing policy will offer at least a $\mu = 15\%$ relative value gain, our timer-based hybrid supervisor switches at the right moments and <span class="key-highlight">drives every evaluated initial condition smoothly to the target set $\mathcal{Z}^*$</span>.

<div class="callout-box callout-emerald">
  <h4>🛠️ Practical Engineering Takeaway: When to Use Timer Supervision vs. Spatial Hysteresis</h4>
  <p>
    Across our hybrid RL framework, <strong>spatial hysteresis</strong> (such as <a href="/projects/2Dmultihyrl/">2D MultiHyRL</a> and <a href="/projects/l_multihyrl_3d_avoidance/">3D L-MultiHyRL</a>) and <strong>predictive timer supervision</strong> offer complementary tools for chatter-free autonomy. When a single dynamics model is unknown or expensive to roll out online—such as vision-driven 3D flight—spatial hysteresis over learned CNN features provides zero-rollout switching. Conversely, when an agent switches among <strong>arbitrary learned policies with complex, fragmented value boundaries</strong> where critical regions cannot be partitioned in advance, our <strong>Timer-Based Hybrid Supervisor</strong> guarantees chatter-free stability using only value evaluations along short forward predictions.
  </p>
</div>

For the complete mathematical proofs on outer semicontinuity of the predictive jump map, non-Zeno hybrid time domains, and the hybrid Lyapunov stability analysis, please refer to our RLC 2025 paper {% cite dePriester2025TimerHyRL %}.
