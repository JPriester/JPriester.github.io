---
layout: page
title: "Hysteresis-Based Reinforcement Learning for Chatter-Free Autonomy"
description: "Eliminating Structural Indecision and Adversarial Chattering in Safety-Critical Sequential Decision-Making (HyRL, MultiHyRL & L-MultiHyRL)"
img: assets/img/publication_preview/lmultihyrl_preview.png
video: /assets/video/LMultiHyRLHysteresisLogicLight.mp4
importance: 1
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Research Overview: HyRL (ACC 2022) • MultiHyRL (RLC 2024) • L-MultiHyRL (2026)</h4>
  <p>
    This article provides a unified overview of our line of research on <strong>Hysteresis-Based Reinforcement Learning</strong>—spanning our published foundational algorithms in <strong>HyRL (ACC 2022)</strong> {% cite dePriester2022ACC %} and <strong>MultiHyRL (RLC 2024)</strong> {% cite dePriester2024MultiHyRL %}, and highlighting simulation and physical quadrotor flight results from our latest 3D extension, <strong>L-MultiHyRL</strong> {% cite dePriester2026LMultiHyRL %} <em>(manuscript in preparation; full algorithmic details to appear upon publication)</em>:
    <a href="{{ '/assets/pdf/hyrlpaper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 6px;">[ACC 2022 PDF →]</a>
    <a href="{{ '/assets/pdf/MultihyrlPaper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 8px;">[RLC 2024 PDF →]</a>
  </p>
</div>

In **sequential decision-making for safety-critical applications**, an autonomous agent must frequently choose between multiple equally viable options—whether selecting a trajectory around an obstacle, choosing a rotation direction, or committing to one of several competing tasks. Because standard continuous and discrete reinforcement learning (RL) policies are **memoryless mappings** from observations to actions, these choices create **critical regions of indecision** ($\mathcal{M}^\ast$) in the state space where opposing decisions diverge.

Near these critical boundaries, small sensor noise or an adversary can easily perturb the agent's observations to make an alternative option appear momentarily better—causing the policy to rapidly oscillate back and forth (**chattering**), stall in a deadlock, or crash. Crucially, <span class="key-highlight">scaling up to larger neural networks and more advanced AI models does not eliminate this vulnerability</span>: in higher-dimensional state spaces, critical decision boundaries persist across the state space in far more places than expected.

**Autonomous obstacle avoidance and attitude control** provide a clear, visual setting where this structural failure mode appears directly. Whenever a vehicle approaches an obstacle on its way to a target, it must commit to a bypass route (passing above or below in 2D, or maneuvering in 3D space). Along the critical boundary $\mathcal{M}^\ast$ ahead of the obstacle, small adversarial observation shifts move the perceived obstacle into the vehicle's active passage direction—making the opposing route appear shorter and causing a memoryless agent to chatter until it collides head-on.

<div class="interactive-lab-card">
  <div class="direction-canvas-wrap">
    <div class="canvas-header">
      <span class="canvas-title">Animation 1: Why Standard Memoryless RL Policies Chatter &amp; Crash</span>
      <span class="canvas-live-dot" style="color: #dc2626; background: #fef2f2; border-color: #fecaca;">● Baseline Failure Case</span>
    </div>
    <video
      src="{{ '/assets/video/LMultiHyRLHysteresisLight.mp4' | relative_url }}"
      autoplay
      loop
      muted
      playsinline
      controls
    ></video>
    <div class="canvas-caption">
      <strong>Topological Chattering Under Adversarial Obstacle Shifts:</strong>
      <strong>Phase 1 (Vehicle Tries Upper Route $\rightarrow$ Obstacle Moves Up):</strong> As the vehicle steers to pass above the obstacle (blue dashed intended route), an adversarial observation perturbation shifts the perceived obstacle upward into its passage direction.
      <strong>Phase 2 (Vehicle Switches to Lower Route $\rightarrow$ Obstacle Moves Down):</strong> Blocked above—and seeing a shorter path below—the memoryless policy reverses to the lower route (green dashed intended route), where an opposing downward shift blocks it again.
      <strong>Phase 3 (Repeated Route Chattering &amp; Crash):</strong> The vehicle and perturbed obstacle alternate directions across five rapid route reversals until the vehicle crashes head-on into the obstacle.
    </div>
  </div>
