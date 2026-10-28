---
layout: page
title: "L-MultiHyRL: Robust 3D Obstacle Avoidance"
description: "Latent Multi-Policy Hysteresis-Based Reinforcement Learning for Perception-Driven 3D Navigation in SE(3) and SO(3)"
img: assets/img/publication_preview/lmultihyrl_preview.svg
importance: 1
category: work
related_publications: true
---

<div class="callout-box callout-amber">
  <h4>📄 Research Article &amp; Preprint Note</h4>
  <p>
    This article presents an overview of our upcoming manuscript, <strong>"Latent Multi-Policy Hysteresis-Based Reinforcement Learning for Robust 3D Obstacle Avoidance"</strong> by <em>Jan de Priester, Joseph Lullo, Kyle Volle, Prashant Ganesh, and Ricardo G. Sanfelice</em> (UC Santa Cruz &amp; Air Force Research Laboratory). As the manuscript is currently under review, the full PDF will be posted upon publication.
  </p>
</div>

Deep reinforcement learning (RL) enables agile autonomous flight directly from high-dimensional onboard sensory data, offering significantly lower inference latency than traditional optimization-based planners. Yet, standard continuous and memoryless RL policies suffer from a fundamental mathematical vulnerability in topologically obstructed spaces: **rapid decision switching (chattering)** near decision boundaries.

To resolve these vulnerabilities in three-dimensional flight, we introduce **Latent Multi-Policy Hysteresis-Based Reinforcement Learning (L-MultiHyRL)**—a perception-driven hybrid RL framework that eliminates chattering in both **$\text{SE}(3)$ spatial obstacle avoidance** and **$\text{SO}(3)$ 3D attitude tracking**, validated in simulation and on a physical **ModalAI Sentinel quadrotor**.

---

### 1. Why Standard Continuous RL Fails in 3D Navigation: Dual Topological Obstructions

When an autonomous aerial vehicle navigates a 3D environment with obstacles $p_{\text{ob}} \in \mathcal{P}_{\text{ob}} \subset \mathbb{R}^3$ toward a target $p^* \in \mathbb{R}^3$, its state evolves on $z = (p, q) \in \mathcal{Z} := \mathbb{R}^3 \times \mathbb{S}^3$, where $p \in \mathbb{R}^3$ is inertial position and $q = (\eta, \epsilon) \in \mathbb{S}^3$ is the unit quaternion representing body-frame attitude. Standard continuous RL policies (such as PPO) face **two distinct topological obstructions**:

1. **Spatial Obstacle Obstruction in $\mathbb{R}^3$:**
   Each obstacle punches a hole in the navigable space, rendering the free space *non-contractible*. To bypass an obstacle, any policy must choose a passage direction (e.g., passing left vs. right, or over vs. under). Because standard neural network policies parameterize continuous or memoryless discrete mappings, this choice induces a zero-width decision boundary where trajectories bifurcate. Near this boundary, small sensor noise or adversarial perturbations ($\varepsilon_p$ shifting perceived obstacle location along the intended passage vector $\hat{\nu}_{\text{int}}$) trick the policy into rapidly oscillating between conflicting bypass routes, stalling or crashing directly into the obstacle.
2. **Attitude Tracking Obstruction in $\text{SO}(3)$:**
   Because the unit 3-sphere $\mathbb{S}^3$ double-covers $\text{SO}(3)$, the vehicle is aligned with the target if the attitude error quaternion $q_e(z) = (\eta_e(z), \epsilon_e(z)) := q^{-1} \otimes q^*$ converges to either of the antipodal set-points $(\pm 1, 0)$. Tracking only a single pole $(+1, 0)$ causes the notorious **unwinding phenomenon**, where a vehicle near $(-1, 0)$ unnecessarily rotates $360^\circ$. Conversely, standard memoryless shortest-path tracking uses the discontinuous scalar error:
   $$e_\eta(z) := \operatorname{sgn}(\eta_e(z)) - \eta_e(z)$$
   This creates a strict switching boundary at $\eta_e = 0$ (when facing away from the target). An adversarial attitude disturbance of magnitude $\varepsilon_q$ repeatedly flips the sign of $\eta_e$ across zero, trapping the vehicle in high-frequency pitch and yaw command oscillations $(u_y, u_z) \in [-1, 1]^2$.

