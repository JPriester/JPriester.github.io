---
layout: page
title: "Intrinsic Stability in Discrete-Time RL"
description: "Why Unmodified Discounted Reinforcement Learning Intrinsically Guarantees Closed-Loop Practical Asymptotic Stability"
img: assets/img/publication_preview/intrinsic_stability_preview.svg
importance: 2
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Published at RLC 2026</h4>
  <p>
    This article accompanies our paper, <strong>"Intrinsic Closed-Loop Practical Asymptotic Stability in Discrete-Time Reinforcement Learning"</strong> by <em>Jan de Priester and Ricardo G. Sanfelice</em>, accepted at the <strong>2026 Reinforcement Learning Conference (RLC)</strong>.
    <a href="{{ '/assets/pdf/DT_RL_paper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 6px;">[Read Full Paper PDF →]</a>
  </p>
</div>

Deep reinforcement learning (RL) has achieved remarkable empirical success across complex continuous control benchmarks—including dynamic legged locomotion, high-speed autonomous drone racing, and dexterous in-hand manipulation. Fundamentally, these tasks are **set-stabilization problems**: rather than driving a system to a single static equilibrium point, the control policy must steer the state $x \in \mathcal{X}$ toward—and indefinitely maintain it within—a compact geometric target set $\mathcal{A} \subset \mathcal{X}$ (such as a periodic walking gait limit cycle or a spatial racing trajectory).

To deploy learned feedback laws in safety-critical settings, a large body of literature modifies the RL algorithm to enforce strict asymptotic stability—for instance, by jointly training neural Lyapunov functions, imposing Lyapunov decrease constraints during policy updates, or wrapping the policy in an external safety filter. While these tools are valuable for training-time safety during exploration, they **alter the core RL objective**, restrict natural exploration, and demand exact zero-error convergence that is practically unattainable with finite-capacity neural network function approximators.

In this work, we ask a fundamental question:
> **Does the unmodified discounted RL objective already guarantee closed-loop stability on its own?**

We prove that the answer is **yes**: under mild regularity and controllability assumptions, near-optimal neural network policies trained on the **unmodified discounted RL objective** intrinsically guarantee **Uniform Practical Asymptotic Stability (UPAS)**.

---

### 1. Why Steady-State Error in RL is a Feature of Discounting, Not a Bug

In classical control theory, asymptotic stability is an *infinite-horizon* property. In contrast, discrete-time RL optimizes an expected *discounted* return over a compact class of continuous feedback laws $\kappa \in \mathbb{K} \subset \mathcal{C}(\mathcal{X}, \mathcal{U})$:

$$\max_{\kappa \in \mathbb{K}} \mathcal{J}_\gamma(\kappa), \qquad \mathcal{J}_\gamma(\kappa) := \int_{\mathcal{X}} V_\gamma^\kappa(x)\, p_0(x)\, \mathrm{d}x, \qquad V_\gamma^\kappa(x_0) := \sum_{k=0}^{\infty} \gamma^k R\big(x_k^\kappa, \kappa(x_k^\kappa)\big)$$

where $\gamma \in (0, 1)$ is the discount factor and $x_{k+1} = g(x_k, u_k)$ are the discrete-time dynamics.

Why do standard RL agents often settle into a small steady-state offset near the target set $\mathcal{A}$?
- A discount factor $\gamma < 1$ effectively **truncates the optimization horizon**, producing a slightly myopic agent that weighs immediate control effort more heavily than distant future state penalties.
- Once the state gets "close enough" to $\mathcal{A}$, the immediate cost of applying a corrective control input outweighs the discounted future benefit of reducing the state error further.
- Consequently, the small steady-state error observed in practice is **not an algorithmic failure**—it is the direct mathematical consequence of optimizing a discounted return!

---

### 2. Bridging Deep RL and Input-to-State Stability (ISS)

To establish formal closed-loop guarantees without modifying the RL algorithm, we adapt the discrete-time **Input-to-State Stability (ISS)** framework (Jiang & Wang, 2001) and draw inspiration from discounted Model Predictive Control (Postoyan et al., 2016). While MPC relies on exact online optimization, deep RL uses parameterized feedforward neural networks $\kappa_{\psi^*} \in \mathcal{NN}_{n,m,w}^\phi \cap \mathbb{K}$ that achieve near-optimal expected returns at best.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-12">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/iss_bridge_overview.svg" title="ISS Bridge Overview for Intrinsic Practical Stability in RL" class="img-fluid rounded" %}
  </div>
</div>
<div class="caption">
  Overview of our theoretical framework. Both the horizon truncation from the discount factor ($\gamma &lt; 1$) and the suboptimality of the parameterized neural network ($\delta &gt; 0$) are modeled as bounded disturbances ($d_\gamma$ and $\varepsilon_\delta / \gamma$) in an ISS-Lyapunov analysis using the negated optimal value function $Y(x) := -V_\gamma^{\kappa_\gamma^*}(x)$.