</div>

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-10">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/rlc24_fig1_bev_setting.png" title="Overview of the Bird's-Eye View Obstacle Avoidance Setting (RLC 2024)" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Topological Chattering in Obstacle Avoidance (Figure 1 of {% cite dePriester2024MultiHyRL %}):</strong> <strong>Left:</strong> A vehicle (orange triangle) observes a local bird's-eye view window (yellow square) while navigating past an obstacle toward a target. Above or below the red dashed decision boundary, the RL policy steers along smooth bypass trajectories (blue and green). Near the boundary, small measurement noise induces rapid switching (red chattering trajectory) and collision. <strong>Right:</strong> Vehicle orientation $\xi$ and relative target tracking errors $e_p$ and $e\sb{\theta}$.
</div>

Across **HyRL** {% cite dePriester2022ACC %}, **MultiHyRL** {% cite dePriester2024MultiHyRL %}, and **L-MultiHyRL** {% cite dePriester2026LMultiHyRL %}, our core insight is simple:
> **Continuous neural networks cannot robustly break symmetry around obstacles without discrete memory.** By partitioning the state space into overlapping regions and embedding specialized RL policies inside a **closed-loop hybrid system with hysteresis switching**, **Hysteresis-Based Reinforcement Learning** eliminates decision chattering and guarantees decisive, collision-free obstacle avoidance under worst-case observation noise.

---

### 1. How Hysteresis-Based RL Works: Overlapping Partitions &amp; Hysteresis Logic

How do we systematically eliminate topological chattering without hand-crafting avoidance controllers? Beginning with our **ACC 2022** {% cite dePriester2022ACC %} and **RLC 2024** {% cite dePriester2024MultiHyRL %} papers, we established a general **hysteresis-based reinforcement learning** framework built on three steps:

1. **Empirical Critical Point Identification:**
   Starting from a single baseline RL policy, we probe the environment with small opposing state perturbations $\pm \nu$. States from which perturbed rollouts split around opposite sides of an obstacle reveal the baseline policy's vulnerable critical region $\mathcal{M}^\ast$ (panel a below).
2. **Overlapping State-Space Partitioning:**
   We bisect the critical region $\mathcal{M}^\ast$ into two domains $\mathcal{M}\sb{1}$ (e.g., an **Upper Partition**) and $\mathcal{M}\sb{2}$ (e.g., a **Lower Partition**) and expand each across the centerline boundary by a margin $\rho$ to create an **overlapping hysteresis region** ($\mathcal{M}\sb{1}^{\text{exp}}$ intersecting $\mathcal{M}\sb{2}^{\text{exp}}$) whose width exceeds the worst-case sensor or adversarial perturbation (panels b and c below).
3. **Dedicated Policies &amp; Hysteresis Switching Logic:**
   Two specialized RL policies are trained on the overlapping domains—one committed to passing each side of the obstacle. A discrete logic state locks in the initialized policy inside the overlap and **ignores perturbation-induced shortcuts**, only permitting a switch if the vehicle moves completely outside the active policy's expanded partition.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/overview_critpoints_hysteresispolicies.png" title="Critical Point Identification and Overlapping Hysteresis Partitions" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Constructing Overlapping Hysteresis Regions:</strong> <strong>(a)</strong> Small opposing perturbations $z_0 \pm \nu$ cause baseline trajectories $\phi_1$ and $\phi_2$ to diverge around the obstacle, identifying critical points. <strong>(b)</strong> The empirical critical points $\mathcal{M}^\ast$ (red dots) are bisected into $\mathcal{M}\sb{1}$ and $\mathcal{M}\sb{2}$ and expanded by $\rho_1$ to create an overlap buffer. <strong>(c)</strong> Inside the overlap between $\mathcal{M}\sb{1}^{\text{exp}}$ and $\mathcal{M}\sb{2}^{\text{exp}}$, both specialized policies safely route the vehicle around opposite sides of the obstacle without chattering.