<div class="callout-box">
  <h4>💡 Why Recurrent Networks (RNNs) and Frame Stacking Don't Fix Chattering</h4>
  <p>
    While embedding memory via RNNs, LSTMs, or frame stacking may appear to help, these architectures embed memory through <em>continuous hidden states</em>. Consequently, the composite policy mapping remains continuous—meaning the topological decision boundary is not eliminated, but merely shifted into a higher-dimensional space where adversarial perturbations still induce chattering. Eliminating the boundary requires <strong>overlapping contractible partitions</strong> governed by discrete hysteresis logic.
  </p>
</div>

---

### 2. The L-MultiHyRL Architecture

Earlier hybrid RL approaches (such as 2D MultiHyRL) required global obstacle coordinates to construct state-space partitions and left the $\text{SO}(3)$ attitude obstruction unaddressed. **L-MultiHyRL** overcomes both limitations:

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-12">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/architecture_overview.svg" title="L-MultiHyRL Architecture Overview" class="img-fluid rounded" %}
  </div>
</div>
<div class="caption">
  Overview of the L-MultiHyRL architecture. Local visual projections $\mathcal{V} = (\mathcal{V}_{xy}, \mathcal{V}_{xz})$ are processed by the baseline policy's CNN branch $\Phi_{\text{CNN}}$ to extract local latent features $h_{\mathcal{V}}(z)$. A lightweight multi-head classifier $\Gamma(h_{\mathcal{V}}(z))$ predicts spatial partition membership directly from these local features, while the closed-loop hybrid supervisor $\mathcal{H}_s$ maintains discrete memory states $(\lambda, \vartheta)$ for chatter-free spatial route commitment and $\text{SO}(3)$ attitude tracking.
</div>

#### Step A: Topological Critical Point Identification & SVD Partitioning
Starting from a baseline continuous PPO policy $\kappa_{\text{global}}$ and its value network $\hat{V}_{\text{global}}$ in a canonical single-obstacle setting, **Algorithm 1** iteratively identifies the **topological critical set $\mathcal{M}^* \subset \mathcal{Z}$** where trajectories locally diverge around the obstacle:
- Candidate states $(p, q) \in S$ are shifted by $\pm \nu$ of radius $\alpha$ along the spatial value gradient $\nabla_p \hat{V}_{\text{global}}(p, q)$.
- If the forward-simulated trajectories $\phi_1, \phi_2$ separate beyond threshold $D_{\text{thresh}} > 0$, $(p, q)$ is recorded as a critical point, and the search propagates along orthonormal tangent vectors $t_1, t_2 \perp \nabla_p \hat{V}_{\text{global}}(p, q)$ with decaying search radius $\alpha \gets \beta \alpha$.
- Once $\mathcal{M}^*$ is identified, we enclose it in a convex hull with centroid $p^c$, compute a best-fit bisecting plane via **Singular Value Decomposition (SVD)**, and expand each half-space across the plane by a margin $\rho > 0$ (unioning with obstacle-free space $\mathcal{V} = 0$) to form overlapping contractible partitions $\mathcal{M}_1^{\text{exp}}$ and $\mathcal{M}_2^{\text{exp}}$ with non-intersecting boundaries $\partial \mathcal{M}_1^{\text{exp}} \cap \partial \mathcal{M}_2^{\text{exp}} = \varnothing$.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-12">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/critical_points_svd.svg" title="Topological Critical Points, SVD Partitioning, and Dual Hysteresis" class="img-fluid rounded" %}
  </div>
</div>
<div class="caption">
  (a) Identification of topological critical points $\mathcal{M}^*$ where perturbed trajectories $\phi_1, \phi_2$ diverge around the obstacle. (b) SVD best-fit plane through centroid $p^c$ expanded by $\rho$ to create a shared $2\rho$ hysteresis overlap (pink). (c) Overlapping hysteresis band $[-\delta_\eta, +\delta_\eta]$ in $\mathbb{S}^3$ quaternion space that prevents antipodal attitude chattering.
