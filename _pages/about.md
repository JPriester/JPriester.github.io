---
layout: about
title: about
permalink: /
subtitle: "Ph.D. Candidate & Safe RL Researcher | UC Santa Cruz"

hero_bio: |
  I am a Ph.D. candidate in Electrical and Computer Engineering at the **UC Santa Cruz Hybrid Systems Laboratory** (expected Sept. 2027), advised by **Prof. Ricardo G. Sanfelice**. With 5+ years of experience in **reinforcement learning and hybrid control**, I develop learning-based control methods with formal **safety, stability, and robustness guarantees**.

  My work focuses on building RL policies (**Python, PyTorch, JAX**) that prevent failure in adversarial edge cases—such as indecision and chattering around obstacles—and transfer reliably to physical hardware, as well as proving mathematically when standard RL policies intrinsically guarantee closed-loop stability. I am a first-author researcher (**RLC** 3×, **ACC**) and serve as a Senior Reviewer for **RLC**.

status_banner: "Seeking a 2027 Research Scientist role or internship in the SF Bay Area."

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false
  more_info: >
    <div>Ph.D. Candidate (2027)</div>
    <div>UC Santa Cruz • ECE</div>
    <div>Santa Cruz, CA</div>

selected_papers: true
social: true

announcements:
  enabled: true
  scrollable: true
  limit: 6

latest_posts:
  enabled: false
  scrollable: true
  limit: 3
---

<div id="research-directions" class="section-header" style="margin-top: 0.5rem;">
  <span class="section-kicker">Research Overview</span>
  <h2>High-Level Research Directions</h2>
  <p class="section-desc">
    My research combines <strong>reinforcement learning</strong> with <strong>control and hybrid systems theory</strong> so that learned policies remain stable, decisive, and robust in the real world. Explore the three core directions below through visual animations and interactive articles.
  </p>
</div>