</div>

<div class="interactive-lab-card">
  <div class="direction-canvas-wrap">
    <div class="canvas-header">
      <span class="canvas-title">Animation 2: How Overlapping Partitions &amp; Hysteresis Lock In the Active Route</span>
      <span class="canvas-live-dot">● Hysteresis Switching Logic</span>
    </div>
    <video
      src="{{ '/assets/video/LMultiHyRLHysteresisLogicLight.mp4' | relative_url }}"
      autoplay
      loop
      muted
      playsinline
      controls
    ></video>
    <div class="canvas-caption">
      <strong>Eliminating Route Chattering via Overlapping Spatial Partitions:</strong>
      <strong>Phase 1 (Overlapping Spatial Partitions Create Hysteresis):</strong> The <strong>Upper Partition</strong> (blue) and <strong>Lower Partition</strong> (green) extend across the centerline to form a central <strong>Overlapping Region</strong> where both bypass routes are valid.
      <strong>Phase 2 (Initialized Upper Route Commits Despite Upward Shift):</strong> When initialized to the Upper Route policy (blue), the obstacle shifts upward—making the lower path geometrically shorter (red dashed curve). Because the vehicle remains inside the Upper Partition, it ignores the shorter route and commits to the upper trajectory with <strong>0 route reversals</strong>.
      <strong>Phase 3 (Initialized Lower Route Commits Despite Downward Shift):</strong> Symmetrically, when initialized to the Lower Route policy (green) and confronted with a downward obstacle shift, the vehicle stays inside the Lower Partition and commits smoothly beneath the obstacle with <strong>0 route reversals</strong>.
    </div>
  </div>
</div>

#### Scaling from Single-Obstacle State Feedback to Multi-Obstacle Visual Perception
- **HyRL (ACC 2022)** {% cite dePriester2022ACC %} introduced this hysteresis-based RL architecture for single-obstacle avoidance using state coordinates (below left), proving that overlapping training sets prevent adversarial chattering along $\mathcal{M}^\ast$.
- **MultiHyRL (RLC 2024)** {% cite dePriester2024MultiHyRL %} generalized the framework to **cluttered multi-obstacle 2D arenas** and **local bird's-eye-view visual observations** (below right), combining a CNN-MLP perception architecture with directional hysteresis and obstacle-focus switching across relaxed Voronoi cells.

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/acc22_fig5_hyrl_obstacle.png" title="HyRL Single-Obstacle Avoidance Trajectories (ACC 2022, Fig. 5)" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/rlc24_fig2_architecture.png" title="MultiHyRL Closed-Loop Control Architecture (RLC 2024, Fig. 2)" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (HyRL — ACC 2022, Figure 5 {% cite dePriester2022ACC %}):</strong> Under measurement noise ($\epsilon = 0.1$), a standard DQN policy (red dashed) chatters along $y = 0$ and crashes into the obstacle $\mathcal{O}$, whereas HyRL (blue solid) commits to passing above ($q_0 = 0$) or below ($q_0 = 1$). <strong>Right (MultiHyRL — RLC 2024, Figure 2 {% cite dePriester2024MultiHyRL %}):</strong> Closed-loop control architecture combining local bird's-eye view CNN features with relative target error MLP features.
</div>

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/rlc24_fig5_voronoi_partitions.png" title="Multi-Obstacle Relaxed Voronoi Partitions (RLC 2024, Fig. 5)" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Multi-Obstacle State-Space Partitioning (MultiHyRL — RLC 2024, Figure 5 {% cite dePriester2024MultiHyRL %}):</strong> Relaxed Voronoi partitioning across four obstacles ($1, 2, 3, 4$), where blue and green regions denote clockwise and counterclockwise bypass domains and the red region denotes their hysteresis overlap around each obstacle and the target $p^\ast$.
</div>

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se2_overlay_case3_2d.png" title="2D Multi-Obstacle Avoidance Under Adversarial Perturbations" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se2_combined_cumulative_performance.png" title="2D Cumulative Success Rate Across Perturbation Magnitudes" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (2D Multi-Obstacle Trajectories Under Adversarial Noise, $\varepsilon_p = 0.20$):</strong> Standard continuous PPO (red dashed) chatters and collides with the obstacles (black circles), while hybrid hysteresis switching (blue and green trajectories) routes all initial conditions safely to the target (gold star). <strong>Right (2D Cumulative Success Rate Across 1,000 Runs):</strong> As perturbation magnitude increases to $\varepsilon_p = 0.20$, Standard PPO (solid lines) drops below <strong>25%</strong> success, whereas our hybrid hysteresis framework (dashed lines) achieves <strong>100% collision-free convergence</strong>.
</div>