</div>

#### Step B: Sensor-Agnostic Latent Partition Classification & Local Policy Training
Instead of relying on global obstacle coordinates or training a separate vision backbone, L-MultiHyRL reuses the **learned local latent features** $h_{\mathcal{V}}(z) := \Phi_{\text{CNN}}(\mathcal{V}(z)) \in \mathbb{R}^{n_h}$ from the baseline policy's CNN branch.
- A single multi-head classifier $\Gamma: \mathbb{R}^{n_h} \to [0, 1]^{|\Lambda|}$ is trained directly on $h_{\mathcal{V}}(z)$ to output partition scores $\hat{y}_\lambda(z) := [\Gamma(h_{\mathcal{V}}(z))]_\lambda$. Because $\mathcal{V}$ is body-frame aligned, this classifier naturally generalizes to arbitrary obstacle positions and multi-obstacle environments!
- For each partition $\lambda \in \Lambda := \{1, 2\}$, a specialized local policy $\kappa_\lambda$ is trained using a logarithmic barrier reward penalty that keeps the vehicle strictly within its assigned expanded partition $\mathcal{M}_\lambda^{\text{exp}}$:
  $$R_\lambda(z) := R(z) - c_5 \max\left\{0,\, \ln\left(\frac{0.5}{\hat{y}_\lambda(z)}\right)\right\}$$

#### Step C: Discretized Closed-Loop Hybrid System $\mathcal{H}_s = (C, F_s, D, G)$
To orchestrate chatter-free switching, we formulate a discretized closed-loop hybrid system with state $x := (z, \lambda, \vartheta) \in \mathcal{Z} \times \Lambda \times \{-1, 1\}$, where $\lambda$ locks in the active spatial policy $\kappa_\lambda$ and $\vartheta$ locks in the active quaternion pole $\pm 1$ via the hysteresis-augmented attitude error $e_\eta(z, \vartheta) := \vartheta - \eta_e(z)$:
- **Flow Set $C$ & Flow Map $F_s$:** The vehicle flows continuously under $\kappa_\lambda(z, \vartheta)$ while $\hat{y}_\lambda(z) \ge \underline{\mu}$ (inside active partition) and $\vartheta \eta_e(z) \ge -\delta_\eta$ (inside active quaternion hysteresis band).
- **Jump Set $D$ & Jump Map $G$:** Discrete jumps occur only when $\hat{y}_\lambda(z) \le \underline{\mu} < 0.5$ (switching $\lambda$ to a partition $\ell$ where $\hat{y}_\ell(z) \ge \overline{\mu} > 0.5$) or when $\vartheta \eta_e(z) \le -\delta_\eta$ (flipping $\vartheta \mapsto -\vartheta$, which maps the state strictly into the interior of $C_{-\vartheta}$ by a margin of $2\delta_\eta$).
- **Guaranteed Chattering Elimination:** Whenever the attitude hysteresis margin satisfies $\delta_\eta > \sin(\varepsilon_q / 2)$ (e.g., $\delta_\eta = 0.2 > \sin(0.2) \approx 0.199$ for $\varepsilon_q = 0.4$), adversarial attitude disturbances cannot push the error across the opposing switching threshold.

---

### 3. Interactive Simulation Sandbox: Standard PPO vs. L-MultiHyRL

Use the interactive simulator below to compare **Standard Continuous PPO** against **L-MultiHyRL** under adversarial obstacle shifts ($\varepsilon_p = 0.20$) and attitude perturbations ($\varepsilon_q = 0.40$):

