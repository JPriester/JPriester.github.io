---
layout: page
title: "Latent Multi-Policy Hybrid RL for 3D Obstacle Avoidance"
description: "Overcoming Spatial and Attitude Chattering in SE(3) via Latent Feature Partitioning and Dual Hysteresis"
img: assets/img/publication_preview/lmultihyrl_preview.png
importance: 1
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Research Article &amp; Hardware Validation (2026)</h4>
  <p>
    This article provides an accessible overview of our work, <strong>"Latent Multi-Policy Hysteresis-Based Reinforcement Learning for Robust 3D Obstacle Avoidance"</strong> by <em>Jan de Priester, Joseph Lullo, Kyle Volle, Prashant Ganesh, and Ricardo G. Sanfelice</em> {% cite dePriester2026LMultiHyRL %}, building upon our hybrid RL foundations in <strong>HyRL (ACC 2022)</strong> {% cite dePriester2022ACC %} and <strong>MultiHyRL (RLC 2024)</strong> {% cite dePriester2024MultiHyRL %}.
  </p>
</div>

Deep reinforcement learning (RL) enables agile autonomous navigation directly from onboard sensors. However, standard RL policies are **continuous, memoryless mappings** from observations to actions—a structural limitation that makes them vulnerable to **topological obstructions** during obstacle avoidance.

Whenever an autonomous vehicle approaches an obstacle in 3D space, it must break symmetry and commit to a bypass route (e.g., passing left/right or over/under). Along the **set of critical points** $\mathcal{M}^*$ dividing these equally viable routes, small sensor noise or adversarial perturbations can repeatedly flip the policy's decision. Instead of committing to a safe path around the obstacle, the vehicle **chatters** rapidly between opposing commands and collides head-on.

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/slide_bev_chattering.png" title="Topological Chattering Along the Critical Line in Local Bird's-Eye View" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/slide_bev_overlap_cones.png" title="Overlapping Hysteresis Domains Around an Obstacle" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Chattering Along Critical Points $\mathcal{M}^*$):</strong> When a vehicle approaches an obstacle along the critical dividing line (red dashed), small observation noise causes a memoryless policy to oscillate between passing left (blue) and passing right (green), crashing into the obstacle. <strong>Right (Hysteresis Across Overlapping Domains):</strong> Partitioning the space around the obstacle into overlapping regions $\mathcal{M}_1^{\text{exp}}$ and $\mathcal{M}_2^{\text{exp}}$ allows a hybrid supervisor to lock in a dedicated policy inside the overlap $\mathcal{M}_1^{\text{exp}} \cap \mathcal{M}_2^{\text{exp}}$.
</div>

In our earlier work, we resolved this vulnerability in 2D planar settings:
- **HyRL (ACC 2022)** {% cite dePriester2022ACC %} introduced **hysteresis-based reinforcement learning**, showing that training two policies on overlapping contractible subsets around an obstacle—and switching between them via a discrete logic variable—eliminates chattering when global obstacle coordinates are known.
- **MultiHyRL (RLC 2024)** {% cite dePriester2024MultiHyRL %} extended this framework to **multi-obstacle 2D environments with local bird's-eye-view perception**, identifying critical points empirically from a single baseline policy and training a separate classifier to estimate overlapping regions from raw 2D images.

Scaling hybrid RL from 2D planar navigation to full **6-DoF aerial flight in $\text{SE}(3)$** introduces two major challenges: (1) rotational tracking on $\text{SO}(3)$ suffers from its own topological obstruction— causing severe **attitude chattering**—and (2) training separate image classifiers for 3D spatial partitions is computationally redundant.

In this work, we introduce **Latent Multi-Policy Hysteresis-Based RL (L-MultiHyRL)** {% cite dePriester2026LMultiHyRL %}:
> **L-MultiHyRL unifies 3D spatial and rotational anti-chattering inside a single closed-loop hybrid system.** By predicting overlapping spatial partitions directly from the baseline policy's **learned latent CNN features** and pairing spatial policy switching with **quaternion pole hysteresis**, L-MultiHyRL achieves chatter-free 3D obstacle avoidance in both simulation and physical quadrotor flight tests.

---

### 1. Dual Topological Obstructions in 3D Flight ($\text{SE}(3)$ and $\text{SO}(3)$)