---

### 2. Extending to 3D Flight &amp; Attitude Tracking: L-MultiHyRL Results (2026)

In our latest work on **Latent Multi-Policy Hysteresis-Based Reinforcement Learning (L-MultiHyRL)** {% cite dePriester2026LMultiHyRL %}, we extend hysteresis-based reinforcement learning from 2D planar navigation to **full 3D aerial flight in $\text{SE}(3)$ and rotational attitude tracking in $\text{SO}(3)$** using local onboard visual observations.

In 3D flight, a vehicle faces **two simultaneous topological obstructions**:
1. **3D Spatial Obstacle Avoidance:** Breaking symmetry around 3D obstacles in cluttered environments under position observation noise $\varepsilon_p$.
2. **3D Attitude Tracking on $\text{SO}(3)$:** Because antipodal unit quaternions $\pm q_e$ represent the same physical 3D orientation via opposite rotation directions, continuous policies encounter a critical switching boundary at a $180^\circ$ rotation error ($\eta_e = 0$), where attitude noise $\varepsilon_q$ induces violent angular velocity chattering.

*(Because the L-MultiHyRL manuscript is currently under review, we omit the internal algorithmic and architectural details here and highlight the 3D simulation and physical hardware validation results below.)*

#### 3D Simulation Benchmark: Single- &amp; Multi-Obstacle Navigation
Across 1,000 randomized 3D initial conditions under worst-case adversarial observation shifts ($\varepsilon_p = 0.20\text{ m}$) and attitude disturbances ($\varepsilon_q = 0.4$), **Standard PPO** suffers from severe chattering—colliding with obstacles in 3D space and stalling at unaligned orientations near $\eta_e = 0$. In contrast, **L-MultiHyRL** achieves **100.0% collision-free convergence** across every tested noise level:

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_normal_attitude_chattering_delta00.png" title="Standard RL Policy Attitude Chattering at the Equator" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_hybrid_attitude_chattering_delta02.png" title="L-MultiHyRL Chatter-Free Attitude Convergence" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>3D Attitude Tracking Under Adversarial Noise ($\varepsilon_q = 0.4$):</strong> <strong>Left (Standard PPO):</strong> Initial orientations near the antipodal boundary ($\eta_e(0)$ near $0$) are trapped at $\eta_e = 0$ as angular velocity commands $u_y$ and $u_z$ chatter at maximum frequency. <strong>Right (L-MultiHyRL):</strong> Hybrid hysteresis eliminates control chattering and smoothly stabilizes all initial orientations.
</div>

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_overlay_case3_np0_20_na0_00_2way_1_2d_projections.png" title="Multi-Obstacle 3D Simulation Trajectories Under Adversarial Noise" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>3D Multi-Obstacle Navigation Under Adversarial Observation Perturbations ($\varepsilon_p = 0.20\text{ m}$):</strong> Top-down ($p_x$-$p_y$, left) and side ($p_x$-$p_z$, right) projections. Standard PPO (red dashed) chatters and collides with the spherical obstacles (black circles), while L-MultiHyRL smoothly routes all trajectories to the target (gold star) via committed bypass modes (blue and green).
</div>

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-10">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_combined_cumulative_performance_jax_2way.png" title="3D Cumulative Success Rate Across Perturbation Magnitudes" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>3D Cumulative Success Rate Over Time (1,000 Runs per Condition):</strong> As the adversarial perturbation magnitude increases from $\varepsilon_p = 0.00$ to $\varepsilon_p = 0.20$, Standard PPO (solid curves) degrades to <strong>60.4%</strong> success in the single-obstacle arena and <strong>68.6%</strong> in the multi-obstacle arena. <strong>L-MultiHyRL (dashed curves) maintains 100.0% collision-free convergence across all noise levels.</strong>
</div>