<div class="interactive-lab-card">
  <div class="direction-canvas-wrap">
    <div class="canvas-header">
      <span class="canvas-title">Interactive 3D/2D Projection Simulator: L-MultiHyRL vs. Standard PPO</span>
      <span class="canvas-live-dot">● Interactive Simulation</span>
    </div>
    <canvas id="lmultihyrlArticleCanvas" width="760" height="320"></canvas>
    <div class="canvas-controls">
      <div>
        <button class="canvas-btn active" id="lmBtnMulti" onclick="window.setLMMode('multi')">Multi-Obstacle SE(3)</button>
        <button class="canvas-btn" id="lmBtnSingle" onclick="window.setLMMode('single')">Single-Obstacle SE(3)</button>
        <button class="canvas-btn" id="lmBtnAttitude" onclick="window.setLMMode('attitude')">SO(3) Attitude Hysteresis</button>
      </div>
      <div>
        <label style="margin-right: 8px; font-weight: 600; color: #334155;">Adversarial Perturbation ε:</label>
        <input type="range" id="lmNoiseSlider" min="0" max="0.25" step="0.05" value="0.20" style="vertical-align: middle; width: 110px;" oninput="window.setLMNoise(this.value)">
        <span id="lmNoiseLabel" style="font-family: monospace; font-weight: 700; color: #1d4ed8; margin-left: 4px;">0.20</span>
      </div>
    </div>
    <div class="canvas-caption" id="lmCanvasCaption">
      <strong>Multi-Obstacle SE(3) Avoidance:</strong> Under adversarial obstacle shift $\varepsilon_p = 0.20$, Standard PPO (red dashed) chatters across the decision boundaries and collides, while L-MultiHyRL (blue $\lambda=1$, green $\lambda=2$) commits to its expanded partition and achieves a 100% success rate.
    </div>
  </div>
</div>