<!-- ===================================================================== -->
<!-- DIRECTION 1: HYSTERESIS-BASED REINFORCEMENT LEARNING & SUPERVISION    -->
<!-- ===================================================================== -->
<div class="direction-card">
  <div class="direction-header">
    <span class="direction-badge badge-blue">Direction I • Hysteresis-Based RL &amp; Hybrid Supervision</span>
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">HyRL • MultiHyRL • L-MultiHyRL • Timer Supervision</span>
  </div>
  <h3 class="direction-title">Hysteresis-Based Reinforcement Learning for Robust, Chatter-Free Autonomy</h3>

  <!-- ROW 1: THE SETTING & FAILURE MECHANISM (LEFT) + BASELINE FAILURE ANIMATION (RIGHT) -->
  <div class="direction-body-grid" style="align-items: stretch;">
    <div style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="direction-meta-box">
          <div class="meta-box-title">1. The Problem: Indecision &amp; Chattering in Standard RL</div>
          <p>
            When an autonomous agent navigates around obstacles or chooses between competing tasks, it must commit to a viable route. Because standard RL policies are <strong>continuous or memoryless discrete mappings</strong>, obstacles create decision boundaries where opposing paths diverge. Near these boundaries, small sensor noise or adversarial perturbations can make an alternative route appear momentarily better—causing the agent to rapidly switch back and forth (<strong>chattering</strong>), stall, or crash.
          </p>
        </div>

        <div class="direction-meta-box">
          <div class="meta-box-title">⚡ Why Standard &amp; Recurrent RL Fall Short</div>
          <p>
            Recurrent policies (RNNs) and frame stacking embed memory via continuous hidden states—which merely shifts the decision boundary into a higher-dimensional space rather than eliminating it. Under adversarial observation perturbations, standard RL obstacle-avoidance success drops to <strong>~60% in 3D</strong> and <strong>below 25% in 2D</strong>.
          </p>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">🛠️ Our Approach (Visualized Below)</div>
        <p>
          We combine RL policies with <strong>hybrid discrete memory</strong> to guarantee decisive commitment—using <span class="key-highlight">overlapping spatial hysteresis partitions</span> (<strong>HyRL, MultiHyRL &amp; L-MultiHyRL</strong>, with L-MultiHyRL predicting partition membership directly from onboard perception via learned latent features) or <span class="key-highlight">predictive dwell-time timers</span> (<strong>Timer-Based Hybrid Supervisor</strong>) when switching boundaries between policies are complex or unknown in advance.
        </p>
      </div>
    </div>

    <!-- Animation 1: Normal Agent Failure Case (LMultiHyRLHysteresisLight.mp4) -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="canvas-header">
          <span class="canvas-title">1. Baseline Failure: Chattering &amp; Crash</span>
          <span class="canvas-live-dot" style="color: #dc2626; background: #fef2f2; border-color: #fecaca;">● Standard Continuous RL</span>
        </div>
        <video
          src="{{ '/assets/video/LMultiHyRLHysteresisLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
          controls
        ></video>
      </div>
      <div class="canvas-caption">
        <strong>Why Memoryless RL Policies Fail Around Obstacles:</strong> As the vehicle steers toward the upper route (Phase 1), an adversarial perturbation shifts the obstacle upward to block its path. Blocked above, the memoryless policy switches to the lower route (Phase 2), where a downward shift blocks it again—triggering repeated route reversals (Phase 3) and a head-on collision.
      </div>
    </div>
  </div>

  <!-- ROW 2: THE TWO PROPOSED SOLUTIONS SIDE-BY-SIDE -->
  <div class="direction-body-grid equal-cols" style="margin-top: 1.25rem; padding-top: 1.25rem; border-top: 1px dashed var(--global-divider-color); align-items: stretch;">
    <!-- Animation 2: How Hysteresis Works (LMultiHyRLHysteresisLogicLight.mp4) -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="canvas-header">
          <span class="canvas-title">2. Solution I: Spatial Hysteresis Partitions</span>
          <span class="canvas-live-dot">● HyRL • MultiHyRL • L-MultiHyRL</span>
        </div>
        <video
          src="{{ '/assets/video/LMultiHyRLHysteresisLogicLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
          controls
        ></video>
      </div>
      <div class="canvas-caption">
        <strong>Hysteresis-Based RL (Onboard Perception):</strong> Upper (blue) and Lower (green) partitions overlap around the centerline so both bypass routes are valid (Phase 1). When the obstacle shifts upward (Phase 2) or downward (Phase 3) to make the opposing route shorter, the agent stays inside its active partition, ignores the shortcut, and commits with <strong>0 route reversals</strong>.
      </div>
    </div>

    <!-- Animation 3: Predictive Dwell-Time Supervision (TimerHybridSupervisorLight.mp4) -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="canvas-header">
          <span class="canvas-title">3. Solution II: Predictive Dwell-Time Timer</span>
          <span class="canvas-live-dot">● Timer Supervisor (RLC '25)</span>
        </div>
        <video
          src="{{ '/assets/video/TimerHybridSupervisorLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
          controls
        ></video>
      </div>
      <div class="canvas-caption">
        <strong>Timer-Based Hybrid Supervisor (Complex Value Boundaries):</strong> When switching boundaries between multiple policies are fragmented or unknown in advance, our supervisor uses a short forward prediction (Phase 1) to forecast when an alternative policy becomes significantly better ($V_1$ exceeding $V_2$), locking in an adaptive dwell timer ($\delta_d = 5$) to safely clear each obstacle.
      </div>
    </div>
  </div>

  <!-- ROW 3: WHY IT MATTERS & WHERE IT EXTENDS (LEFT) + APPLICATIONS & ARTICLES (RIGHT) -->
  <div class="direction-body-grid equal-cols" style="margin-top: 1.25rem; align-items: stretch;">
    <div class="direction-meta-box" style="margin-bottom: 0; display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="meta-box-title">💡 Why It Matters &amp; Where It Extends</div>
        <p>
          Critical regions of indecision are not unique to obstacle avoidance—they arise whenever a continuous or discrete policy must choose between equally viable options. Left unaddressed, these boundaries are easily exploited by small perturbations or an adversary to trigger deadlocks or crashes. Crucially, <strong>scaling up to larger AI models does not remove this vulnerability</strong>: in higher-dimensional state spaces, critical decision boundaries persist in far more places than expected. Our methods provide a <strong>foundational layer of robustness</strong> against these structural failure modes across robotics and sequential decision-making for safety-critical applications.
        </p>
      </div>
      <div class="app-tag-list" style="margin-top: 0.7rem;">
        <span class="app-tag">Agile 3D Quadrotor / UAV Flight</span>
        <span class="app-tag">Vision-Based Obstacle Avoidance</span>
        <span class="app-tag">Adversarial-Resilient AI &amp; Control</span>
        <span class="app-tag">Multi-Policy Decision-Making</span>
      </div>
    </div>

    <div class="direction-meta-box" style="margin-bottom: 0; display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="meta-box-title">📖 Deep-Dive Articles &amp; Related Work</div>
        <p style="margin-bottom: 0.75rem;">
          By embedding discrete hysteresis memory and predictive dwell timers, our methods achieve <strong>100% obstacle avoidance success in simulation</strong> (vs. 60% for standard RL) and transfer reliably to physical hardware while preserving underlying policy performance and stability. Explore the full details in our interactive articles:
        </p>
      </div>
      <div class="paper-chip-list">
        <a href="{{ '/projects/l_multihyrl_3d_avoidance/' | relative_url }}" class="paper-chip">
          <span>📖 Article: Hysteresis-Based RL (HyRL, MultiHyRL &amp; L-MultiHyRL)</span>
        </a>
        <a href="{{ '/projects/timer_based_hybrid_supervisor/' | relative_url }}" class="paper-chip">
          <span>📖 Article: Timer-Based Hybrid Supervisor (RLC '25)</span>
        </a>
      </div>
    </div>
  </div>
</div>

<!-- ===================================================================== -->
<!-- DIRECTION 2: FOUNDATIONS & INTRINSIC STABILITY OF DEEP RL             -->
<!-- ===================================================================== -->
<div class="direction-card">
  <div class="direction-header">
    <span class="direction-badge badge-emerald">Direction II • Theoretical Foundations of RL</span>
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">Closed-Loop Practical Stability • Continuous Control</span>
  </div>
  <h3 class="direction-title">Intrinsic Practical Stability in Reinforcement Learning</h3>

  <!-- ROW 1: SETTING & INSIGHT (LEFT) + LOCOMOTION LIMIT CYCLE ANIMATION (RIGHT) -->
  <div class="direction-body-grid" style="align-items: stretch;">
    <div style="display: flex; flex-direction: column; justify-content: space-between; gap: 0.85rem;">
      <div class="direction-meta-box" style="margin-bottom: 0; flex: 1;">
        <div class="meta-box-title">1. The Setting &amp; Core Question</div>
        <p>
          Many continuous control tasks in robotics—such as legged locomotion, drone racing, and manipulation—are <em>set-stabilization problems</em>: the policy must steer the system toward and keep it within a desired target set or trajectory. Existing safe RL methods often constrain the learning algorithm with Lyapunov conditions or external safety filters, which can restrict exploration and complicate training.
        </p>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0; flex: 1;">
        <div class="meta-box-title">2. Main Theoretical Result (RLC 2026)</div>
        <p>
          We prove mathematically that, for deterministic continuous control tasks with a continuous optimal policy, <span class="key-highlight">standard discounted RL policies intrinsically guarantee closed-loop practical asymptotic stability</span> without imposing constraints on the learning process. Modeling the <strong>discount factor</strong> ($\gamma < 1$) and <strong>neural network approximation error</strong> ($\delta > 0$) as bounded disturbances shows that stability scales directly with learning performance: trajectories converge into a bounded neighborhood around the target that shrinks to zero as approximation error $\delta \to 0$ and, subsequently, $\gamma \to 1$.
        </p>
      </div>
    </div>

    <!-- Visual 1: Paper Animation (IntrinsicRLStabilityLight.mp4) -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div class="canvas-header">
        <span class="canvas-title">1. Animation: Stabilizing a Locomotion Limit Cycle</span>
        <span class="canvas-live-dot">● RLC 2026</span>
      </div>
      <div style="background: #ffffff; flex: 1; display: flex; align-items: center;">
        <video
          src="{{ '/assets/video/IntrinsicRLStabilityLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
          controls
          style="width: 100%; display: block;"
        ></video>
      </div>
      <div class="canvas-caption">
        <strong>Practical Stability of a Periodic Locomotion Gait:</strong> A galloping quadruped tracks a repeating target gait (dashed loops). <strong>Phase 1:</strong> As training progresses, neural network approximation error shrinks and the guaranteed stability tube tightens. <strong>Phase 2:</strong> As the discount factor approaches $1$ (farsighted), the tube contracts to the exact target gait.
      </div>
    </div>
  </div>

  <!-- ROW 2: THEORETICAL & NUMERICAL BOUNDS (LEFT) + IMPACT, APPLICATIONS & INTERACTIVE ARTICLE (RIGHT) -->
  <div class="direction-body-grid equal-cols" style="margin-top: 1.25rem; padding-top: 1.25rem; border-top: 1px dashed var(--global-divider-color); align-items: stretch;">
    <!-- Visual 2: Theoretical Neighborhoods & Numerical Bounds -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div class="canvas-header">
        <span class="canvas-title">2. Theoretical Tubes &amp; Steady-State Bounds ($\gamma, \delta$)</span>
        <span class="canvas-live-dot">● Theorems 3.9 &amp; 4.5</span>
      </div>
      <div style="padding: 10px 12px; background: #ffffff; flex: 1; display: flex; flex-direction: column; justify-content: space-evenly; gap: 6px;">
        <!-- Top Pair: Effect of Discount Factor gamma -->
        <div style="display: grid; grid-template-columns: 45.8% 54.2%; gap: 6px; align-items: center;">
          <img
            src="{{ '/assets/img/intrinsic_rl/PracticalStability_Discounting.png' | relative_url }}"
            alt="Theorem 3.9: Practical Stability Neighborhood under Discounting"
            class="img-fluid rounded"
            style="width: 100%; height: auto; display: block;"
          >
          <img
            src="{{ '/assets/img/intrinsic_rl/ideal_bound.png' | relative_url }}"
            alt="Numerical Steady-State Bounds Across Discount Factors"
            class="img-fluid rounded"
            style="width: 100%; height: auto; display: block;"
          >
        </div>
        <!-- Bottom Pair: Effect of Approximation Error delta -->
        <div style="display: grid; grid-template-columns: 45.8% 54.2%; gap: 6px; align-items: center;">
          <img
            src="{{ '/assets/img/intrinsic_rl/PracticalStability_DiscountingApproximation.png' | relative_url }}"
            alt="Theorem 4.5: Practical Stability Neighborhood under Neural Network Approximation"
            class="img-fluid rounded"
            style="width: 100%; height: auto; display: block;"
          >
          <img
            src="{{ '/assets/img/intrinsic_rl/approx_bound.png' | relative_url }}"
            alt="Numerical Steady-State Bounds Across Approximation Errors"
            class="img-fluid rounded"
            style="width: 100%; height: auto; display: block;"
          >
        </div>
      </div>
      <div class="canvas-caption">
        <strong>Analytical &amp; Numerical Practical Stability Bounds:</strong> <strong>Top (Discounting $\gamma$):</strong> Trajectories converge into a neighborhood $\nu(\gamma)$ (blue) around $\mathcal{A}$ that shrinks toward zero as $\gamma \to 1$. <strong>Bottom (Network Error $\delta$):</strong> Approximation error expands the bound to $\tilde{\nu}(\gamma, \delta)$ (green), which contracts back to $\nu(\gamma)$ as $\delta \to 0$.
      </div>
    </div>

    <!-- Why It Matters, Applications & Interactive Deep-Dive Links -->
    <div style="display: flex; flex-direction: column; justify-content: space-between; gap: 0.85rem;">
      <div class="direction-meta-box" style="margin-bottom: 0; flex: 1; display: flex; flex-direction: column; justify-content: space-between;">
        <div>
          <div class="meta-box-title">3. Why It Matters &amp; Where It Extends</div>
          <p>
            While training-time constraints remain valuable for safe exploration, this result shows that <strong>modifying the RL objective is often unnecessary to guarantee the stability of the deployed policy</strong>. It also explains why small steady-state offsets are a natural effect of discounting rather than an algorithmic bug, and guarantees that any target accuracy can be certified at a standard discount factor $\gamma < 1$.
          </p>
        </div>
        <div class="app-tag-list" style="margin-top: 0.7rem;">
          <span class="app-tag">Legged Locomotion &amp; Limit Cycles</span>
          <span class="app-tag">Autonomous Drone Racing</span>
          <span class="app-tag">Principled Discount Factor Tuning</span>
          <span class="app-tag">Continuous Set Stabilization</span>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">📖 Interactive Deep-Dive Article &amp; Paper</div>
        <p style="margin-bottom: 0.7rem;">
          Explore the proof intuition and an <strong>interactive steady-state simulator</strong> where you can vary $\gamma$, $\delta$, and reward weights live in your browser:
        </p>
        <div class="paper-chip-list">
          <a href="{{ '/projects/intrinsic_stability_rl/' | relative_url }}" class="paper-chip">
            <span>📖 Article &amp; Interactive Simulator: Intrinsic Stability in RL (RLC '26)</span>
          </a>
          <a href="{{ '/assets/pdf/intrinsic_practical_stability_RL.pdf' | relative_url }}" target="_blank" class="paper-chip chip-secondary">
            <span>📄 RLC 2026 Paper PDF</span>
          </a>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ===================================================================== -->
<!-- DIRECTION 3: SIM-TO-REAL & PHYSICAL HARDWARE VALIDATION               -->
<!-- ===================================================================== -->
<div class="direction-card">
  <div class="direction-header">
    <span class="direction-badge badge-amber">Direction III • Physical Robotics &amp; Sim-to-Real Validation</span>
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">ModalAI Sentinel UAV • 6-DoF Elfin e03 Manipulator</span>
  </div>
  <h3 class="direction-title">From Theory &amp; Simulation to Physical Robots: Real-World Robustness Validation</h3>

  <!-- ROW 1: NARRATIVE PROGRESSION (LEFT) + QUADROTOR FLIGHT VALIDATION (RIGHT) -->
  <div class="direction-body-grid" style="align-items: stretch;">
    <div style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="direction-meta-box">
          <div class="meta-box-title">1. The Goal: Scaling Theoretical Guarantees to Real Hardware</div>
          <p>
            While our core methods are grounded in control theory and simulation, a central goal of this direction is showing that <span class="key-highlight">the robustness we design transfers reliably to physical robots</span>. Real hardware introduces unmodeled aerodynamics, actuator delays, sensor drift, and contact dynamics—conditions where standard switching boundaries cause severe oscillations.
          </p>
        </div>

        <div class="direction-meta-box">
          <div class="meta-box-title">2. 3D Quadrotor Flight &amp; Attitude Control (L-MultiHyRL)</div>
          <p>
            We deploy the <strong>simulation-derived closed-loop hybrid system</strong> from <strong>L-MultiHyRL</strong>—comprising the trained local policies, latent partition classifier, and discrete hysteresis logic—directly onboard a physical <strong>ModalAI Sentinel quadrotor</strong> (VOXL 2 at 25 Hz over ROS 2 / PX4) without retraining. Under adversarial obstacle shifts ($\varepsilon_p = 0.25\text{ m}$) and $\text{SO}(3)$ attitude disturbances ($\varepsilon_q = 0.4$) where standard PPO chatters and crashes, the hybrid system commits decisively to a single passage route and converges smoothly.
          </p>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">3. Robotic Contact &amp; Force Regulation (IEEE CCTA 2026)</div>
        <p>
          On a <strong>6-DoF Elfin e03 manipulator with an OnRobot RG2FT force/torque gripper</strong>, we formulate a two-level hybrid hysteresis controller across free-space position control (`MOVE`) and quantized gripper force modes (`TIGHTEN`, `HOLD`, `LOOSEN`). Unlike baseline switching—which suffers from rapid force oscillations—our hysteresis logic maintains stable force regulation under setpoint changes, shifting centers of gravity, changing object stiffness, and 30-minute sensor drift.
        </p>
      </div>
    </div>

    <!-- Hardware Validation I: Real Quadrotor Flight & Attitude Tracking Results -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div class="canvas-header">
        <span class="canvas-title">1. 3D Quadrotor Flight &amp; Attitude Tracking</span>
        <span class="canvas-live-dot">● ModalAI Sentinel UAV</span>
      </div>
      <div style="padding: 10px 12px; background: #ffffff; flex: 1; display: flex; flex-direction: column; justify-content: space-evenly; gap: 6px;">
        <img
          src="{{ '/assets/img/lmultihyrl/se3_experiment_overlay_multi_obs_np0_25_na0_00_2way_1_2d_projections.png' | relative_url }}"
          alt="Physical ModalAI Sentinel Quadrotor Multi-Obstacle Flight Trajectories"
          class="img-fluid rounded"
          style="width: 100%; height: auto; display: block;"
        >
        <img
          src="{{ '/assets/img/lmultihyrl/se3_experiment_normal_attitude_np0_00_na0_40_attitude_chattering.png' | relative_url }}"
          alt="Hardware Attitude Chattering Under Standard PPO"
          class="img-fluid rounded"
          style="width: 100%; height: auto; display: block;"
        >
        <img
          src="{{ '/assets/img/lmultihyrl/se3_experiment_hybrid_attitude_np0_00_na0_40_attitude_chattering.png' | relative_url }}"
          alt="Hardware Chatter-Free Attitude Convergence Under L-MultiHyRL"
          class="img-fluid rounded"
          style="width: 100%; height: auto; display: block;"
        >
      </div>
      <div class="canvas-caption">
        <strong>Closed-Loop Hybrid Deployment on a ModalAI Sentinel Quadrotor:</strong> <strong>Top:</strong> Physical 3D multi-obstacle flights ($\varepsilon_p = 0.25\text{ m}$)—Standard PPO (red) chatters and collides, while the L-MultiHyRL closed-loop hybrid system (blue/green) flies collision-free. <strong>Middle vs. Bottom:</strong> Hardware $\text{SO}(3)$ attitude tracking ($\varepsilon_q = 0.4$) comparing Standard PPO control chattering (middle) against smooth L-MultiHyRL convergence (bottom).
      </div>
    </div>
  </div>

  <!-- ROW 2: 6-DOF MANIPULATOR HARDWARE VALIDATION (LEFT) + IMPACT, APPLICATIONS & LINKS (RIGHT) -->
  <div class="direction-body-grid equal-cols" style="margin-top: 1.25rem; padding-top: 1.25rem; border-top: 1px dashed var(--global-divider-color); align-items: stretch;">
    <!-- Hardware Validation II: 6-DoF Manipulator & Force Regulation -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div class="canvas-header">
        <span class="canvas-title">2. 6-DoF Manipulator &amp; Contact Force Regulation</span>
        <span class="canvas-live-dot">● IEEE CCTA 2026</span>
      </div>
      <div style="padding: 10px 12px; background: #ffffff; flex: 1; display: flex; align-items: center;">
        <div class="row g-2 align-items-stretch" style="width: 100%; margin: 0;">
          <div class="col-6" style="display: flex; align-items: center; justify-content: center; padding-left: 0; padding-right: 4px;">
            <img
              src="{{ '/assets/img/ccta26/together.png' | relative_url }}"
              alt="RG2FT End Effector and 6-DoF Elfin e03 Manipulator Hardware Setup"
              class="img-fluid rounded"
              style="width: 100%; height: 100%; object-fit: contain; display: block;"
            >
          </div>
          <div class="col-6" style="display: flex; flex-direction: column; justify-content: space-between; gap: 6px; padding-left: 4px; padding-right: 0;">
            <img
              src="{{ '/assets/img/ccta26/1_No_Hysteresis_IEEE.jpg' | relative_url }}"
              alt="Baseline Force Chattering Without Hysteresis"
              class="img-fluid rounded"
              style="width: 100%; height: auto; display: block;"
            >
            <img
              src="{{ '/assets/img/ccta26/2_Ball_3N_to_1N_Hysteresis_V2.jpg' | relative_url }}"
              alt="Chatter-Free Hysteresis Force Regulation"
              class="img-fluid rounded"
              style="width: 100%; height: auto; display: block;"
            >
          </div>
        </div>
      </div>
      <div class="canvas-caption">
        <strong>Hysteresis Contact &amp; Force Control on a 6-DoF Manipulator:</strong> <strong>Left:</strong> Physical <strong>Elfin e03 6-DoF arm and RG2FT force/torque gripper</strong>. <strong>Right (Top):</strong> Baseline switching without hysteresis suffers from rapid state and contact-force oscillations. <strong>Right (Bottom):</strong> Our hysteresis controller smoothly regulates $3\text{ N}$ and $1\text{ N}$ contact force setpoints without chattering.
      </div>
    </div>

    <!-- Why It Matters, Applications & Hardware Links -->
    <div style="display: flex; flex-direction: column; justify-content: space-between; gap: 0.85rem;">
      <div class="direction-meta-box" style="margin-bottom: 0; flex: 1; display: flex; flex-direction: column; justify-content: space-between;">
        <div>
          <div class="meta-box-title">💡 Why It Matters &amp; Where It Extends</div>
          <p>
            Physical robots regularly operate across switching boundaries—both in <strong>3D aerial obstacle avoidance and attitude tracking</strong> and in <strong>contact-rich manipulation</strong> between free-space motion and force control. Discrete hysteresis provides a lightweight, general mechanism that prevents hardware chattering and transfers reliably from simulation to the real world.
          </p>
        </div>
        <div class="app-tag-list" style="margin-top: 0.7rem;">
          <span class="app-tag">Onboard UAV Flight (PX4 / ROS 2)</span>
          <span class="app-tag">SO(3) Quaternion Attitude Control</span>
          <span class="app-tag">Sim-to-Real Hybrid Deployment</span>
          <span class="app-tag">6-DoF Force &amp; Contact Control</span>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">📖 Hardware Validation &amp; Related Work</div>
        <p style="margin-bottom: 0.7rem;">
          Explore the experimental flight results and physical manipulation studies in detail:
        </p>
        <div class="paper-chip-list">
          <a href="{{ '/projects/l_multihyrl_3d_avoidance/#3-physical-flight-validation-on-a-modalai-sentinel-quadrotor' | relative_url }}" class="paper-chip">
            <span>🚁 Quadrotor 3D Flight Validation (L-MultiHyRL)</span>
          </a>
          <a href="{{ '/publications/#spott2026experimental' | relative_url }}" class="paper-chip chip-secondary">
            <span>🤖 6-DoF Hysteresis Contact Control (IEEE CCTA '26)</span>
          </a>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ===================================================================== -->
<!-- FEATURED RESEARCH ARTICLES SHOWCASE                                   -->
<!-- ===================================================================== -->
<div class="section-header">
  <span class="section-kicker">Accessible Deep Dives</span>
  <h2>Featured Research Articles</h2>
  <p class="section-desc">
    Read concise, visual explanations of our research—complete with intuitive diagrams, animations, and experimental results.
  </p>
</div>

<div class="articles-grid">
  <a href="{{ '/projects/l_multihyrl_3d_avoidance/' | relative_url }}" class="article-showcase-card">
    <div>
      <div class="article-thumb-wrap">
        <video
          src="{{ '/assets/video/LMultiHyRLHysteresisLogicLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
        ></video>
      </div>
      <div class="article-tag-row">
        <span class="article-tag">Hysteresis-Based RL • 2D &amp; 3D</span>
        <span class="article-venue">ACC '22 • RLC '24 • 2026</span>
      </div>
      <h3>Hysteresis-Based Reinforcement Learning for Chatter-Free Autonomy (HyRL, MultiHyRL &amp; L-MultiHyRL)</h3>
      <p>
        How overlapping spatial hysteresis partitions eliminate structural indecision and adversarial chattering in safety-critical sequential decision-making—achieving 100% collision-free 2D/3D navigation and transferring reliably to a physical quadrotor.
      </p>
    </div>
    <span class="article-read-link">Read Article →</span>
  </a>

  <a href="{{ '/projects/intrinsic_stability_rl/' | relative_url }}" class="article-showcase-card">
    <div>
      <div class="article-thumb-wrap">
        <video
          src="{{ '/assets/video/IntrinsicRLStabilityLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
        ></video>
      </div>
      <div class="article-tag-row">
        <span class="article-tag">RL Foundations • Practical Stability</span>
        <span class="article-venue">RLC 2026</span>
      </div>
      <h3>Intrinsic Practical Stability in Discrete-Time Reinforcement Learning</h3>
      <p>
        Why modifying the core RL objective is often unnecessary for deployed stability: proving mathematically that standard discounted RL policies intrinsically guarantee closed-loop practical stability that scales with learning performance.
      </p>
    </div>
    <span class="article-read-link">Read Article →</span>
  </a>

  <a href="{{ '/projects/timer_based_hybrid_supervisor/' | relative_url }}" class="article-showcase-card">
    <div>
      <div class="article-thumb-wrap">
        <video
          src="{{ '/assets/video/TimerHybridSupervisorLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
        ></video>
      </div>
      <div class="article-tag-row">
        <span class="article-tag">Hybrid Supervision</span>
        <span class="article-venue">RLC 2025</span>
      </div>
      <h3>Timer-Based Hybrid Supervisor for Robust Policy Switching</h3>
      <p>
        Preventing indecision, chattering, and deadlocks across fragmented multi-policy value boundaries using short-horizon state predictions and an adaptive dwell-time timer while preserving underlying policy stability.
      </p>
    </div>
    <span class="article-read-link">Read Article →</span>
  </a>
</div>
