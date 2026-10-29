---
layout: about
title: about
permalink: /
subtitle: "Safe & Hybrid Reinforcement Learning Researcher | Ph.D. Candidate at UC Santa Cruz"

hero_bio: |
  I am a Ph.D. candidate in Electrical & Computer Engineering at the **UC Santa Cruz Hybrid Systems Lab** (defending Summer 2027), advised by **Prof. Ricardo G. Sanfelice**. My research bridges **nonlinear & hybrid control theory** with **deep reinforcement learning** to build autonomous systems with formal closed-loop stability, robustness, and safety guarantees.

  Collaborating with the **Air Force Research Laboratory (AFRL)**, I design perception-driven **hysteresis-based reinforcement learning** and hybrid supervisory architectures that eliminate catastrophic decision chattering under sensor and adversarial perturbations, alongside theoretical foundations proving when standard RL intrinsically guarantees closed-loop practical stability. I also serve as a Senior Reviewer for RLC 2026.

status_banner: "Seeking Research Scientist internships focused on learning-based control and autonomous robotics with verifiable safety guarantees."

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
    Rather than treating learning and control as separate modules, my research embeds formal dynamical systems structure directly into deep reinforcement learning. Explore the three core pillars below through visual animations and accessible research articles.
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
  <h3 class="direction-title">Hysteresis-Based Reinforcement Learning for Chatter-Free, Robust Autonomy</h3>

  <!-- ROW 1: THE SETTING & FAILURE MECHANISM (LEFT) + BASELINE FAILURE ANIMATION (RIGHT) -->
  <div class="direction-body-grid" style="align-items: stretch;">
    <div style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="direction-meta-box">
          <div class="meta-box-title">1. The Setting &amp; Topological Chattering Problem</div>
          <p>
            Whenever an autonomous vehicle navigates around obstacles or switches among competing tasks, it must break symmetry and commit to a route. Because standard neural network policies parameterize <strong>continuous, memoryless mappings</strong> from observations to actions, <em>topological obstructions</em> create critical boundaries where opposing decisions diverge. Along these boundaries, small sensor noise or adversarial perturbations shift the perceived obstacle into the vehicle's active passage direction—making the opposing route appear shorter and tricking the agent into rapidly oscillating between conflicting routes (<strong>chattering</strong>) until it stalls or crashes.
          </p>
        </div>

        <div class="direction-meta-box">
          <div class="meta-box-title">⚡ Why Standard &amp; Recurrent RL Cannot Fix This</div>
          <p>
            Continuous feedback laws—including recurrent policies (RNNs) and frame stacking—cannot eliminate critical switching boundaries around obstacles without discrete state memory. Under worst-case observation perturbations, standard RL success rates drop from 100% down to <strong>60% in 3D</strong> and <strong>below 25% in 2D</strong>.
          </p>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">🛠️ Our Proposed Approach (Visualized Below)</div>
        <p>
          We embed specialized RL policies inside <strong>hybrid dynamical systems</strong> with discrete memory—using <span class="key-highlight">overlapping spatial hysteresis partitions</span> (<strong>HyRL, MultiHyRL &amp; L-MultiHyRL</strong>) for model-free onboard perception, or <span class="key-highlight">predictive dwell-time timers</span> (<strong>Timer-Based Hybrid Supervisor</strong>) when value boundaries are complex or unknown in advance.
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
        <strong>Hysteresis-Based RL (Model-Free Onboard Perception):</strong> Upper (blue) and Lower (green) partitions overlap around the centerline so both bypass routes are valid (Phase 1). When the obstacle shifts upward (Phase 2) or downward (Phase 3) to make the opposing route shorter, the agent stays inside its initialized partition, ignores the shortcut, and commits with <strong>0 route reversals</strong>.
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
        <strong>Timer-Based Hybrid Supervisor (Complex Value Boundaries):</strong> When switching boundaries between arbitrary policies are fragmented or unknown in advance, our supervisor rolls out a short forward forecast (Phase 1) to predict when an alternative policy becomes significantly better ($V_1$ exceeding $V_2$), locking in an adaptive dwell timer ($\delta_d = 5$) to safely clear each obstacle.
      </div>
    </div>
  </div>

  <!-- ROW 3: WHY IT MATTERS & WHERE IT EXTENDS (LEFT) + APPLICATIONS & ARTICLES (RIGHT) -->
  <div class="direction-body-grid equal-cols" style="margin-top: 1.25rem; align-items: stretch;">
    <div class="direction-meta-box" style="margin-bottom: 0; display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="meta-box-title">💡 Why It Matters &amp; Where It Extends</div>
        <p>
          Without altering standard RL reward design or sacrificing agility, embedding discrete hysteresis memory upgrades fragile neural policies into <strong>verifiably chatter-free controllers</strong>—achieving <strong>100% collision-free navigation</strong> under worst-case adversarial noise. The framework naturally extends from 2D planar avoidance to <strong>3D vision-based UAV flight in $\text{SE}(3)$</strong>, <strong>large-angle quaternion attitude control on $\text{SO}(3)$</strong>, and <strong>hierarchical multi-policy decision-making</strong>.
        </p>
      </div>
      <div class="app-tag-list" style="margin-top: 0.7rem;">
        <span class="app-tag">Agile 3D Quadrotor / UAV Flight</span>
        <span class="app-tag">Vision-Based Obstacle Avoidance</span>
        <span class="app-tag">Adversarial-Resilient Autonomy</span>
        <span class="app-tag">Multi-Policy Supervisory Control</span>
      </div>
    </div>

    <div class="direction-meta-box" style="margin-bottom: 0; display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="meta-box-title">📖 Deep-Dive Articles &amp; Related Work</div>
        <p style="margin-bottom: 0.75rem;">
          Explore the mathematical foundations, state-space partitioning algorithms, 2D/3D simulation benchmarks, and physical quadrotor flight validations in our interactive deep-dive articles:
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

  <div class="direction-body-grid" style="align-items: stretch;">
    <div style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="direction-meta-box">
          <div class="meta-box-title">1. The Setting &amp; Core Question</div>
          <p>
            Many continuous control tasks in robotics—such as legged locomotion gaits, autonomous drone racing, and dexterous manipulation—are <em>set-stabilization problems</em> that require steering a system toward and keeping it within a desired target behavior. Existing safe RL methods typically modify the learning algorithm with Lyapunov constraints or external safety filters, which can restrict exploration and complicate training.
          </p>
        </div>

        <div class="direction-meta-box">
          <div class="meta-box-title">2. Proposed Theoretical Insight (RLC 2026)</div>
          <p>
            We prove that <span class="key-highlight">modifying the core RL objective is often unnecessary to guarantee the stability of the deployed policy</span>: standard discounted RL <strong>intrinsically guarantees uniform practical asymptotic stability (UPAS)</strong>. By modeling the <strong>discounted horizon</strong> (which makes the agent slightly myopic) and <strong>neural network approximation errors</strong> as bounded disturbances, we show that near-optimal policies naturally steer the system into a tight neighborhood around the target that shrinks to zero as network approximation improves and $\gamma \to 1$.
          </p>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">3. Why It Matters, Where It Extends &amp; Deep-Dive Article</div>
        <p style="margin-bottom: 0.65rem;">
          This result explains why native steady-state offsets in RL are a natural feature of discounting rather than a bug, and proves that any real-world accuracy tolerance can be certified at a strictly sub-unity discount factor $\gamma < 1$ without training instability.
        </p>
        <div class="app-tag-list" style="margin-bottom: 0.75rem;">
          <span class="app-tag">Legged Locomotion &amp; Limit Cycles</span>
          <span class="app-tag">Autonomous Drone Racing</span>
          <span class="app-tag">Principled Discount Factor Tuning</span>
        </div>
        <div class="paper-chip-list">
          <a href="{{ '/projects/intrinsic_stability_rl/' | relative_url }}" class="paper-chip">
            <span>📖 Article: Intrinsic Practical Stability in RL (RLC '26)</span>
          </a>
          <a href="{{ '/assets/pdf/intrinsic_practical_stability_RL.pdf' | relative_url }}" target="_blank" class="paper-chip chip-secondary">
            <span>📄 RLC 2026 Paper PDF</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Paper Animation: IntrinsicRLStabilityLight.mp4 -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="canvas-header">
          <span class="canvas-title">Animation: Stabilizing a Locomotion Limit Cycle</span>
          <span class="canvas-live-dot">● RLC 2026</span>
        </div>
        <video
          src="{{ '/assets/video/IntrinsicRLStabilityLight.mp4' | relative_url }}"
          autoplay
          loop
          muted
          playsinline
          controls
        ></video>
      </div>
      <div class="canvas-caption">
        <strong>Practical Stability of a Periodic Locomotion Gait:</strong> A galloping quadruped tracks a repeating target gait (dashed loops). <strong>Phase 1:</strong> As training progresses, neural network approximation error shrinks and the guaranteed stability tube tightens. <strong>Phase 2:</strong> As the discount factor approaches $1$ (farsighted), the tube contracts to the exact target gait.
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
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">ModalAI Sentinel UAV (L-MultiHyRL) • 6-DoF Manipulator (CCTA '26)</span>
  </div>
  <h3 class="direction-title">From Theory &amp; Simulation to Physical Robots: Real-World Robustness Validation</h3>

  <div class="direction-body-grid" style="align-items: stretch;">
    <div style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="direction-meta-box">
          <div class="meta-box-title">1. The Setting &amp; Goal: Scaling Theory to Real Hardware</div>
          <p>
            While the foundation of our research is rooted in formal control theory and simulation, the central goal of this direction is proving that <span class="key-highlight">the mathematical robustness we design scales to the real world and works reliably on physical robots</span> under unmodeled aerodynamics, actuator delays, sensor drift, and compliant surface contact.
          </p>
        </div>

        <div class="direction-meta-box">
          <div class="meta-box-title">2. Flagship RL Validation • L-MultiHyRL Quadrotor Flight</div>
          <p>
            In our flagship sim-to-real study, we deploy <strong>L-MultiHyRL</strong> zero-shot onto a physical <strong>ModalAI Sentinel quadrotor</strong>. Under adversarial observation perturbations and $\text{SO}(3)$ antipodal rotation boundaries where standard continuous RL policies chatter, stall, and crash, our hysteresis-based RL architecture transfers seamlessly across the sim-to-real gap—achieving <strong>100% collision-free 3D obstacle avoidance and smooth attitude convergence</strong>.
          </p>
        </div>
      </div>

      <div class="direction-meta-box" style="margin-bottom: 0;">
        <div class="meta-box-title">3. Classical Hybrid Counterpart • 6-DoF Contact Control (CCTA '26)</div>
        <p style="margin-bottom: 0.75rem;">
          While <strong>not RL-based</strong>, our collaborative robotics study on a physical <strong>6-DoF manipulator</strong> applies the <strong>same hybrid hysteresis-switching principle</strong> between position and force controllers—eliminating contact bouncing and maintaining stable grasping under changing object stiffness, shifting centers of gravity, and 30-minute thermal sensor drift.
        </p>
        <div class="paper-chip-list">
          <a href="{{ '/projects/l_multihyrl_3d_avoidance/#3-physical-flight-validation-on-a-modalai-sentinel-quadrotor' | relative_url }}" class="paper-chip">
            <span>🚁 Flagship: Quadrotor 3D Flight Validation (L-MultiHyRL)</span>
          </a>
          <a href="{{ '/publications/#spott2026experimental' | relative_url }}" class="paper-chip chip-secondary">
            <span>🤖 6-DoF Hysteresis Contact Control (IEEE CCTA '26)</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Real Hardware Flight & Attitude Tracking Results -->
    <div class="direction-canvas-wrap" style="display: flex; flex-direction: column; justify-content: space-between;">
      <div>
        <div class="canvas-header">
          <span class="canvas-title">Flagship Hardware Validation: 3D Quadrotor Flight</span>
          <span class="canvas-live-dot">● ModalAI Sentinel UAV</span>
        </div>
        <div style="padding: 10px 12px 6px 12px; background: #ffffff;">
          <img
            src="{{ '/assets/img/lmultihyrl/se3_experiment_overlay_multi_obs_np0_25_na0_00_2way_1_2d_projections.png' | relative_url }}"
            alt="Physical ModalAI Sentinel Quadrotor Multi-Obstacle Flight Trajectories"
            class="img-fluid rounded"
            style="margin-bottom: 10px;"
          >
          <div class="row g-2">
            <div class="col-6">
              <img
                src="{{ '/assets/img/lmultihyrl/se3_experiment_normal_attitude_np0_00_na0_40_attitude_chattering.png' | relative_url }}"
                alt="Hardware Attitude Chattering Under Standard PPO"
                class="img-fluid rounded"
              >
            </div>
            <div class="col-6">
              <img
                src="{{ '/assets/img/lmultihyrl/se3_experiment_hybrid_attitude_np0_00_na0_40_attitude_chattering.png' | relative_url }}"
                alt="Hardware Chatter-Free Attitude Convergence Under L-MultiHyRL"
                class="img-fluid rounded"
              >
            </div>
          </div>
        </div>
      </div>
      <div class="canvas-caption">
        <strong>Flagship Sim-to-Real Transfer on a ModalAI Sentinel Quadrotor:</strong> <strong>Top:</strong> Physical 3D multi-obstacle flights under perturbations ($\varepsilon_p = 0.25\text{ m}$)—Standard PPO (red) chatters and collides, while L-MultiHyRL (blue and green) flies collision-free to the target. <strong>Bottom:</strong> Hardware $\text{SO}(3)$ attitude tracking ($\varepsilon_q = 0.4$) comparing Standard PPO chattering (left) against smooth L-MultiHyRL convergence (right).
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
      <h3>Hysteresis-Based Reinforcement Learning for 2D &amp; 3D Obstacle Avoidance (HyRL, MultiHyRL &amp; L-MultiHyRL)</h3>
      <p>
        How overlapping hysteresis domains eliminate topological chattering in autonomous obstacle avoidance and attitude tracking—achieving 100% collision-free navigation in 2D/3D simulations and on a physical ModalAI Sentinel quadrotor.
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
        Why modifying the core RL objective is often unnecessary for deployed stability: showing how standard discounted RL with neural network policies naturally steers systems into a tight neighborhood around the target.
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
      <h3>Chatter-Free Policy Switching via a Predictive Timer-Based Hybrid Supervisor</h3>
      <p>
        Eliminating rapid policy chattering across complex multi-target value function boundaries using a resettable timer that adaptively enforces dwell time via forward state prediction.
      </p>
    </div>
    <span class="article-read-link">Read Article →</span>
  </a>
</div>