<script>
(function() {
  const canvas = document.getElementById('lmultihyrlArticleCanvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  let mode = 'multi';
  let eps = 0.20;
  let t = 0;

  window.setLMMode = function(m) {
    mode = m;
    t = 0;
    document.getElementById('lmBtnMulti').classList.toggle('active', m === 'multi');
    document.getElementById('lmBtnSingle').classList.toggle('active', m === 'single');
    document.getElementById('lmBtnAttitude').classList.toggle('active', m === 'attitude');
    const cap = document.getElementById('lmCanvasCaption');
    if (m === 'multi') {
      cap.innerHTML = '<strong>Multi-Obstacle SE(3) Avoidance:</strong> Under adversarial obstacle shift ε_p = ' + eps.toFixed(2) + ', Standard PPO (red) chatters between passage routes and crashes, whereas L-MultiHyRL (blue λ=1, green λ=2) maintains 100% success across 242 initial conditions.';
    } else if (m === 'single') {
      cap.innerHTML = '<strong>Single-Obstacle SE(3) Avoidance &amp; 2ρ Hysteresis Overlap:</strong> Pink band shows the SVD-derived overlapping region M_1^{exp} ∩ M_2^{exp}. Once committed to λ=1 or λ=2, the vehicle ignores adversarial shifts up to the partition boundary.';
    } else {
      cap.innerHTML = '<strong>SO(3) Attitude Tracking under Adversarial Rotation (ε_q = 0.4):</strong> Standard PPO chatters across η_e = 0 with violent pitch/yaw oscillations, while L-MultiHyRL holds active pole ϑ ∈ {-1,+1} within hysteresis band ±δ_η = ±0.2.';
    }
  };

  window.setLMNoise = function(val) {
    eps = parseFloat(val);
    document.getElementById('lmNoiseLabel').textContent = eps.toFixed(2);
    window.setLMMode(mode);
  };

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    t = (t + 0.008) % 1;

    // Subtle grid
    ctx.strokeStyle = '#e2e8f0';
    ctx.lineWidth = 1;
    for (let x = 40; x < canvas.width; x += 60) {
      ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
    }
    for (let y = 40; y < canvas.height; y += 60) {
      ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
    }

    if (mode === 'single' || mode === 'multi') {
      const obstacles = mode === 'single'
        ? [{x: 370, y: 160, r: 44}]
        : [{x: 250, y: 115, r: 36}, {x: 250, y: 215, r: 36}, {x: 460, y: 160, r: 40}];

      // Draw hysteresis overlap bands
      obstacles.forEach(ob => {
        ctx.fillStyle = 'rgba(236, 72, 153, 0.12)';
        ctx.fillRect(ob.x - 140, ob.y - 24, 140, 48);
        ctx.strokeStyle = 'rgba(219, 39, 119, 0.45)';
        ctx.setLineDash([4, 4]);
        ctx.beginPath();
        ctx.moveTo(ob.x - 140, ob.y - 24); ctx.lineTo(ob.x, ob.y - 24);
        ctx.moveTo(ob.x - 140, ob.y + 24); ctx.lineTo(ob.x, ob.y + 24);
        ctx.stroke();
        ctx.setLineDash([]);
      });

      // Draw obstacles
      obstacles.forEach(ob => {
        const grad = ctx.createRadialGradient(ob.x - 10, ob.y - 10, 6, ob.x, ob.y, ob.r);
        grad.addColorStop(0, '#cbd5e1');
        grad.addColorStop(1, '#475569');
        ctx.fillStyle = grad;
        ctx.beginPath(); ctx.arc(ob.x, ob.y, ob.r, 0, Math.PI * 2); ctx.fill();
        ctx.strokeStyle = '#1e293b'; ctx.lineWidth = 1.8; ctx.stroke();
      });

      // Target Star
      const tx = 690, ty = 160;
      ctx.fillStyle = '#f59e0b';
      ctx.beginPath(); ctx.arc(tx, ty, 11, 0, Math.PI * 2); ctx.fill();
      ctx.strokeStyle = '#b45309'; ctx.lineWidth = 2; ctx.stroke();
      ctx.fillStyle = '#0f172a'; ctx.font = 'bold 11px Inter, sans-serif';
      ctx.fillText('Target p*', tx - 24, ty + 28);

      // L-MultiHyRL Trajectories (Blue lambda=1 & Green lambda=2)
      const paths = mode === 'single' ? [
        {color: '#1d4ed8', label: 'L-MultiHyRL (λ=1)', pts: [[60,145],[190,140],[370,78],[540,120],[690,160]]},
        {color: '#059669', label: 'L-MultiHyRL (λ=2)', pts: [[60,175],[190,180],[370,242],[540,200],[690,160]]}
      ] : [
        {color: '#1d4ed8', label: 'L-MultiHyRL (λ=1)', pts: [[55,115],[145,105],[250,48],[355,85],[460,85],[585,125],[690,160]]},
        {color: '#059669', label: 'L-MultiHyRL (λ=2)', pts: [[55,215],[145,222],[250,275],[355,235],[460,235],[585,195],[690,160]]}
      ];

      paths.forEach(p => {
        ctx.strokeStyle = p.color;
        ctx.lineWidth = 3.2;
        ctx.beginPath();
        ctx.moveTo(p.pts[0][0], p.pts[0][1]);
        for (let i = 1; i < p.pts.length; i++) {
          const prev = p.pts[i - 1], curr = p.pts[i];
          const cx = (prev[0] + curr[0]) / 2;
          ctx.bezierCurveTo(cx, prev[1], cx, curr[1], curr[0], curr[1]);
        }
        ctx.stroke();

        // Animated drone dot along path
        const seg = t * (p.pts.length - 1);
        const idx = Math.min(Math.floor(seg), p.pts.length - 2);
        const frac = seg - idx;
        const px = p.pts[idx][0] + (p.pts[idx+1][0] - p.pts[idx][0]) * frac;
        const py = p.pts[idx][1] + (p.pts[idx+1][1] - p.pts[idx][1]) * frac;
        ctx.fillStyle = p.color;
        ctx.beginPath(); ctx.arc(px, py, 6, 0, Math.PI * 2); ctx.fill();
      });

      // Standard PPO Trajectory (chattering if eps > 0.05)
      const crashOb = obstacles[0];
      ctx.strokeStyle = '#dc2626';
      ctx.lineWidth = 2.4;
      ctx.setLineDash([5, 3]);
      ctx.beginPath();
      if (eps >= 0.10) {
        const startY = crashOb.y;
        ctx.moveTo(60, startY);
        const steps = 9;
        const endX = crashOb.x - crashOb.r;
        for (let i = 1; i <= steps; i++) {
          const sx = 60 + (endX - 60) * (i / steps);
          const chatter = (i % 2 === 0 ? 1 : -1) * (18 * (eps / 0.20));
          ctx.lineTo(sx, startY + (i === steps ? 0 : chatter));
        }
        ctx.stroke();
        ctx.setLineDash([]);
        ctx.fillStyle = '#dc2626';
        ctx.beginPath(); ctx.arc(endX, startY, 6, 0, Math.PI * 2); ctx.fill();
        ctx.font = 'bold 11px Inter, sans-serif';
        ctx.fillText('PPO Chatter & Collision', endX - 115, startY - 22);
      } else {
        ctx.moveTo(60, crashOb.y);
        ctx.bezierCurveTo(200, crashOb.y - 65, 480, crashOb.y - 65, 690, 160);
        ctx.stroke();
        ctx.setLineDash([]);
      }
    } else {
      // Attitude Tracking SO(3) plot: Left = eta_e(t), Right = Control u_y(t)
      ctx.fillStyle = '#0f172a';
      ctx.font = 'bold 12px Inter, sans-serif';
      ctx.fillText('Scalar Attitude Error η_e(t) in SO(3)', 60, 32);
      ctx.fillText('Rotational Control Command u_z(t)', 430, 32);

      // Left Axes
      ctx.strokeStyle = '#475569'; ctx.lineWidth = 1.5;
      ctx.beginPath(); ctx.moveTo(60, 50); ctx.lineTo(60, 270); ctx.moveTo(60, 160); ctx.lineTo(350, 160); ctx.stroke();
      // Hysteresis thresholds +- delta_eta
      ctx.strokeStyle = '#64748b'; ctx.setLineDash([4, 4]);
      ctx.beginPath(); ctx.moveTo(60, 130); ctx.lineTo(350, 130); ctx.moveTo(60, 190); ctx.lineTo(350, 190); ctx.stroke();
      ctx.setLineDash([]);
      ctx.fillStyle = '#475569'; ctx.font = '10px monospace';
      ctx.fillText('+1.0', 28, 60); ctx.fillText('+δ_η', 28, 133); ctx.fillText('0.0', 34, 163); ctx.fillText('-δ_η', 28, 193); ctx.fillText('-1.0', 28, 265);

      // L-MultiHyRL smooth convergence to +1
      ctx.strokeStyle = '#059669'; ctx.lineWidth = 3;
      ctx.beginPath(); ctx.moveTo(60, 160);
      for (let x = 0; x <= 280; x += 5) {
        const y = 160 - 98 * (1 - Math.exp(-x / 85));
        ctx.lineTo(60 + x, y);
      }
      ctx.stroke();

      // Standard PPO trapped chattering around 0
      ctx.strokeStyle = '#dc2626'; ctx.lineWidth = 2.2;
      ctx.beginPath(); ctx.moveTo(60, 160);
      for (let x = 0; x <= 280; x += 4) {
        const y = 160 + (eps > 0.05 ? Math.sin(x * 0.6 + t * 25) * 14 : -98 * (1 - Math.exp(-x / 85)));
        ctx.lineTo(60 + x, y);
      }
      ctx.stroke();

      // Right Axes (Control oscillation)
      ctx.strokeStyle = '#475569'; ctx.lineWidth = 1.5;
      ctx.beginPath(); ctx.moveTo(430, 50); ctx.lineTo(430, 270); ctx.moveTo(430, 160); ctx.lineTo(720, 160); ctx.stroke();
      ctx.fillStyle = '#475569';
      ctx.fillText('+1', 408, 65); ctx.fillText('0', 414, 163); ctx.fillText('-1', 408, 260);

      if (eps > 0.05) {
        // PPO square-wave chattering between -1 and +1
        ctx.strokeStyle = 'rgba(220, 38, 38, 0.75)'; ctx.lineWidth = 1.6;
        ctx.beginPath();
        for (let x = 0; x <= 280; x += 6) {
          const y = ((Math.floor((x + t * 60) / 6) % 2) === 0) ? 65 : 255;
          if (x === 0) ctx.moveTo(430 + x, y); else ctx.lineTo(430 + x, y);
        }
        ctx.stroke();
      }
      // L-MultiHyRL smooth decaying control
      ctx.strokeStyle = '#1d4ed8'; ctx.lineWidth = 3;
      ctx.beginPath();
      for (let x = 0; x <= 280; x += 5) {
        const y = 160 - 95 * Math.exp(-x / 75);
        if (x === 0) ctx.moveTo(430 + x, y); else ctx.lineTo(430 + x, y);
      }
      ctx.stroke();
    }

    requestAnimationFrame(draw);
  }
  draw();
})();
</script>