</div>

Our proof builds a three-step theoretical bridge from the expected RL objective to pointwise closed-loop trajectory bounds:

1. **From Expected Optimality to Pointwise Optimality (Lemma 3.8 &amp; Lemma 4.4):**
   Although RL maximizes an *expected* return $\mathcal{J}_\gamma(\kappa)$ weighted by a strictly positive initial state distribution $p_0(x) > 0$, we leverage the compactness of the admissible policy space $\mathbb{K}$ under the supremum norm to prove that bounding the expected suboptimality $\mathcal{J}_\gamma(\kappa_\gamma^*) - \mathcal{J}_\gamma(\kappa_{\psi^*}) \le \delta$ guarantees a uniform **worst-case pointwise value bound** across every individual state $x \in \mathcal{X}$:
   $$\max_{x \in \mathcal{X}} \Big\{ V_\gamma^{\kappa_\gamma^*}(x) - V_\gamma^{\kappa_{\psi^*}}(x) \Big\} \le \alpha_\gamma(\delta) =: \varepsilon_\delta$$
2. **The Negated Optimal Value Function as an ISS-Lyapunov Candidate:**
   Let $\sigma: \mathcal{X} \to \mathbb{R}_{\ge 0}$ be a proper indicator measuring the distance to the target set $\mathcal{A}$ (with $\sigma(x) = 0 \iff x \in \mathcal{A}$). Under a strict reward penalty $R(x, u) \le -\underline{\alpha}(\sigma(x))$ and the existence of a stabilizing baseline policy $\kappa_s \in \mathbb{K}$, the negated optimal value function $Y(x) := -V_\gamma^{\kappa_\gamma^*}(x)$ satisfies the sandwich bounds:
   $$\underline{\alpha}_Y(\sigma(x)) \le Y(x) \le \overline{\alpha}_Y(\sigma(x)) \qquad \forall x \in \mathcal{X}$$
   Evaluating the one-step Bellman difference along trajectories $x^+ = g(x, \kappa_{\psi^*}(x))$ of the parameterized neural network policy yields the ISS-Lyapunov decrease condition:
   $$Y(x^+) - Y(x) \le -\alpha_Y(Y(x)) + \underbrace{\frac{1-\gamma}{\gamma}\overline{\alpha}_V^{\kappa_s}(\sigma_{\max})}_{d_\gamma \text{ (Discounting)}} + \underbrace{\frac{\varepsilon_\delta}{\gamma}}_{\text{NN Error}} =: -\alpha_Y(Y(x)) + \tilde{d}_{\gamma, \delta}$$
3. **Uniform Practical Asymptotic Stability (Theorem 4.5):**
   Because $Y(x)$ is a valid discrete-time ISS-Lyapunov function with respect to the constant disturbance $\tilde{d}_{\gamma, \delta}$, every closed-loop trajectory $\{x_k\}_{k \in \mathbb{N}}$ generated by the parameterized neural network policy $\kappa_{\psi^*}$ satisfies:
   $$\sigma(x_k) \le \max\Big\{\beta(\sigma(x_0), k),\; \tilde{\nu}(\gamma, \delta)\Big\} \quad \forall k \in \mathbb{N}, \qquad \text{with } \lim_{\gamma \to 1}\Big(\lim_{\delta \to 0} \tilde{\nu}(\gamma, \delta)\Big) = 0$$

<div class="callout-box">
  <h4>🛠️ Practical Engineering Takeaway (Corollary 3.9.1)</h4>
  <p>
    In practice, pushing $\gamma \to 1$ often causes severe value-function variance and training instability. Our analysis proves that <strong>taking the strict limit $\gamma \to 1$ is unnecessary</strong>: for any desired target precision tolerance $\rho > 0$ (e.g., keeping a robotic arm within $2\text{ mm}$ of a goal manifold), there exists a strictly smaller discount factor $\gamma^* \in (0, 1)$ such that any $\gamma \in [\gamma^*, 1)$ guarantees ultimate convergence within $\rho$.
  </p>
</div>

---

### 3. Interactive Analytical Sandbox: Explore $\gamma$ and $\delta$ in Real Time

In **Examples 3 and 4** of the paper, we analyze the discrete-time system $x^+ = x + 0.1 u$ on $\mathcal{X} = \mathcal{U} = [-1, 1]$ with target $\mathcal{A} = \{0\}$, indicator $\sigma(x) = |x|$, and reward $R(x, u) = -(x^2 + 0.5|u|)$. Because the linear control penalty $0.5|u|$ dominates the quadratic state penalty $x^2$ near the origin, the exact optimal policy $\kappa_\gamma^*(x)$ exhibits an exact analytical steady-state boundary:

