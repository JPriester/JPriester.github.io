---
layout: about
title: about
permalink: /
subtitle: "Safe & Hybrid Reinforcement Learning Researcher | Ph.D. Candidate at UC Santa Cruz"

hero_bio: |
  I am a Ph.D. candidate in Electrical & Computer Engineering at the **UC Santa Cruz Hybrid Systems Lab** (defending Summer 2027), advised by **Prof. Ricardo G. Sanfelice**. My research bridges **nonlinear & hybrid control theory** with **deep reinforcement learning** to build autonomous systems with formal closed-loop stability, robustness, and safety guarantees.

  Collaborating with the **Air Force Research Laboratory (AFRL)**, I design perception-driven hybrid RL architectures that eliminate catastrophic decision chattering under sensor and adversarial perturbations, alongside theoretical foundations proving when standard RL intrinsically guarantees closed-loop practical stability. I also serve as a Senior Reviewer for RLC 2026.

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
<!-- DIRECTION 1: HYBRID & PERCEPTION-DRIVEN ROBUST RL                     -->
<!-- ===================================================================== -->
<div class="direction-card">
  <div class="direction-header">
    <span class="direction-badge badge-blue">Direction I • Hybrid &amp; Perception-Driven RL</span>
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">2D &amp; 3D Navigation • Hysteresis • Timer Supervision</span>
  </div>
  <h3 class="direction-title">Hybrid Reinforcement Learning for Chatter-Free, Robust Autonomy</h3>

  <div class="direction-body-grid">
    <div>
      <p class="direction-summary">
        <strong>Overview:</strong> Standard neural network policies are continuous, memoryless mappings from observations to actions. When an autonomous vehicle navigates around obstacles or switches among competing tasks, <em>topological obstructions</em> create critical boundaries where small sensor noise or adversarial perturbations trick the agent into rapidly oscillating between conflicting decisions (<strong>chattering</strong>)—leading to stalls or collisions. We combine deep RL policies with <strong>hybrid dynamical systems</strong>, using <span class="key-highlight">overlapping hysteresis regions</span> and <span class="key-highlight">predictive dwell-time timers</span> to guarantee decisive, chatter-free trajectory commitment.
      </p>

      <div class="direction-meta-box">
        <div class="meta-box-title">✨ Key Takeaway</div>
        <p>
          Continuous feedback laws—including recurrent policies (RNNs) and frame stacking—cannot eliminate critical switching boundaries around obstacles without discrete memory. Across our <strong>HyRL</strong>, <strong>MultiHyRL</strong>, and <strong>L-MultiHyRL</strong> framework, we partition the space around obstacles into overlapping domains to achieve 100% collision-free 2D and 3D navigation under adversarial perturbations. When value boundaries are fragmented or unknown in advance, our <strong>Timer-Based Hybrid Supervisor</strong> predicts forward value trajectories to adaptively lock in the active policy until the agent safely clears the vulnerable region.
        </p>
      </div>

      <div class="direction-meta-box">
        <div class="meta-box-title">🚀 Application Areas</div>
        <div class="app-tag-list">
          <span class="app-tag">Agile 3D Quadrotor / UAV Flight</span>
          <span class="app-tag">Vision-Based Obstacle Avoidance</span>
          <span class="app-tag">Adversarial-Resilient Autonomy</span>
          <span class="app-tag">Multi-Policy Supervisory Control</span>
        </div>
      </div>

      <div class="direction-links">
        <span class="links-label">Deep-Dive Articles &amp; Related Work</span>
        <div class="paper-chip-list">
          <a href="{{ '/projects/l_multihyrl_3d_avoidance/' | relative_url }}" class="paper-chip">
            <span>📖 Article: HyRL, MultiHyRL &amp; 3D L-MultiHyRL</span>
          </a>
          <a href="{{ '/projects/timer_based_hybrid_supervisor/' | relative_url }}" class="paper-chip">
            <span>📖 Article: Timer-Based Hybrid Supervisor (RLC '25)</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Paper Animation: TimerHybridSupervisorLight.mp4 -->
    <div class="direction-canvas-wrap">
      <div class="canvas-header">
        <span class="canvas-title">Animation: Predictive Dwell-Time Supervision</span>
        <span class="canvas-live-dot">● RLC 2025</span>
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
        <strong>Eliminating Decision Chattering Around Obstacles:</strong> When an environmental perturbation shifts an obstacle across the critical boundary, a memoryless agent (red) chatters between opposing routes and crashes. Our <strong>Timer-Based Hybrid Supervisor</strong> forecasts when an alternative policy becomes significantly better ($V_1$ exceeding $V_2$), locking in an adaptive dwell timer ($\delta_d = 5$) to safely clear each obstacle.
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
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">Closed-Loop Stability • Continuous Control</span>
  </div>
  <h3 class="direction-title">Intrinsic Practical Stability in Reinforcement Learning</h3>

  <div class="direction-body-grid">
    <div>
      <p class="direction-summary">
        <strong>Overview:</strong> Many continuous control tasks in robotics—such as legged locomotion gaits, autonomous drone racing, and dexterous manipulation—are fundamentally <em>set-stabilization problems</em> that require steering a system toward and keeping it within a desired target behavior. Existing safe RL methods typically modify the learning algorithm with Lyapunov constraints or external safety filters, which can restrict exploration and complicate training. In our <strong>RLC 2026</strong> paper, we prove that <span class="key-highlight">modifying the core RL objective is often unnecessary to guarantee the stability of the deployed policy</span>: standard discounted RL <strong>intrinsically guarantees practical asymptotic stability</strong>.
      </p>

      <div class="direction-meta-box">
        <div class="meta-box-title">✨ Key Takeaway</div>
        <p>
          Why do standard RL agents often settle into a small steady-state error near the target? Rather than being an algorithmic flaw, this offset is a <strong>natural consequence of discounting future rewards</strong>, which makes the agent slightly <em>myopic</em> and content with being "close enough." By modeling both this <strong>discounted horizon</strong> and <strong>neural network approximation errors</strong> as bounded disturbances, we prove that near-optimal neural policies naturally steer the system into a <span class="key-highlight">tight, bounded neighborhood around the target</span>—which shrinks to zero as network approximation improves and the agent becomes more farsighted.
        </p>
      </div>

      <div class="direction-meta-box">
        <div class="meta-box-title">🚀 Application Areas</div>
        <div class="app-tag-list">
          <span class="app-tag">Legged Locomotion &amp; Limit Cycles</span>
          <span class="app-tag">Autonomous Drone Racing</span>
          <span class="app-tag">Dexterous Manipulation</span>
          <span class="app-tag">Principled Discount Factor Tuning</span>
        </div>
      </div>

      <div class="direction-links">
        <span class="links-label">Deep-Dive Article &amp; Paper</span>
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
    <div class="direction-canvas-wrap">
      <div class="canvas-header">
        <span class="canvas-title">Animation: Stabilizing a Periodic Locomotion Gait</span>
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
      <div class="canvas-caption">
        <strong>Practical Stability of a Limit Cycle:</strong> A galloping quadruped tracks a repeating target gait (dashed loops). <strong>Phase 1:</strong> As training progresses, neural network approximation error shrinks and the guaranteed stability tube tightens. <strong>Phase 2:</strong> As the discount factor approaches $1$ (farsighted), the tube contracts to the exact target gait.
      </div>
    </div>
  </div>
</div>

<!-- ===================================================================== -->
<!-- DIRECTION 3: PHYSICAL ROBOTICS & SIM-TO-REAL HYBRID CONTROL           -->
<!-- ===================================================================== -->
<div class="direction-card">
  <div class="direction-header">
    <span class="direction-badge badge-amber">Direction III • Physical Robotics &amp; Sim-to-Real</span>
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">ModalAI Sentinel UAV • 6-DoF Manipulators</span>
  </div>
  <h3 class="direction-title">Sim-to-Real 3D Flight, Attitude Hysteresis &amp; Hybrid Contact Control</h3>

  <div class="direction-body-grid">
    <div>
      <p class="direction-summary">
        <strong>Overview:</strong> Deploying learning-based and hybrid controllers onto physical robots requires overcoming real-world dynamics gaps, sensor noise, and rotational or contact discontinuities. Our experimental work validates hybrid RL and hybrid feedback controllers on physical hardware—including <strong>zero-shot 3D obstacle avoidance and $\text{SO}(3)$ attitude tracking on a ModalAI Sentinel quadrotor</strong> and <strong>hysteresis-based contact detection and force regulation on a 6-DoF robotic manipulator</strong> (IEEE CCTA 2026).
      </p>

      <div class="direction-meta-box">
        <div class="meta-box-title">✨ Key Takeaway</div>
        <p>
          Physical robots encounter topological switching boundaries in both <strong>3D rotation space ($\text{SO}(3)$)</strong>—where antipodal unit quaternions $\pm q_e$ induce pitch/yaw chattering across the equator $\eta_e = 0$—and <strong>contact-rich manipulation</strong>, where sensor noise triggers end-effector bouncing at surface contact thresholds. Embedding discrete hysteresis memory eliminates hardware chattering and enables smooth, reliable physical execution without retraining.
        </p>
      </div>

      <div class="direction-meta-box">
        <div class="meta-box-title">🚀 Application Areas</div>
        <div class="app-tag-list">
          <span class="app-tag">Onboard UAV Flight (PX4 / ROS 2)</span>
          <span class="app-tag">SO(3) Quaternion Attitude Control</span>
          <span class="app-tag">Zero-Shot Sim-to-Real Transfer</span>
          <span class="app-tag">6-DoF Robotic Force &amp; Contact Control</span>
        </div>
      </div>

      <div class="direction-links">
        <span class="links-label">Hardware Validation &amp; Related Work</span>
        <div class="paper-chip-list">
          <a href="{{ '/projects/l_multihyrl_3d_avoidance/#3-physical-flight-validation-on-a-modalai-sentinel-quadrotor' | relative_url }}" class="paper-chip">
            <span>🚁 Quadrotor 3D Flight Validation (L-MultiHyRL)</span>
          </a>
          <a href="{{ '/publications/#spott2026experimental' | relative_url }}" class="paper-chip chip-secondary">
            <span>🤖 6-DoF Hybrid Contact &amp; Force Control (CCTA '26)</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Real Hardware Flight & Attitude Tracking Results -->
    <div class="direction-canvas-wrap">
      <div class="canvas-header">
        <span class="canvas-title">Hardware Validation: 3D Quadrotor Flight &amp; Attitude Tracking</span>
        <span class="canvas-live-dot">● ModalAI Sentinel UAV</span>
      </div>
      <div style="padding: 12px 14px 6px 14px; background: #ffffff;">
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
      <div class="canvas-caption">
        <strong>Top:</strong> Physical <strong>ModalAI Sentinel quadrotor</strong> flights through a 3D multi-obstacle arena under perturbations ($\varepsilon_p = 0.25\text{ m}$): Standard PPO (red) chatters and collides, while L-MultiHyRL (blue and green) flies collision-free to the target. <strong>Bottom:</strong> Hardware $\text{SO}(3)$ attitude tracking ($\varepsilon_q = 0.4$) comparing Standard PPO chattering (left) against smooth L-MultiHyRL convergence (right).
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
      <div class="article-tag-row">
        <span class="article-tag">Hybrid RL • 2D &amp; 3D Navigation</span>
        <span class="article-venue">ACC '22 • RLC '24 • 2026</span>
      </div>
      <h3>Hysteresis-Based Hybrid RL for 2D &amp; 3D Obstacle Avoidance (HyRL, MultiHyRL &amp; L-MultiHyRL)</h3>
      <p>
        How overlapping hysteresis domains eliminate topological chattering in autonomous obstacle avoidance and attitude tracking—achieving 100% collision-free navigation in 2D/3D simulations and on a physical ModalAI Sentinel quadrotor.
      </p>
    </div>
    <span class="article-read-link">Read Article →</span>
  </a>

  <a href="{{ '/projects/intrinsic_stability_rl/' | relative_url }}" class="article-showcase-card">
    <div>
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