---

### 4. Hardware Validation on a Physical Quadrotor in $\text{SE}(3)$

To verify that the formal robustness properties established in simulation transfer to the real world, we deployed both the standard continuous PPO agent and the **L-MultiHyRL** agent on a physical **ModalAI Sentinel quadrotor** equipped with an onboard **VOXL 2** companion computer in an indoor flight arena.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-12">
    {% include figure.liquid loading="eager" path="assets/img/lmultihyrl/hardware_sim2real.svg" title="ModalAI Sentinel Sim-to-Real Pipeline and Cumulative Success Rates" class="img-fluid rounded" %}
  </div>
</div>
<div class="caption">
  Left: Zero-retraining sim-to-real deployment architecture on the ModalAI Sentinel quadrotor using a parallel onboard kinematic simulation to bridge first-order policy kinematics with second-order underactuated PX4 flight control. Right: Cumulative success rate across dense 3D initial condition grids under varying adversarial obstacle shifts $\varepsilon_p$.
</div>

#### Bridging the Sim-to-Real Gap Without Retraining
Because the RL policies are trained using first-order 3D nonholonomic kinematics:
$$\dot{z} = \begin{bmatrix} \dot{p} \\ \dot{q} \end{bmatrix} = \begin{bmatrix} \mathcal{R}(q) v \\ \frac{1}{2} q \otimes \omega \end{bmatrix}$$
a physical sim-to-real gap arises: a real quadrotor is an underactuated second-order system that must tilt (roll and pitch) to translate, rather than directly tracking a 3D kinematic attitude quaternion. To bridge this gap **without any retraining or fine-tuning**:
- A motion capture system streams real-time 3D physical position estimates $p$ at **120 Hz** over **ROS 2 (Humble)** to the VOXL 2 companion computer.
- In parallel, the VOXL 2 runs a lightweight **onboard kinematic attitude propagation** $q \in \mathbb{S}^3$ initialized at $q_0 \in \mathbb{S}^3$, combining it with the real-time physical position $p$ to construct the multimodal observation at **25 Hz**.
- The active L-MultiHyRL policy outputs body-frame velocity commands, which are rotated via $\mathcal{R}(q)$ into the inertial frame and sent to the inner-loop **PX4 flight controller** as velocity tracking set-points.

