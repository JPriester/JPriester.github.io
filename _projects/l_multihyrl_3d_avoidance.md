---
layout: page
title: "Hysteresis-Based Hybrid RL for 2D & 3D Obstacle Avoidance"
description: "Overcoming Topological Chattering in Autonomous Navigation Across HyRL, MultiHyRL, and L-MultiHyRL"
img: assets/img/publication_preview/lmultihyrl_preview.png
importance: 1
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Research Overview: HyRL (ACC 2022) • MultiHyRL (RLC 2024) • L-MultiHyRL (2026)</h4>
  <p>
    This article provides a unified overview of our line of research on <strong>Hysteresis-Based Hybrid Reinforcement Learning</strong> for robust obstacle avoidance—spanning our published foundational algorithms in <strong>HyRL (ACC 2022)</strong> {% cite dePriester2022ACC %} and <strong>MultiHyRL (RLC 2024)</strong> {% cite dePriester2024MultiHyRL %}, and highlighting simulation and physical quadrotor flight results from our latest extension to 3D aerial navigation, <strong>L-MultiHyRL</strong> {% cite dePriester2026LMultiHyRL %} <em>(manuscript under review; full algorithmic details to appear upon publication)</em>:
    <a href="{{ '/assets/pdf/hyrlpaper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 6px;">[ACC 2022 PDF →]</a>
    <a href="{{ '/assets/pdf/MultihyrlPaper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 8px;">[RLC 2024 PDF →]</a>
  </p>
</div>

Deep reinforcement learning (RL) enables agile autonomous navigation directly from onboard sensors. However, standard RL policies parameterize **continuous, memoryless mappings** from observations to actions—a fundamental limitation that makes them vulnerable to **topological obstructions** in safety-critical obstacle avoidance.

Whenever an autonomous vehicle approaches an obstacle on its way to a target, it must break symmetry and commit to a bypass route (for example, passing left or right in 2D, or maneuvering in 3D space). Because the policy is continuous, this directional choice creates a vulnerable **set of critical points** $\mathcal{M}^*$ directly ahead of the obstacle where opposing trajectories diverge. Along this boundary, small sensor noise or adversarial perturbations repeatedly flip the policy's decision—causing the vehicle to **chatter** rapidly between conflicting commands and collide head-on with the obstacle.

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/slide_bev_chattering.png" title="Topological Chattering Along the Critical Line in Local Bird's-Eye View" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/slide_bev_overlap_cones.png" title="Overlapping Hysteresis Domains Around an Obstacle" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Chattering Along Critical Points):</strong> When a vehicle approaches an obstacle along the critical dividing line (red dashed), small observation noise causes a memoryless policy to oscillate between passing left (blue) and passing right (green), crashing into the obstacle. <strong>Right (Hysteresis Across Overlapping Domains):</strong> Partitioning the space around the obstacle into overlapping regions allows a hybrid supervisor to lock in a dedicated bypass policy inside the overlap region.
</div>

Across **HyRL** {% cite dePriester2022ACC %}, **MultiHyRL** {% cite dePriester2024MultiHyRL %}, and **L-MultiHyRL** {% cite dePriester2026LMultiHyRL %}, our core insight is simple:
> **Continuous neural networks cannot robustly break symmetry around obstacles without discrete memory.** Embedding reinforcement learning policies inside a **closed-loop hybrid system with hysteresis switching** eliminates decision chattering and guarantees decisive, collision-free obstacle avoidance under worst-case observation noise.

---

### 1. Foundations in 2D Navigation: HyRL (ACC 2022) &amp; MultiHyRL (RLC 2024)

How do we systematically eliminate topological chattering without hand-crafting avoidance controllers? In our **ACC 2022** {% cite dePriester2022ACC %} and **RLC 2024** {% cite dePriester2024MultiHyRL %} papers, we established a general hybrid reinforcement learning framework built on three steps:

1. **Empirical Critical Point Identification:**
   Starting from a single baseline RL policy, we probe the environment with small opposing state perturbations $\pm \nu$. States from which perturbed rollouts split around opposite sides of an obstacle reveal the baseline policy's vulnerable critical region $\mathcal{M}^*$ (panel a below).
2. **Overlapping State-Space Partitioning:**
   We bisect the critical region $\mathcal{M}^*$ into two domains $\mathcal{M}_1$ and $\mathcal{M}_2$ and expand each across the boundary to create an **overlapping hysteresis zone** ($\mathcal{M}_1^{\text{exp}}$ intersecting $\mathcal{M}_2^{\text{exp}}$) whose width exceeds the worst-case sensor or adversarial perturbation (panels b and c below).