---

### 3. Physical Flight Validation on a ModalAI Sentinel Quadrotor

To verify that these robustness gains transfer to real-world aerial hardware under aerodynamic disturbances and closed-loop tracking delays, we deployed the **simulation-derived closed-loop hybrid system**—consisting of the trained local policies, latent partition classifier, and discrete hysteresis logic—directly onboard a physical **ModalAI Sentinel quadrotor** (VOXL 2 companion computer running at 25 Hz over ROS 2 / PX4) inside a motion-capture flight arena without retraining or hardware-specific fine-tuning.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_experiment_overlay_multi_obs_np0_25_na0_00_2way_1_2d_projections.png" title="Hardware Flight Trajectories on the ModalAI Sentinel Quadrotor" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Physical Quadrotor Multi-Obstacle Flights Under Perturbations ($\varepsilon_p = 0.25\text{ m}$):</strong> Top-down ($p_x$-$p_y$, left) and side ($p_x$-$p_z$, right) flight paths recorded on the ModalAI Sentinel quadrotor. Standard PPO (red) suffers from mid-flight indecision and collides with the obstacles (black circles), whereas L-MultiHyRL (blue and green) executes decisive, collision-free 3D avoidance maneuvers in every flight trial.
</div>

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_experiment_normal_attitude_np0_00_na0_40_attitude_chattering.png" title="Hardware Attitude Chattering Under Standard PPO" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_experiment_hybrid_attitude_np0_00_na0_40_attitude_chattering.png" title="Hardware Chatter-Free Attitude Convergence Under L-MultiHyRL" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Physical Quadrotor Attitude Tracking Under Perturbations ($\varepsilon_q = 0.4$):</strong> <strong>Left (Standard PPO):</strong> Initial orientations near the antipodal boundary trigger severe hardware angular rate chattering and stall near $\eta_e = 0$. <strong>Right (L-MultiHyRL):</strong> Hybrid hysteresis eliminates chattering on the physical quadrotor and drives all trajectories smoothly to the desired orientation.
</div>

<div class="callout-box callout-emerald">
  <h4>🛠️ Broader Impact: A Foundation for Robustness in Modern AI &amp; Robotics</h4>
  <p>
    Critical regions of indecision are not limited to obstacle avoidance—they arise whenever a continuous or discrete policy must choose between equally viable options. Left unaddressed, these boundaries can be easily exploited by small perturbations or an adversary to trigger deadlocks or crashes. Crucially, <strong>scaling up to larger neural networks and more advanced AI models does not eliminate this vulnerability</strong>: in higher-dimensional state spaces, critical decision boundaries persist in far more places than expected. From <strong>2D state-based HyRL (ACC 2022)</strong> {% cite dePriester2022ACC %} and <strong>2D vision-based MultiHyRL (RLC 2024)</strong> {% cite dePriester2024MultiHyRL %} to <strong>3D L-MultiHyRL (2026)</strong> {% cite dePriester2026LMultiHyRL %}, our methods provide a foundational layer of robustness against these structural failure modes across robotics and sequential decision-making for safety-critical applications.
  </p>
</div>