$$\nu(\gamma) = \frac{5(1 - \gamma)}{2\gamma}, \qquad \tilde{\nu}(\gamma, \delta) = \frac{\nu(\gamma)}{w_\delta}$$

where $w_\delta \in (0, 1]$ models the weight accuracy of a two-neuron ReLU network $\kappa_{\psi^*}(x)$ as approximation error $\delta$ shrinks during training. Use the sliders below to explore how $\gamma$ and $\delta$ govern the closed-loop trajectory $\{x_k\}_{k \in \mathbb{N}}$:

<div class="interactive-lab-card">
  <div class="direction-canvas-wrap">
    <div class="canvas-header">
      <span class="canvas-title">Interactive Closed-Loop UPAS Trajectory Explorer: σ(x_k) vs. Step k</span>
      <span class="canvas-live-dot">● Exact Closed-Form Dynamics</span>
    </div>
    <canvas id="upasArticleCanvas" width="760" height="300"></canvas>
    <div class="canvas-controls">
      <div>
        <label style="font-weight: 600; color: #334155;">Discount Factor γ:</label>
        <input type="range" id="upasGammaSlider" min="0.84" max="0.99" step="0.01" value="0.90" style="vertical-align: middle; width: 120px;" oninput="window.updateUPASPlot()">
        <span id="upasGammaVal" style="font-family: monospace; font-weight: 700; color: #1d4ed8; margin-right: 14px;">0.90</span>

        <label style="font-weight: 600; color: #334155;">NN Approximation Error δ:</label>
        <input type="range" id="upasDeltaSlider" min="0.00" max="0.30" step="0.02" value="0.04" style="vertical-align: middle; width: 120px;" oninput="window.updateUPASPlot()">
        <span id="upasDeltaVal" style="font-family: monospace; font-weight: 700; color: #059669;">0.04</span>
      </div>
      <div>
        <span id="upasBoundReadout" style="font-family: monospace; font-size: 0.8rem; font-weight: 700; color: #0f172a; background: #eff6ff; padding: 4px 8px; border-radius: 6px; border: 1px solid #bfdbfe;">
          ν̃(γ, δ) = 0.320
        </span>
      </div>
    </div>
    <div class="canvas-caption">
      <strong>Blue solid curve:</strong> Parameterized neural network trajectory $\sigma(x_k)$ converging to $\tilde{\nu}(\gamma, \delta)$. <strong>Green dashed curve:</strong> Exact optimal policy ($\delta = 0$) converging to $\nu(\gamma) = \frac{5(1-\gamma)}{2\gamma}$. Increase $\gamma \to 0.99$ and decrease $\delta \to 0.00$ to watch the steady-state neighborhood vanish!
    </div>
  </div>
</div>