We consider an autonomous aerial vehicle operating in $Z := \text{SE}(3) = \mathbb{R}^3 \times \text{SO}(3)$ with position $p \in \mathbb{R}^3$ and orientation represented by a unit quaternion $q = (\eta, \epsilon) \in \mathbb{S}^3$. To navigate using local onboard sensing without a global map, the policy receives global tracking errors $(e_p, q_e)$ combined with **two orthogonal body-aligned 2D binary obstacle projections** $\mathcal{V}(z) = (\mathcal{V}_{xy}(z), \mathcal{V}_{xz}(z))$ capturing the local horizontal (top-down) and vertical (side) fields of view:

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/3d_Visobs.png" title="Multimodal Observation Model for 3D Navigation in SE(3)" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Multimodal Observation Space in $\text{SE}(3)$:</strong> <strong>(a)</strong> Isometric view of the vehicle with position error $e_p$, quaternion attitude error $q_e$, and orthogonal body-aligned sensing planes $\mathcal{V}_{xy}$ (cyan) and $\mathcal{V}_{xz}$ (magenta). <strong>(b)</strong> The resulting $64 \times 64$ binary visual projections $\mathcal{V} = (\mathcal{V}_{xy}, \mathcal{V}_{xz})$. <strong>(c)</strong> Geometry of an adversarial observation shift $\varepsilon_p \hat{\nu}_{\text{int}}$ perturbing the perceived obstacle position across the vehicle-to-target line of sight.
</div>

Why do continuous RL policies fail in $\text{SE}(3)$ under bounded sensor or adversarial perturbations $(\varepsilon_p, \varepsilon_q)$? Because **two distinct topological obstructions** arise simultaneously:

1. **Spatial Chattering Around 3D Obstacles ($\mathbb{R}^3 \setminus \mathcal{O}$):**
   Any continuous policy must contain a critical switching surface $\mathcal{M}^*$ ahead of each obstacle separating opposing 3D bypass trajectories. Note that **recurrent policies (RNNs/LSTMs) or frame stacking do not eliminate this obstruction**: because a fixed-window history mapping remains continuous on a connected product space, a persistent perturbation along the updated critical boundary still forces chattering and collision.
2. **Rotational Unwinding &amp; Chattering on $\text{SO}(3)$ ($\mathbb{S}^3$ Double Cover):**
   Every physical 3D orientation corresponds to two antipodal unit quaternions, $q_e$ and $-q_e$, representing opposite rotation directions around the Euler axis. A continuous controller on $\text{SO}(3)$ either suffers from **unwinding** (rotating almost $360^\circ$ when near $\eta_e \approx -1$) or introduces a discontinuous sign flip across the equator $\eta_e = 0$ (where the error angle is $180^\circ$). Under attitude noise $\varepsilon_q > 0$ near $\eta_e = 0$, a standard RL policy chatters violently between opposing angular velocity commands $(u_y, u_z)$, stalling at an unaligned attitude:

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_normal_attitude_chattering_delta00.png" title="Standard RL Policy Attitude Chattering at the Equator" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_hybrid_attitude_chattering_delta02.png" title="L-MultiHyRL Chatter-Free Attitude Convergence" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>$\text{SO}(3)$ Attitude Tracking Under Adversarial Noise ($\varepsilon_q = 0.4$):</strong> <strong>Left (Standard PPO, $\delta_\eta = 0$):</strong> Initial orientations near the antipodal equator ($\eta_e(0) \in \{0, \pm 0.13\}$) are trapped at $\eta_e = 0$ as angular velocity commands $(u_y, u_z)$ chatter at maximum frequency. <strong>Right (L-MultiHyRL with Quaternion Hysteresis, $\delta_\eta = 0.2$):</strong> Hysteresis locks in the active pole $\vartheta \in \{-1, 1\}$, eliminating control chattering and smoothly stabilizing all initial orientations.
</div>

---

### 2. How L-MultiHyRL Works: Latent Partitioning &amp; Dual Hysteresis

Starting from a single baseline RL policy $\pi_0$ trained via PPO, **L-MultiHyRL** robustifies 3D navigation in three steps without requiring global obstacle coordinates or dynamics rollouts at runtime:

#### Step 1: Critical Point Identification &amp; SVD Hyperplane Partitioning
To locate the vulnerable states of $\pi_0$ around an obstacle, we roll out pairs of trajectories fromperturbed positions $p \pm \nu$ across a 3D grid. Wherever opposing perturbations cause the trajectories to diverge onto opposite sides of the obstacle ($d_H(\phi_1, \phi_2) > \mu^*$), we label the initial state as a **critical point** $z \in \mathcal{M}^*$.