3. **Dedicated Policies &amp; Hybrid Hysteresis Switching:**
   Two specialized RL policies are trained (or fine-tuned) on the overlapping domains—one committed to passing each side of the obstacle. A discrete logic state locks in the active policy inside the overlap and only permits a switch if the vehicle moves completely outside the active policy's expanded domain.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/overview_critpoints_hysteresispolicies.png" title="Critical Point Identification and Overlapping Hysteresis Partitions" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Constructing Overlapping Hysteresis Regions ({% cite dePriester2024MultiHyRL %}):</strong> <strong>(a)</strong> Small opposing perturbations $z_0 \pm \nu$ cause baseline trajectories $\phi_1$ and $\phi_2$ to diverge around the obstacle, identifying critical points. <strong>(b)</strong> The empirical critical points $\mathcal{M}^*$ (red dots) are bisected into $\mathcal{M}_1$ and $\mathcal{M}_2$ and expanded by $\rho_1$ to create an overlap buffer. <strong>(c)</strong> Inside the overlap between $\mathcal{M}_1^{\text{exp}}$ and $\mathcal{M}_2^{\text{exp}}$, both specialized policies safely route the vehicle around opposite sides of the obstacle without chattering.
</div>

#### Scaling from Single-Obstacle State Feedback to Multi-Obstacle Visual Perception
- **HyRL (ACC 2022)** {% cite dePriester2022ACC %} introduced this hysteresis-based RL architecture for single-obstacle avoidance using state coordinates (below left), proving that overlapping training sets prevent adversarial chattering along $\mathcal{M}^*$.
- **MultiHyRL (RLC 2024)** {% cite dePriester2024MultiHyRL %} generalized the framework to **cluttered multi-obstacle 2D arenas** (below right) and **local bird's-eye-view visual observations**, combining directional hysteresis with obstacle-focus switching across relaxed Voronoi cells.

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/publication_preview/hyrlavoidance.jpg" title="HyRL Single-Obstacle Avoidance (ACC 2022)" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se2_overlay_case3_2d.png" title="2D Multi-Obstacle Avoidance Under Adversarial Perturbations" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (HyRL — ACC 2022 {% cite dePriester2022ACC %}):</strong> Overlapping domains $\mathcal{M}_0$ and $\mathcal{M}_1$ eliminate chattering along the critical set $\mathcal{M}^*$ ahead of an obstacle. <strong>Right (2D Multi-Obstacle Navigation Under Adversarial Noise):</strong> Under worst-case observation perturbations ($\varepsilon_p = 0.20$), a standard continuous PPO policy (red dashed) chatters and collides with the obstacles (black circles), while hybrid hysteresis switching (blue and green trajectories) routes all initial conditions safely to the target (gold star).
</div>

<div class="row justify-content-sm-center mt-3 mb-2">
  <div class="col-sm-10">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se2_combined_cumulative_performance.png" title="2D Cumulative Success Rate Across Perturbation Magnitudes" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>2D Planar Benchmark (1,000 Runs per Condition):</strong> As the adversarial observation perturbation increases from $\varepsilon_p = 0.00$ to $\varepsilon_p = 0.20$, Standard PPO (solid lines) drops below <strong>25%</strong> success in both single- and multi-obstacle arenas, whereas our hybrid hysteresis framework (dashed lines) achieves <strong>100% collision-free convergence</strong>.
</div>

---

### 2. Extending to 3D Flight &amp; Attitude Tracking: L-MultiHyRL Results (2026)

In our latest work on **Latent Multi-Policy Hysteresis-Based RL (L-MultiHyRL)** {% cite dePriester2026LMultiHyRL %}, we extend hybrid reinforcement learning from 2D planar navigation to **full 3D aerial flight in $\text{SE}(3)$ and rotational attitude tracking in $\text{SO}(3)$** using local onboard visual observations.

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

To verify that these robustness gains transfer directly to real-world aerial hardware under aerodynamic disturbances and closed-loop tracking delays, we deployed **L-MultiHyRL** zero-shot onto a physical **ModalAI Sentinel quadrotor** inside an indoor motion-capture flight arena.

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
  <h4>🛠️ Practical Impact &amp; Applications</h4>
  <p>
    From <strong>2D state-based HyRL (ACC 2022)</strong> {% cite dePriester2022ACC %} and <strong>2D vision-based MultiHyRL (RLC 2024)</strong> {% cite dePriester2024MultiHyRL %} to <strong>3D L-MultiHyRL (2026)</strong> {% cite dePriester2026LMultiHyRL %}, our hybrid reinforcement learning framework provides a principled upgrade for safety-critical autonomous systems—including <strong>agile UAV flight in cluttered environments</strong>, <strong>vision-based obstacle avoidance under sensor spoofing or occlusion</strong>, and <strong>large-angle 3D attitude maneuvers</strong>.
  </p>
</div>