<script>
(function() {
  const canvas = document.getElementById('upasArticleCanvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');

  function simulateTrajectory(gamma, wDelta) {
    const nu = (5 * (1 - gamma)) / (2 * gamma);
    const pts = [];
    let x = 1.0;
    for (let k = 0; k <= 30; k++) {
      pts.push({k: k, x: Math.abs(x)});
      const uRaw = -10 * Math.max(wDelta * x - nu, 0) + 10 * Math.max(-wDelta * x - nu, 0);
      const u = Math.max(-1, Math.min(1, uRaw));
      x = x + 0.1 * u;
    }
    return {pts: pts, bound: nu / wDelta, exactBound: nu};
  }

  window.updateUPASPlot = function() {
    const gamma = parseFloat(document.getElementById('upasGammaSlider').value);
    const delta = parseFloat(document.getElementById('upasDeltaSlider').value);
    document.getElementById('upasGammaVal').textContent = gamma.toFixed(2);
    document.getElementById('upasDeltaVal').textContent = delta.toFixed(2);

    // Map delta in [0, 0.30] to network weight dilation w_delta in [0.60, 1.00]
    const wDelta = 1.0 - (delta / 0.30) * 0.40;
    const simNN = simulateTrajectory(gamma, wDelta);
    const simExact = simulateTrajectory(gamma, 1.0);

    document.getElementById('upasBoundReadout').textContent = 'ν̃(γ, δ) = ' + Math.min(1.0, simNN.bound).toFixed(3);

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    const padL = 65, padR = 40, padT = 28, padB = 42;
    const plotW = canvas.width - padL - padR;
    const plotH = canvas.height - padT - padB;

    const toX = k => padL + (k / 30) * plotW;
    const toY = v => padT + (1 - Math.min(1.0, Math.max(0, v))) * plotH;

    // Grid
    ctx.strokeStyle = '#e2e8f0'; ctx.lineWidth = 1;
    [0.0, 0.2, 0.4, 0.6, 0.8, 1.0].forEach(val => {
      const y = toY(val);
      ctx.beginPath(); ctx.moveTo(padL, y); ctx.lineTo(padL + plotW, y); ctx.stroke();
      ctx.fillStyle = '#64748b'; ctx.font = '11px monospace';
      ctx.fillText(val.toFixed(1), padL - 32, y + 4);
    });
    for (let k = 0; k <= 30; k += 5) {
      const x = toX(k);
      ctx.beginPath(); ctx.moveTo(x, padT); ctx.lineTo(x, padT + plotH); ctx.stroke();
      ctx.fillStyle = '#64748b'; ctx.font = '11px monospace';
      ctx.fillText('k=' + k, x - 12, padT + plotH + 22);
    }

    // Shaded UPAS Neighborhood Band
    const boundY = toY(simNN.bound);
    ctx.fillStyle = 'rgba(29, 78, 216, 0.08)';
    ctx.fillRect(padL, boundY, plotW, padT + plotH - boundY);
    ctx.strokeStyle = '#93c5fd'; ctx.setLineDash([5, 4]); ctx.lineWidth = 1.5;
    ctx.beginPath(); ctx.moveTo(padL, boundY); ctx.lineTo(padL + plotW, boundY); ctx.stroke();
    ctx.setLineDash([]);

    // Exact bound line
    const exactY = toY(simExact.exactBound);
    ctx.strokeStyle = '#10b981'; ctx.setLineDash([3, 3]); ctx.lineWidth = 1.5;
    ctx.beginPath(); ctx.moveTo(padL, exactY); ctx.lineTo(padL + plotW, exactY); ctx.stroke();
    ctx.setLineDash([]);

    // Axes
    ctx.strokeStyle = '#334155'; ctx.lineWidth = 1.8;
    ctx.beginPath(); ctx.moveTo(padL, padT); ctx.lineTo(padL, padT + plotH); ctx.lineTo(padL + plotW, padT + plotH); ctx.stroke();

    // Draw Exact Trajectory (Green Dashed)
    ctx.strokeStyle = '#059669'; ctx.lineWidth = 2.4; ctx.setLineDash([6, 4]);
    ctx.beginPath();
    simExact.pts.forEach((p, idx) => {
      if (idx === 0) ctx.moveTo(toX(p.k), toY(p.x)); else ctx.lineTo(toX(p.k), toY(p.x));
    });
    ctx.stroke(); ctx.setLineDash([]);

    // Draw NN Parameterized Trajectory (Blue Solid + Markers)
    ctx.strokeStyle = '#1d4ed8'; ctx.lineWidth = 3;
    ctx.beginPath();
    simNN.pts.forEach((p, idx) => {
      if (idx === 0) ctx.moveTo(toX(p.k), toY(p.x)); else ctx.lineTo(toX(p.k), toY(p.x));
    });
    ctx.stroke();
    simNN.pts.forEach(p => {
      ctx.fillStyle = '#ffffff'; ctx.strokeStyle = '#1d4ed8'; ctx.lineWidth = 2;
      ctx.beginPath(); ctx.arc(toX(p.k), toY(p.x), 3.5, 0, Math.PI * 2); ctx.fill(); ctx.stroke();
    });

    // Labels
    ctx.fillStyle = '#1d4ed8'; ctx.font = 'bold 11px monospace';
    ctx.fillText('Parameterized NN Bound ν̃(γ,δ) = ' + Math.min(1, simNN.bound).toFixed(3), padL + plotW - 265, Math.max(padT + 14, boundY - 7));
    ctx.fillStyle = '#047857';
    ctx.fillText('Exact Discounted Bound ν(γ) = ' + Math.min(1, simExact.exactBound).toFixed(3), padL + plotW - 265, Math.min(padT + plotH - 6, exactY + 14));
  };

  window.updateUPASPlot();
})();
</script>

---

### 4. Summary of Numerical Validation (Examples 3 &amp; 4)

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-12">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/gamma_delta_curves.svg" title="Numerical Trajectories for Varying Discount Factor and Approximation Error" class="img-fluid rounded" %}
  </div>
</div>
<div class="caption">
  Left (Example 3): Closed-loop trajectories generated by the exact optimal policy $\kappa_\gamma^*$ for $\gamma \in \{0.85, 0.90, 0.96, 0.99\}$ converge directly to the analytical boundary $\nu(\gamma) = \frac{5(1-\gamma)}{2\gamma}$. Right (Example 4): At fixed $\gamma = 0.90$, reducing neural network approximation error $\delta \in \{0.30, 0.04, 0.01\}$ shrinks the ultimate steady-state error $\tilde{\nu}(\gamma, \delta)$ toward the analytical baseline $\nu(0.90)$.
</div>

For complete mathematical proofs of the continuity of the value functional in Banach/metric function spaces, pointwise optimality on compact sets, and the discrete-time ISS-Lyapunov derivations, please see our full RLC 2026 paper {% cite dePriester2026RLC %}.
