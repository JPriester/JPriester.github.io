---
layout: about
title: about
permalink: /
subtitle: "Safe & Hybrid Reinforcement Learning Researcher | Ph.D. Candidate at UC Santa Cruz"

hero_bio: |
  I am a Ph.D. candidate in Electrical & Computer Engineering at the **UC Santa Cruz Hybrid Systems Lab** (defending Summer 2027), advised by **Prof. Ricardo G. Sanfelice**. My research bridges **nonlinear & hybrid control theory** with **deep reinforcement learning** to build autonomous systems with formal closed-loop stability, robustness, and safety guarantees.

  Collaborating with the **Air Force Research Laboratory (AFRL)**, I design perception-driven hybrid RL architectures that eliminate catastrophic decision chattering under adversarial perturbations, alongside theoretical foundations proving when standard RL intrinsically guarantees closed-loop practical stability. I also serve as a Senior Reviewer for RLC 2026.

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
    Rather than treating learning and control as separate modules, my research embeds formal dynamical systems structure directly into deep reinforcement learning. Explore the three core pillars below through live interactive simulations and deep-dive articles.
  </p>
</div>

<!-- ===================================================================== -->
<!-- DIRECTION 1: HYBRID & PERCEPTION-DRIVEN ROBUST RL                     -->
<!-- ===================================================================== -->
<div class="direction-card">
  <div class="direction-header">
    <span class="direction-badge badge-blue">Direction I • Hybrid &amp; Perception-Driven RL</span>
    <span style="font-family: monospace; font-size: 0.8rem; color: #64748b;">SE(3) Navigation • Hysteresis • Timer Supervision</span>
  </div>
  <h3 class="direction-title">Hybrid Reinforcement Learning for Chatter-Free, Adversarial-Resilient Autonomy</h3>

  <div class="direction-body-grid">
    <div>
      <p class="direction-summary">
        <strong>Summary:</strong> Standard neural network policies parameterize continuous or memoryless mappings. When an autonomous vehicle navigates around obstacles in $\text{SE}(3)$ or tracks 3D attitudes in $\text{SO}(3)$, <em>topological obstructions</em> create decision boundaries where tiny sensor noise or adversarial perturbations trick the agent into rapidly alternating between conflicting routes or rotation directions (<strong>chattering</strong>), leading to stalls or crashes. We combine deep RL feature extractors with <strong>hybrid dynamical systems</strong>—using overlapping contractible state-space partitions, hysteresis memory, and predictive resettable timers—to guarantee decisive, chatter-free trajectory commitment.
      </p>

      <div class="direction-meta-box">
        <div class="meta-box-title">✨ Why It's Interesting</div>
        <p>
          Classical topological results in control theory prove that no continuous feedback law can globally stabilize a system in an obstructed space without decision boundaries—and even recurrent networks (RNNs) or frame stacking only shift this boundary into a higher-dimensional continuous hidden state. In <strong>L-MultiHyRL</strong>, we reuse a baseline policy's onboard CNN latent features $h_{\mathcal{V}}(z)$ to dynamically classify overlapping spatial partitions without global obstacle coordinates, pairing spatial hysteresis with $\mathbb{S}^3$ quaternion hysteresis.
        </p>
      </div>

      <div class="direction-meta-box">
        <div class="meta-box-title">🚀 Application Areas</div>
        <div class="app-tag-list">
          <span class="app-tag">Agile 3D Quadrotor / UAV Flight</span>
          <span class="app-tag">Vision-Based Obstacle Avoidance</span>
          <span class="app-tag">Adversarial-Resilient Autonomy</span>
          <span class="app-tag">Multi-Policy Supervisory Control</span>
          <span class="app-tag">Autonomous Driving</span>
        </div>
      </div>

      <div class="direction-links">
        <span class="links-label">Deep-Dive Articles &amp; Related Work</span>
        <div class="paper-chip-list">
          <a href="{{ '/projects/l_multihyrl_3d_avoidance/' | relative_url }}" class="paper-chip">
            <span>📖 Article: L-MultiHyRL in 3D SE(3)</span>
          </a>
          <a href="{{ '/projects/timer_based_hybrid_supervisor/' | relative_url }}" class="paper-chip">
            <span>📖 Article: Timer-Based Hybrid Supervisor (RLC '25)</span>
          </a>
          <a href="{{ '/projects/2Dmultihyrl/' | relative_url }}" class="paper-chip">
            <span>📖 Article: 2D MultiHyRL (RLC '24)</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Interactive Animation 1 -->
    <div class="direction-canvas-wrap">
      <div class="canvas-header">
        <span class="canvas-title">Live Sim: L-MultiHyRL vs. Standard RL</span>
        <span class="canvas-live-dot">● Interactive</span>
      </div>
      <canvas id="homeHyrlCanvas" width="460" height="290"></canvas>
      <div class="canvas-controls">
        <div>
          <button class="canvas-btn active" id="homeHyrlAdvBtn" onclick="window.setHomeHyrlMode('adv')">Adversarial Shift (ε_p = 0.20)</button>
          <button class="canvas-btn" id="homeHyrlCleanBtn" onclick="window.setHomeHyrlMode('clean')">Unperturbed (ε_p = 0.00)</button>
        </div>
        <span style="font-family: monospace; font-size: 0.74rem; color: #059669; font-weight: 700;">100% vs. 60.4% Success</span>
      </div>
      <div class="canvas-caption" id="homeHyrlCaption">
        <strong>Adversarial Perturbation Active:</strong> Standard PPO (red) chatters across the decision boundary and crashes into the obstacle. <strong>L-MultiHyRL</strong> (blue $\lambda=1$, green $\lambda=2$) locks in its active local policy across the pink $2\rho$ hysteresis overlap.
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
  <h3 class="direction-title">Sim-to-Real Aerial Autonomy, Attitude Hysteresis &amp; Hybrid Contact Control</h3>

  <div class="direction-body-grid">
    <div>
      <p class="direction-summary">
        <strong>Summary:</strong> Deploying theoretical control and RL frameworks on physical robots requires overcoming real-world dynamics gaps, actuator saturation, and contact or rotational discontinuities. Our experimental work validates hybrid RL and hybrid feedback controllers on physical hardware—including <strong>onboard 25 Hz vision-based 3D flight on a ModalAI Sentinel quadrotor</strong> (VOXL 2, ROS 2 Humble, PX4) and <strong>hysteresis-based contact detection and force regulation on a 6-link robotic manipulator</strong> (IEEE CCTA 2026).
      </p>

      <div class="direction-meta-box">
        <div class="meta-box-title">✨ Why It's Interesting</div>
        <p>
          Physical robots experience both continuous sim-to-real mismatches (e.g., first-order kinematic RL policies vs. second-order underactuated quadrotors that must tilt to translate) and discrete topological traps (such as $\mathbb{S}^3$ double-cover attitude chattering at $\eta_e = 0$ or end-effector bouncing at surface contact thresholds). We resolve these without policy retraining via parallel onboard kinematic propagation and hybrid hysteresis supervisors.
        </p>
      </div>

      <div class="direction-meta-box">
        <div class="meta-box-title">🚀 Application Areas</div>
        <div class="app-tag-list">
          <span class="app-tag">Onboard UAV Flight (PX4 / ROS 2)</span>
          <span class="app-tag">SO(3) Quaternion Attitude Control</span>
          <span class="app-tag">Zero-Retraining Sim-to-Real</span>
          <span class="app-tag">6-DoF Robotic Force &amp; Contact Control</span>
        </div>
      </div>

      <div class="direction-links">
        <span class="links-label">Hardware Validation &amp; Related Work</span>
        <div class="paper-chip-list">
          <a href="{{ '/projects/l_multihyrl_3d_avoidance/#4-hardware-validation-on-a-physical-quadrotor-in-textse3' | relative_url }}" class="paper-chip">
            <span>🚁 Quadrotor Sim-to-Real Validation (L-MultiHyRL)</span>
          </a>
          <a href="{{ '/publications/#spott2026experimental' | relative_url }}" class="paper-chip chip-secondary">
            <span>🤖 6-DoF Hybrid Contact &amp; Force Control (CCTA '26)</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Interactive Animation 3 -->
    <div class="direction-canvas-wrap">
      <div class="canvas-header">
        <span class="canvas-title">Live Sim: SO(3) Quaternion &amp; Contact Hysteresis</span>
        <span class="canvas-live-dot">● Hardware Modes</span>
      </div>
      <canvas id="homeRoboticsCanvas" width="460" height="290"></canvas>
      <div class="canvas-controls">
        <div>
          <button class="canvas-btn active" id="homeRobQuatBtn" onclick="window.setHomeRobMode('quat')">SO(3) Attitude Hysteresis</button>
          <button class="canvas-btn" id="homeRobContactBtn" onclick="window.setHomeRobMode('contact')">Manipulator Contact Regulation</button>
        </div>
      </div>
      <div class="canvas-caption" id="homeRobCaption">
        <strong>SO(3) Antipodal Quaternion Tracking ($\mathbb{S}^3$):</strong> Under adversarial rotational disturbance $\varepsilon_q = 0.4$, hysteresis memory $\vartheta \in \{-1, +1\}$ with band $\pm \delta_\eta$ prevents unwinding and eliminates pitch/yaw chattering on the quadrotor.
      </div>
    </div>
  </div>
</div>

<!-- ===================================================================== -->
<!-- FEATURED RESEARCH ARTICLES SHOWCASE                                   -->
<!-- ===================================================================== -->
<div class="section-header">
  <span class="section-kicker">Interactive Deep Dives</span>
  <h2>Featured Research Articles</h2>
  <p class="section-desc">
    Read accessible, visual explanations of our recent papers—complete with interactive sandboxes and architectural breakdowns.
  </p>
</div>

<div class="articles-grid">
  <a href="{{ '/projects/l_multihyrl_3d_avoidance/' | relative_url }}" class="article-showcase-card">
    <div>
      <div class="article-tag-row">
        <span class="article-tag">3D Hybrid RL • SE(3) &amp; SO(3)</span>
        <span class="article-venue">Preprint 2026</span>
      </div>
      <h3>L-MultiHyRL: Latent Multi-Policy Hysteresis-Based RL for Robust 3D Obstacle Avoidance</h3>
      <p>
        How onboard CNN latent features and dual spatial/quaternion hysteresis eliminate chattering in 3D flight—achieving 100% obstacle avoidance in simulation and on a physical ModalAI Sentinel quadrotor.
      </p>
    </div>
    <span class="article-read-link">Read Interactive Article →</span>
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

  <a href="{{ '/projects/2Dmultihyrl/' | relative_url }}" class="article-showcase-card">
    <div>
      <div class="article-tag-row">
        <span class="article-tag">Adversarial Robustness</span>
        <span class="article-venue">RLC 2024</span>
      </div>
      <h3>MultiHyRL: Robust Hybrid RL for Obstacle Avoidance Against Observation Attacks</h3>
      <p>
        Identifying topological critical points and constructing overlapping relaxed Voronoi partitions to protect autonomous navigation agents against adversarial sensor noise.
      </p>
    </div>
    <span class="article-read-link">Read Article →</span>
  </a>
</div>

<!-- Homepage Interactive Animations Script -->
<script>
(function() {
  // ---------------------------------------------------------------------------
  // 1. Direction I Canvas: L-MultiHyRL 3D/2D Obstacle Avoidance Animation
  // ---------------------------------------------------------------------------
  const hyrlCanvas = document.getElementById('homeHyrlCanvas');
  if (hyrlCanvas) {
    const ctx1 = hyrlCanvas.getContext('2d');
    let hyrlMode = 'adv';
    let phase1 = 0;

    window.setHomeHyrlMode = function(m) {
      hyrlMode = m;
      document.getElementById('homeHyrlAdvBtn').classList.toggle('active', m === 'adv');
      document.getElementById('homeHyrlCleanBtn').classList.toggle('active', m === 'clean');
      document.getElementById('homeHyrlCaption').innerHTML = m === 'adv'
        ? '<strong>Adversarial Perturbation Active (ε_p = 0.20):</strong> Standard PPO (red) chatters across the decision boundary and crashes into the obstacle. <strong>L-MultiHyRL</strong> (blue λ=1, green λ=2) commits to its active partition across the pink 2ρ hysteresis overlap.'
        : '<strong>Unperturbed Nominal Flight (ε_p = 0.00):</strong> Without adversarial observation shifts, both Standard PPO and L-MultiHyRL smoothly bypass the 3D obstacles to reach the target.';
    };

    function renderHyrl() {
      ctx1.clearRect(0, 0, hyrlCanvas.width, hyrlCanvas.height);
      phase1 = (phase1 + 0.007) % 1;

      // Subtle background grid
      ctx1.strokeStyle = '#e2e8f0'; ctx1.lineWidth = 1;
      for (let x = 35; x < hyrlCanvas.width; x += 50) {
        ctx1.beginPath(); ctx1.moveTo(x, 0); ctx1.lineTo(x, hyrlCanvas.height); ctx1.stroke();
      }
      for (let y = 35; y < hyrlCanvas.height; y += 50) {
        ctx1.beginPath(); ctx1.moveTo(0, y); ctx1.lineTo(hyrlCanvas.width, y); ctx1.stroke();
      }

      // Partition background tint
      ctx1.fillStyle = 'rgba(29, 78, 216, 0.035)';
      ctx1.fillRect(0, 0, hyrlCanvas.width, 145);
      ctx1.fillStyle = 'rgba(5, 150, 105, 0.035)';
      ctx1.fillRect(0, 145, hyrlCanvas.width, 145);

      // 2rho Hysteresis Overlap Zone
      ctx1.fillStyle = 'rgba(236, 72, 153, 0.13)';
      ctx1.fillRect(30, 118, 210, 54);
      ctx1.strokeStyle = 'rgba(219, 39, 119, 0.55)';
      ctx1.setLineDash([4, 4]);
      ctx1.beginPath();
      ctx1.moveTo(30, 118); ctx1.lineTo(240, 118);
      ctx1.moveTo(30, 172); ctx1.lineTo(240, 172);
      ctx1.stroke();
      ctx1.setLineDash([]);

      ctx1.fillStyle = '#be185d'; ctx1.font = 'bold 10px monospace';
      ctx1.fillText('M_1^exp ∩ M_2^exp (2ρ Overlap)', 42, 112);

      // 3D Obstacle Sphere
      const ox = 235, oy = 145, orad = 42;
      const oGrad = ctx1.createRadialGradient(ox - 12, oy - 12, 5, ox, oy, orad);
      oGrad.addColorStop(0, '#cbd5e1');
      oGrad.addColorStop(0.7, '#64748b');
      oGrad.addColorStop(1, '#334155');
      ctx1.fillStyle = oGrad;
      ctx1.beginPath(); ctx1.arc(ox, oy, orad, 0, Math.PI * 2); ctx1.fill();
      ctx1.strokeStyle = '#1e293b'; ctx1.lineWidth = 1.8; ctx1.stroke();

      // Equator ring on sphere to show 3D
      ctx1.strokeStyle = 'rgba(255,255,255,0.45)'; ctx1.setLineDash([3, 3]);
      ctx1.beginPath(); ctx1.ellipse(ox, oy, orad, 13, 0, 0, Math.PI * 2); ctx1.stroke();
      ctx1.setLineDash([]);

      // Target Star
      const tx = 415, ty = 145;
      ctx1.fillStyle = '#f59e0b';
      ctx1.beginPath(); ctx1.arc(tx, ty, 10, 0, Math.PI * 2); ctx1.fill();
      ctx1.strokeStyle = '#b45309'; ctx1.lineWidth = 2; ctx1.stroke();
      ctx1.fillStyle = '#0f172a'; ctx1.font = 'bold 10.5px Inter, sans-serif';
      ctx1.fillText('Target p*', tx - 24, ty + 24);

      // L-MultiHyRL Committed Route 1 (Blue, lambda = 1)
      const route1 = [[40, 134], [125, 126], [235, 64], [340, 102], [415, 145]];
      const route2 = [[40, 156], [125, 164], [235, 226], [340, 188], [415, 145]];

      [
        {pts: route1, col: '#1d4ed8', label: 'L-MultiHyRL (λ=1)', ly: 54},
        {pts: route2, col: '#059669', label: 'L-MultiHyRL (λ=2)', ly: 256}
      ].forEach(r => {
        ctx1.strokeStyle = r.col; ctx1.lineWidth = 3.2;
        ctx1.beginPath(); ctx1.moveTo(r.pts[0][0], r.pts[0][1]);
        for (let i = 1; i < r.pts.length; i++) {
          const p0 = r.pts[i - 1], p1 = r.pts[i];
          const mx = (p0[0] + p1[0]) / 2;
          ctx1.bezierCurveTo(mx, p0[1], mx, p1[1], p1[0], p1[1]);
        }
        ctx1.stroke();
        ctx1.fillStyle = r.col; ctx1.font = 'bold 10.5px Inter, sans-serif';
        ctx1.fillText(r.label, 185, r.ly);

        // Moving quadrotor marker
        const s = phase1 * (r.pts.length - 1);
        const idx = Math.min(Math.floor(s), r.pts.length - 2);
        const f = s - idx;
        const qx = r.pts[idx][0] + (r.pts[idx + 1][0] - r.pts[idx][0]) * f;
        const qy = r.pts[idx][1] + (r.pts[idx + 1][1] - r.pts[idx][1]) * f;
        ctx1.fillStyle = '#ffffff'; ctx1.strokeStyle = r.col; ctx1.lineWidth = 2.5;
        ctx1.beginPath(); ctx1.arc(qx, qy, 5.5, 0, Math.PI * 2); ctx1.fill(); ctx1.stroke();
      });

      // Standard PPO Trajectory (Red)
      ctx1.strokeStyle = '#dc2626'; ctx1.lineWidth = 2.4; ctx1.setLineDash([4, 2]);
      ctx1.beginPath();
      if (hyrlMode === 'adv') {
        ctx1.moveTo(40, 145);
        const cSteps = 8;
        const crashX = ox - orad;
        for (let i = 1; i <= cSteps; i++) {
          const sx = 40 + (crashX - 40) * (i / cSteps);
          const sy = 145 + (i === cSteps ? 0 : (i % 2 === 0 ? -16 : 16));
          ctx1.lineTo(sx, sy);
        }
        ctx1.stroke(); ctx1.setLineDash([]);
        // Animated chattering red agent
        const cProg = Math.min(1, phase1 * 1.35);
        const cx = 40 + (crashX - 40) * cProg;
        const cy = cProg < 1 ? 145 + Math.sin(phase1 * 45) * 14 : 145;
        ctx1.fillStyle = '#dc2626';
        ctx1.beginPath(); ctx1.arc(cx, cy, 5.5, 0, Math.PI * 2); ctx1.fill();
        ctx1.font = 'bold 10px Inter, sans-serif';
        ctx1.fillText('Standard PPO Chatter & Crash', 48, 192);
      } else {
        ctx1.moveTo(40, 145);
        ctx1.bezierCurveTo(140, 75, 310, 75, 415, 145);
        ctx1.stroke(); ctx1.setLineDash([]);
      }

      requestAnimationFrame(renderHyrl);
    }
    renderHyrl();
  }

  // ---------------------------------------------------------------------------
  // 2. Direction III Canvas: SO(3) Quaternion Hysteresis & Contact Control
  // ---------------------------------------------------------------------------
  const robCanvas = document.getElementById('homeRoboticsCanvas');
  if (robCanvas) {
    const ctx3 = robCanvas.getContext('2d');
    let robMode = 'quat';
    let phase3 = 0;

    window.setHomeRobMode = function(m) {
      robMode = m;
      document.getElementById('homeRobQuatBtn').classList.toggle('active', m === 'quat');
      document.getElementById('homeRobContactBtn').classList.toggle('active', m === 'contact');
      document.getElementById('homeRobCaption').innerHTML = m === 'quat'
        ? '<strong>SO(3) Antipodal Quaternion Tracking (S³):</strong> Under adversarial rotational disturbance ε_q = 0.4, hysteresis memory ϑ ∈ {-1, +1} with band ±δ_η prevents unwinding and eliminates pitch/yaw chattering on the quadrotor.'
        : '<strong>6-DoF Manipulator Hybrid Contact Control (CCTA 2026):</strong> Hysteresis-based mode switching between free-space motion and force regulation eliminates end-effector contact bouncing under sensor noise.';
    };

    function renderRobotics() {
      ctx3.clearRect(0, 0, robCanvas.width, robCanvas.height);
      phase3 = (phase3 + 0.012) % 1;

      if (robMode === 'quat') {
        // Left: S^3 Antipodal Circle with +-delta_eta Hysteresis Band
        const cx = 135, cy = 152, rad = 78;
        ctx3.fillStyle = '#0f172a'; ctx3.font = 'bold 11px Inter, sans-serif';
        ctx3.fillText('S³ Quaternion Double-Cover', 55, 32);

        // Hysteresis overlap arc near eta_e = 0 (top and bottom of circle)
        ctx3.strokeStyle = '#cbd5e1'; ctx3.lineWidth = 8;
        ctx3.beginPath(); ctx3.arc(cx, cy, rad, 0, Math.PI * 2); ctx3.stroke();

        // Active +1 hemisphere + delta_eta margin (Green)
        ctx3.strokeStyle = '#10b981'; ctx3.lineWidth = 5;
        ctx3.beginPath(); ctx3.arc(cx, cy, rad, -Math.PI * 0.62, Math.PI * 0.62); ctx3.stroke();

        // Antipodal poles +1 and -1
        ctx3.fillStyle = '#059669';
        ctx3.beginPath(); ctx3.arc(cx + rad, cy, 7, 0, Math.PI * 2); ctx3.fill();
        ctx3.fillStyle = '#1d4ed8';
        ctx3.beginPath(); ctx3.arc(cx - rad, cy, 7, 0, Math.PI * 2); ctx3.fill();

        ctx3.font = 'bold 10px monospace';
        ctx3.fillStyle = '#059669'; ctx3.fillText('q* = (+1,0)', cx + rad - 32, cy + 22);
        ctx3.fillStyle = '#1d4ed8'; ctx3.fillText('(-1,0)', cx - rad - 12, cy + 22);
        ctx3.fillStyle = '#be185d'; ctx3.fillText('η_e = 0 Boundary', cx - 44, cy - rad - 10);

        // Animated quadrotor attitude vector smoothly rotating to (+1,0)
        const ang = -Math.PI * 0.52 * (1 - phase3);
        const qx = cx + rad * Math.cos(ang);
        const qy = cy + rad * Math.sin(ang);
        ctx3.strokeStyle = '#059669'; ctx3.lineWidth = 2.5;
        ctx3.beginPath(); ctx3.moveTo(cx, cy); ctx3.lineTo(qx, qy); ctx3.stroke();
        ctx3.fillStyle = '#ffffff'; ctx3.beginPath(); ctx3.arc(qx, qy, 5, 0, Math.PI * 2); ctx3.fill(); ctx3.stroke();

        // Right: Time evolution of Attitude Error eta_e(t)
        const px0 = 260, py0 = 152, pw = 175;
        ctx3.fillStyle = '#0f172a'; ctx3.font = 'bold 11px Inter, sans-serif';
        ctx3.fillText('Hardware Attitude Error η_e(t)', 260, 32);

        ctx3.strokeStyle = '#475569'; ctx3.lineWidth = 1.4;
        ctx3.beginPath(); ctx3.moveTo(px0, 55); ctx3.lineTo(px0, 245); ctx3.moveTo(px0, py0); ctx3.lineTo(px0 + pw, py0); ctx3.stroke();

        // Hysteresis bounds
        ctx3.strokeStyle = '#94a3b8'; ctx3.setLineDash([4, 3]);
        ctx3.beginPath(); ctx3.moveTo(px0, py0 - 28); ctx3.lineTo(px0 + pw, py0 - 28);
        ctx3.moveTo(px0, py0 + 28); ctx3.lineTo(px0 + pw, py0 + 28); ctx3.stroke();
        ctx3.setLineDash([]);

        // PPO chattering curve (red)
        ctx3.strokeStyle = '#dc2626'; ctx3.lineWidth = 2;
        ctx3.beginPath();
        for (let x = 0; x <= pw; x += 3) {
          const y = py0 + Math.sin(x * 0.55 + phase3 * 25) * 14;
          if (x === 0) ctx3.moveTo(px0 + x, y); else ctx3.lineTo(px0 + x, y);
        }
        ctx3.stroke();

        // L-MultiHyRL smooth convergence (green)
        ctx3.strokeStyle = '#059669'; ctx3.lineWidth = 3;
        ctx3.beginPath();
        for (let x = 0; x <= pw; x += 4) {
          const y = py0 - 85 * (1 - Math.exp(-x / 52));
          if (x === 0) ctx3.moveTo(px0 + x, y); else ctx3.lineTo(px0 + x, y);
        }
        ctx3.stroke();

        ctx3.font = 'bold 10px Inter, sans-serif';
        ctx3.fillStyle = '#059669'; ctx3.fillText('L-MultiHyRL (ϑ=1)', px0 + 62, 82);
        ctx3.fillStyle = '#dc2626'; ctx3.fillText('PPO Chattering', px0 + 62, py0 + 44);
      } else {
        // Manipulator Contact & Force Regulation Comparison
        const padL = 55, padT = 48, pw = 375, ph = 195;
        ctx3.fillStyle = '#0f172a'; ctx3.font = 'bold 11.5px Inter, sans-serif';
        ctx3.fillText('6-DoF Manipulator Contact Force f(t) vs. Target Force f*', 55, 30);

        // Target force line & Hysteresis band
        const targetY = 105;
        const contactY = 185;
        ctx3.fillStyle = 'rgba(16, 185, 129, 0.1)';
        ctx3.fillRect(padL, targetY - 12, pw, 24);
        ctx3.strokeStyle = '#059669'; ctx3.setLineDash([5, 3]);
        ctx3.beginPath(); ctx3.moveTo(padL, targetY); ctx3.lineTo(padL + pw, targetY); ctx3.stroke();
        ctx3.setLineDash([]);

        // Axes
        ctx3.strokeStyle = '#475569'; ctx3.lineWidth = 1.5;
        ctx3.beginPath(); ctx3.moveTo(padL, padT); ctx3.lineTo(padL, padT + ph); ctx3.lineTo(padL + pw, padT + ph); ctx3.stroke();

        // Standard switching: contact chattering
        ctx3.strokeStyle = '#dc2626'; ctx3.lineWidth = 2;
        ctx3.beginPath();
        for (let x = 0; x <= pw; x += 4) {
          let y;
          if (x < 90) y = contactY + 35 * (1 - x / 90);
          else y = contactY - 35 + Math.sin(x * 0.5 + phase3 * 30) * 28;
          if (x === 0) ctx3.moveTo(padL + x, y); else ctx3.lineTo(padL + x, y);
        }
        ctx3.stroke();

        // Hybrid hysteresis controller: smooth approach + locked force regulation
        ctx3.strokeStyle = '#1d4ed8'; ctx3.lineWidth = 3.2;
        ctx3.beginPath();
        for (let x = 0; x <= pw; x += 4) {
          let y;
          if (x < 90) y = contactY + 35 * (1 - x / 90);
          else y = targetY + (contactY - targetY) * Math.exp(-(x - 90) / 42);
          if (x === 0) ctx3.moveTo(padL + x, y); else ctx3.lineTo(padL + x, y);
        }
        ctx3.stroke();

        ctx3.font = 'bold 10.5px Inter, sans-serif';
        ctx3.fillStyle = '#1d4ed8'; ctx3.fillText('Hybrid Hysteresis Force Regulation (Chatter-Free)', padL + 115, targetY - 16);
        ctx3.fillStyle = '#dc2626'; ctx3.fillText('Memoryless Threshold (Contact Bouncing)', padL + 115, contactY + 18);
      }

      requestAnimationFrame(renderRobotics);
    }
    renderRobotics();
  }
})();
</script>