Rather than using complex 2D Voronoi tessellations {% cite dePriester2024MultiHyRL %}, we compute the centroid $p^c$ of $\mathcal{M}^*$ and apply **Singular Value Decomposition (SVD)** to fit an optimal bisecting hyperplane with normal vector $\vec{n}_c$ (panel b below). Shifting this plane by $\pm \rho$ creates two **overlapping 3D half-spaces** $\mathcal{M}_1^{\text{exp}}$ and $\mathcal{M}_2^{\text{exp}}$ of overlap thickness $2\rho > 2\varepsilon_p$, on which we fine-tune two specialized policies $\pi_1$ and $\pi_2$ initialized directly from $\pi_0$:

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/geometry1x3.png" title="Critical Point Identification and SVD Partitioning in 3D" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Geometric Partitioning of 3D Critical Points:</strong> <strong>(a)</strong> Opposing perturbations $p \pm \nu$ cause baseline trajectories $\phi_1, \phi_2$ to diverge around the obstacle, identifying critical points. <strong>(b)</strong> Red dots show the empirical critical set $\mathcal{M}^*$; SVD fits a bisecting plane through the centroid $p^c$ and expands it by $\pm \rho$ to form the overlapping region $\mathcal{M}_1^{\text{exp}} \cap \mathcal{M}_2^{\text{exp}}$ (pink). <strong>(c)</strong> Full 3D view of the overlapping domain ahead of the obstacle.
</div>

#### Step 2: Latent Space Membership Prediction (Zero Redundant Encoders)
At runtime, the vehicle does not know global obstacle coordinates—it only sees its local visual projections $\mathcal{V}(z)$. Instead of training a separate CNN classifier from scratch as in MultiHyRL {% cite dePriester2024MultiHyRL %}, **L-MultiHyRL reuses the baseline policy's frozen convolutional feature extractor** $\Phi_{\text{CNN}}$. A lightweight multi-layer perceptron $g_\psi$ takes the 64-dimensional **latent visual embedding** $h_{\mathcal{V}}(z) = \Phi_{\text{CNN}}(\mathcal{V}(z))$ and directly predicts membership $\hat{m}(z) = (\hat{m}_1, \hat{m}_2) \in \{0, 1\}^2$ in the overlapping regions $\mathcal{M}_1^{\text{exp}}$ and $\mathcal{M}_2^{\text{exp}}$:

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-10">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/network_arch_3D.png" title="Policy and Value Network Architecture with Shared CNN Feature Extractor" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Multimodal Network Architecture:</strong> The convolutional branch $\Phi_{\text{CNN}}$ compresses the orthogonal visual projections $\mathcal{V} = (\mathcal{V}_{xy}, \mathcal{V}_{xz})$ into a 64-D latent representation $h_{\mathcal{V}}$, which is concatenated ($\oplus$) with vector observations $o_{\text{vec}}$ for control and reused directly by the latent partition classifier $g_\psi$.
</div>

#### Step 3: Discretized Closed-Loop Hybrid System with Dual Hysteresis
At each sampling instant $t_k$, the deployed controller operates as a discretized hybrid system $\mathcal{H}_s = (C, F_s, D, G)$ with state $x := (z, \lambda, \vartheta)$, where two independent discrete memory states prevent chattering across both topological obstructions simultaneously:
- **Spatial Policy Hysteresis ($\lambda \in \{1, 2\}$):** Inside the overlap $\mathcal{M}_1^{\text{exp}} \cap \mathcal{M}_2^{\text{exp}}$ (where $\hat{m}(z) = (1, 1)$) or in free space ($\hat{m}(z) = (0, 0)$), the supervisor keeps the active policy $\pi_\lambda$ constant ($\lambda^+ = \lambda$). It switches to $\lambda^+ = 3 - \lambda$ **only** when the vehicle exits the overlap into the exclusive region of the opposing policy ($\hat{m}_\lambda(z) = 0$ and $\hat{m}_{3-\lambda}(z) = 1$).
- **Quaternion Pole Hysteresis ($\vartheta \in \{-1, 1\}$):** Rather than flipping the target quaternion pole at $\eta_e = 0$, the supervisor tracks pole $\vartheta$ and switches to $\vartheta^+ = -\vartheta$ **only** when the scalar quaternion alignment crosses the hysteresis threshold $\vartheta \eta_e \le -\delta_\eta$ (with $\delta_\eta \in (0, 1)$).

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-10">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/hysteresis_logics_se3.png" title="Dual Hysteresis Logic for Attitude and Spatial Obstacle Avoidance" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Dual Hysteresis Switching Logic in $\mathcal{H}_s$:</strong> <strong>(a) Attitude Hysteresis on $\mathbb{S}^3$:</strong> The target pole $\vartheta \in \{-1, 1\}$ only switches when $\eta_e$ crosses outside the hysteresis band $[-\delta_\eta, +\delta_\eta]$, preventing equatorial chattering. <strong>(b) Spatial Hysteresis in $\mathbb{R}^3$:</strong> The active policy $\lambda \in \{1, 2\}$ only switches when leaving the overlapping region $\mathcal{M}_1^{\text{exp}} \cap \mathcal{M}_2^{\text{exp}}$.
</div>

---

### 3. 3D Simulation &amp; Physical Quadrotor Validation