#### Key Experimental Takeaways
- **100% Obstacle Bypass Robustness:** Across 225 single-obstacle and 242 multi-obstacle 3D initial conditions, **L-MultiHyRL maintains a 100% success rate** under all evaluated adversarial shift magnitudes ($\varepsilon_p \in [0, 0.25]$), whereas the standard PPO agent's success rate drops to **60.4%** (single-obstacle) and **68.6%** (multi-obstacle) due to chattering and collisions.
- **Elimination of Hardware Attitude Chattering:** Under adversarial attitude perturbations ($\varepsilon_q = 0.4$), the physical quadrotor under standard PPO becomes trapped oscillating at $\eta_e \in \{0, \pm 0.13\}$, whereas L-MultiHyRL with hysteresis margin $\delta_\eta = 0.2$ smoothly aligns the quadrotor to the target set-point without control oscillations.
- **The Robustness vs. Nominal Path-Length Trade-off:** Because hysteresis locks in the active attitude pole $\vartheta$ and passage route $\lambda$, L-MultiHyRL commits to its chosen side even when a perturbation momentarily makes the alternative path geometrically shorter—trading a slight increase in arrival time for guaranteed chatter-free safety.

For further background on our hybrid reinforcement learning and hysteresis-based switching frameworks, see {% cite dePriester2026LMultiHyRL dePriester2025TimerHyRL dePriester2024MultiHyRL %}.