We evaluate **L-MultiHyRL** against a continuous **Standard PPO** baseline across 1,000 randomized 3D initial conditions in both single-obstacle and multi-obstacle arenas under worst-case adversarial observation perturbations $\varepsilon_p \in [0, 0.20]\text{ m}$, followed by **zero-shot sim-to-real deployment on a physical ModalAI Sentinel quadrotor**.

#### 3D Multi-Obstacle Simulation Benchmark
As shown below, when adversarial noise ($\varepsilon_p = 0.20\text{ m}$) shifts the perceived obstacle positions across the critical dividing plane, **Standard PPO** (red dashed trajectories) chatters between opposing bypass directions and repeatedly crashes into the spherical obstacles (black circles). In contrast, **L-MultiHyRL** (solid blue for $\lambda = 1$, solid green for $\lambda = 2$) locks in a committed bypass mode upon entering each obstacle's latent overlap zone, achieving a **100% success rate** across all perturbation levels:

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_overlay_case3_np0_20_na0_00_2way_1_2d_projections.png" title="Multi-Obstacle 3D Simulation Trajectories Under Adversarial Noise" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>3D Multi-Obstacle Navigation Under Adversarial Observation Perturbations ($\varepsilon_p = 0.20\text{ m}$):</strong> Top-down ($p_x$-$p_y$, left) and side ($p_x$-$p_z$, right) projections. Standard PPO (red dashed) chatters and collides with the obstacles (black circles), while L-MultiHyRL smoothly routes all trajectories to the target (gold star) via policies $\lambda = 1$ (blue) and $\lambda = 2$ (green).
</div>

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-10">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_combined_cumulative_performance_jax_2way.png" title="Cumulative Success Rate Across Perturbation Magnitudes" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Cumulative Success Rate Over Time (1,000 Runs per Condition):</strong> As the adversarial perturbation magnitude increases from $\varepsilon_p = 0.00$ to $\varepsilon_p = 0.20$, Standard PPO (solid curves) degrades to <strong>60.4%</strong> success in the single-obstacle arena and <strong>68.6%</strong> in the multi-obstacle arena. <strong>L-MultiHyRL (dashed curves) maintains 100.0% collision-free convergence across all noise levels.</strong>
</div>

#### Physical Flight Tests on a ModalAI Sentinel Quadrotor
To validate real-world performance under physical aerodynamic disturbances and closed-loop tracking delays, we deployed L-MultiHyRL zero-shot onto a **ModalAI Sentinel quadrotor** inside an OptiTrack motion-capture arena ($50\text{ Hz}$ outer-loop policy updates feeding PX4 velocity and yaw-rate setpoints).

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-11">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/se3_experiment_overlay_multi_obs_np0_25_na0_00_2way_1_2d_projections.png" title="Hardware Flight Trajectories on the ModalAI Sentinel Quadrotor" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Physical Quadrotor Multi-Obstacle Flights Under Perturbations ($\varepsilon_p = 0.25\text{ m}$):</strong> Top-down ($p_x$-$p_y$, left) and side ($p_x$-$p_z$, right) flight paths recorded on the ModalAI Sentinel quadrotor. Standard PPO (red) suffers from mid-flight indecision and collides with the obstacles (black circles), whereas L-MultiHyRL (blue/green) executes decisive, collision-free 3D avoidance maneuvers in every flight trial.
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
  <strong>Physical Quadrotor $\text{SO}(3)$ Attitude Tracking Under Perturbations ($\varepsilon_q = 0.4$):</strong> <strong>Left (Standard PPO, $\delta_\eta = 0$):</strong> Initial orientations near the antipodal boundary ($\eta_e(0) \in \{0, \pm 0.13\}$) trigger severe hardware angular rate chattering and stall near $\eta_e = 0$. <strong>Right (L-MultiHyRL, $\delta_\eta = 0.2$):</strong> Quaternion pole hysteresis eliminates chattering on the physical quadrotor and drives all trajectories smoothly to the desired orientation.
</div>

<div class="callout-box callout-emerald">
  <h4>🛠️ Key Engineering Takeaway: Evolution of the HyRL Framework</h4>
  <p>
    From <strong>2D state-based HyRL (ACC 2022)</strong> {% cite dePriester2022ACC %} and <strong>2D vision-based MultiHyRL (RLC 2024)</strong> {% cite dePriester2024MultiHyRL %} to <strong>3D L-MultiHyRL (2026)</strong> {% cite dePriester2026LMultiHyRL %}, our core insight remains simple: <em>continuous neural networks cannot robustly break symmetry around topological obstructions without discrete memory</em>. By extracting overlapping region classifiers directly from a policy's existing CNN latent space and embedding dual spatial and quaternion hysteresis, L-MultiHyRL upgrades standard deep RL policies into provably chatter-free 3D flight controllers with negligible runtime overhead.
  </p>
</div>
