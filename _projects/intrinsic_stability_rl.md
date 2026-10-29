---
layout: page
title: "Intrinsic Practical Stability in Discrete-Time RL"
description: "Why Unmodified Discounted Reinforcement Learning Intrinsically Guarantees Closed-Loop Practical Asymptotic Stability"
img: assets/img/publication_preview/PracticalStability_DiscountingApproximation.png
importance: 2
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Reinforcement Learning Conference (RLC 2026)</h4>
  <p>
    This article provides an accessible overview of our paper, <strong>"Intrinsic Closed-Loop Practical Asymptotic Stability in Discrete-Time Reinforcement Learning"</strong> by <em>Jan de Priester and Ricardo G. Sanfelice</em>, published in the <strong>Reinforcement Learning Journal (RLC 2026)</strong>. For the complete mathematical definitions and formal proofs, see the full paper:
    <a href="{{ '/assets/pdf/intrinsic_practical_stability_RL.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 6px;">[Read Paper PDF →]</a>
  </p>
</div>

Deep reinforcement learning (RL) has achieved impressive empirical results in complex continuous control tasks—such as **dynamic legged locomotion**, **high-speed autonomous drone racing**, and **dexterous manipulation**. Fundamentally, these tasks are **set-stabilization problems**: rather than driving a system to a single static point, the agent must steer the state $x_k$ from any initial condition $x_0$ toward—and indefinitely keep it within—a desired **target set** $\mathcal{A}$ (such as a repeating walking gait limit cycle or a spatial racing line).

To safely deploy learned policies in the real world, a large body of literature modifies the RL algorithm to enforce formal stability guarantees—for example, by jointly training neural Lyapunov functions, embedding Lyapunov decrease constraints into policy updates, or applying external safety filters. While these techniques are valuable for **training-time safety during exploration**, they <span class="key-highlight">alter the core RL objective, which can restrict natural exploration and complicate training</span>.

In this work, we offer a complementary perspective:
> **Modifying the core RL objective is often unnecessary to guarantee the stability of the deployed policy.** We prove that, under mild conditions, near-optimal policies trained on the **unmodified discounted RL objective** intrinsically guarantee **Uniform Practical Asymptotic Stability (UPAS)**.

---

### 1. Why Steady-State Error in RL is a Feature of Discounting, Not a Bug

In classical control theory, **asymptotic stability** requires a trajectory starting from $x_0$ to converge all the way into the exact target set $\mathcal{A}$ over an infinite horizon (left panel below). In contrast, **practical asymptotic stability** guarantees that the trajectory converges into and indefinitely remains within a **small, bounded neighborhood** of radius $\rho > 0$ around the target set $\mathcal{A}$ (right panel below).

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/Asymp_Stab.png" title="Asymptotic Stability" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/pract_asympt_stab.png" title="Practical Asymptotic Stability" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Asymptotic Stability):</strong> Closed-loop trajectories starting at $x_0$ converge strictly to the target set $\mathcal{A}$. <strong>Right (Practical Asymptotic Stability):</strong> Trajectories converge into and remain inside a bounded $\rho$-neighborhood (blue region) surrounding $\mathcal{A}$.
</div>

Why do standard RL agents naturally exhibit **practical asymptotic stability**—settling into a small steady-state offset near the target rather than converging to exact zero error? Two fundamental factors govern this behavior:

1. **Discounting Creates a Myopic Agent ($\gamma \in (0, 1)$):**
   Rather than optimizing an infinite-horizon undiscounted sum ($\gamma = 1$), RL maximizes an expected *discounted* return weighted by $\gamma^k$ at each future time step $k$. As shown below, discounting exponentially diminishes the weight of distant rewards $\gamma^k R$, effectively **truncating the optimization horizon** and causing the agent to prioritize near-term rewards over distant ones. Once the state gets "close enough" to the target $\mathcal{A}$, the immediate control effort required to push the error smaller outweighs the discounted future benefit. <span class="key-highlight">Thus, native steady-state error in RL is not an algorithmic failure—it is the direct consequence of optimizing a discounted objective.</span>

<div class="row justify-content-sm-center mt-3 mb-3">
  <div class="col-sm-8">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/discounting2.png" title="Effect of Discounting on Future Rewards" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Discounting Induces a Myopic Policy:</strong> Compared to an undiscounted return ($\gamma = 1$, blue), a discount factor $\gamma \in (0, 1)$ (red) exponentially reduces the importance of future step rewards $\gamma^k R$ as time step $k$ increases.
</div>

2. **Neural Network Approximation Error ($\delta > 0$):**
   In practice, deep RL searches over parameterized **feedforward neural networks** rather than all possible continuous functions. Because finite-capacity networks produce *near-optimal* policies with some small suboptimality $\delta > 0$, this approximation error slightly enlarges the steady-state neighborhood around the target.

<div class="interactive-lab-card">
  <div class="direction-canvas-wrap">
    <div class="canvas-header">
      <span class="canvas-title">Animation: Practical Stability of a Quadruped Locomotion Limit Cycle</span>
      <span class="canvas-live-dot">● Two-Phase Convergence</span>
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
      <strong>Visualizing Our Main Result on a Periodic Locomotion Gait:</strong> Each joint of the galloping quadruped tracks a desired limit cycle (dashed loops). <strong>Phase 1 (Training Progression at fixed $\gamma = 0.90$):</strong> As training reduces neural network approximation error, the guaranteed stability neighborhood around the limit cycle shrinks. <strong>Phase 2 (Becoming Farsighted as $\gamma \to 1$):</strong> Increasing the discount factor toward $1$ contracts the remaining neighborhood to the exact target gait.
    </div>
  </div>
</div>

---

### 2. How the Theoretical Guarantee Works

To formally prove that unmodified deep RL guarantees closed-loop practical stability, we adapt the discrete-time **Input-to-State Stability (ISS)** framework and extend tools from discounted Model Predictive Control (MPC) to neural network policies:

- **Modeling Suboptimality as Bounded Disturbances:**
  We analyze the optimal value function as a Lyapunov-like energy candidate for the closed-loop system. Using the Bellman equation, we show that both the **horizon truncation from discounting** ($\gamma \in (0, 1)$) and the **suboptimality of the learned neural network** ($\delta > 0$) act as **bounded disturbances** to the ideal closed-loop dynamics.
- **Bridging Expected Return to Worst-Case Pointwise Guarantees:**
  A key theoretical challenge is that RL algorithms maximize an *expected* return averaged across an initial state distribution, whereas closed-loop stability requires a guarantee that holds *pointwise at every individual state*. By formulating the policy search over a compact space of continuous feedback laws (such as feedforward neural networks with continuous activations and bounded weights) and a strictly positive initial state distribution, we establish two crucial links:
  1. **Exact RL (Lemma 3.8 & Theorem 3.9):** A policy that maximizes the expected return over the initial state distribution is automatically **pointwise optimal at every individual state**, steering trajectories into a discount-dependent neighborhood of radius $\nu(\gamma)$ around $\mathcal{A}$ (left figure below).
  2. **Approximate RL (Lemma 4.4 & Theorem 4.5):** Bounding the expected suboptimality of a neural network policy by $\delta > 0$ automatically guarantees a **uniform worst-case bound on its value error across the entire state space**, steering trajectories into an expanded neighborhood of radius $\tilde{\nu}(\gamma, \delta)$ around $\mathcal{A}$ (right figure below).

<div class="row justify-content-sm-center align-items-center mt-4 mb-2">
  <div class="col-sm-6">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/PracticalStability_Discounting.png" title="Theorem 3.9: Practical Stability under Exact Discounted RL" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/PracticalStability_DiscountingApproximation.png" title="Theorem 4.5: Practical Stability under Approximate Neural Network RL" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Exact Discounted RL — Theorem 3.9):</strong> Under an exact optimal discounted policy, trajectories converge into a bounded neighborhood $\nu(\gamma)$ (blue) around $\mathcal{A}$ that vanishes as $\gamma \to 1$. <strong>Right (Approximate Neural Network RL — Theorem 4.5):</strong> Function approximation error $\delta > 0$ enlarges the residual neighborhood to $\tilde{\nu}(\gamma, \delta)$ (green), which contracts back to $\nu(\gamma)$ as $\delta \to 0$ and then to $\mathcal{A}$ as $\gamma \to 1$.
</div>

Crucially, <span class="key-highlight">the size of the steady-state neighborhood $\tilde{\nu}(\gamma, \delta)$ shrinks to zero as the approximation error $\delta$ approaches $0$ and, subsequently, the discount factor $\gamma$ approaches $1$</span>.

<div class="callout-box">
  <h4>🔍 When Does This Guarantee Hold? (Core Assumptions in Plain Language)</h4>
  <ul style="margin-bottom: 0; padding-left: 1.2rem;">
    <li><strong>Continuous Dynamics &amp; Bounded Workspace (Assumption 3.1):</strong> The discrete-time system dynamics and reward function are continuous, and the state and action spaces are compact—a natural fit for physical robots with bounded workspaces and actuator saturation limits.</li>
    <li><strong>Target-Aligned Reward &amp; Solvable Task (Assumption 3.6):</strong> Relative to the maximum reward in the target set, the reward strictly penalizes distance from the target (preventing reward "blind spots"), and at least one continuous stabilizing baseline policy exists.</li>
    <li><strong>Continuous Optimal Policy (Assumption 3.7):</strong> The task can be optimally solved by a continuous feedback law. <em>(Note: This excludes tasks where the optimal policy is inherently discontinuous—such as bang-bang control or obstacle avoidance around topological obstructions, which we address using hybrid switching in our MultiHyRL and Timer-Supervisor work.)</em></li>
  </ul>
</div>

---

### 3. Numerical Validation: How Discounting ($\gamma$) and Network Error ($\delta$) Shape Steady-State Behavior

In **Examples 3 and 4** of the paper, we illustrate these theoretical bounds on a discrete-time system $x^+ = x + 0.1u$ with state and action spaces $[-1, 1]$, stabilizing the target origin $\mathcal{A} = \lbrace 0 \rbrace$. Here, $\omega(x_k)$ (equal to $\lvert x_k \rvert$) measures the distance to the target at discrete time step $k$, and the reward function is:

$$R(x, u) = -\big(x^2 + 0.5\lvert u \rvert\big)$$

Because the linear control effort penalty ($0.5\lvert u \rvert$) dominates the quadratic state error penalty ($x^2$) near the origin, the optimal policy stops applying control ($u^\ast = 0$) once the state gets close enough to zero—producing a clear, analytically computable steady-state boundary.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-6">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/ideal_bound.png" title="Figure 1: Closed-loop trajectories for varying discount factors" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6">
    {% include figure.liquid loading="eager" path="assets/img/intrinsic_rl/approx_bound.png" title="Figure 2: Closed-loop trajectories for varying approximation errors" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  <strong>Left (Figure 1 / Example 3 — Effect of Discount Factor $\gamma$):</strong> Closed-loop trajectories $\omega(x_k)$ under the exact optimal policy for $\gamma \in \lbrace 0.85, 0.90, 0.96, 0.99 \rbrace$ converge directly to their analytical steady-state boundaries $\nu(\gamma)$ (dashed lines), which shrink toward zero as $\gamma$ increases. <strong>Right (Figure 2 / Example 4 — Effect of Approximation Error $\delta$):</strong> At a fixed discount factor $\gamma = 0.90$, reducing the neural network approximation error $\delta \in \lbrace 0.30, 0.04, 0.01 \rbrace$ shrinks the steady-state offset $\tilde{\nu}(\gamma, \delta)$ toward the ideal discounted boundary $\nu(0.90)$ (black dashed line).
</div>

- **Effect of the Discount Factor $\gamma$ (Left Plot — Example 3):**
  Under the exact optimal policy, the steady-state error boundary is given analytically by $\nu(\gamma) = \frac{5(1-\gamma)}{2\gamma}$. As $\gamma$ increases from $0.85$ to $0.99$, the agent becomes more farsighted and **the steady-state offset shrinks directly toward zero**.
- **Effect of Neural Network Approximation Error $\delta$ (Right Plot — Example 4):**
  Fixing $\gamma = 0.90$, we evaluate parameterized ReLU network policies across decreasing value approximation errors $\delta \in \lbrace 0.30, 0.04, 0.01 \rbrace$—simulating how a neural network policy improves over the course of training. Larger approximation errors act as a larger disturbance that widens the steady-state error, while <span class="key-highlight-blue">reducing $\delta$ during training shrinks the trajectory bound $\tilde{\nu}(\gamma, \delta)$ directly onto the ideal discounted baseline $\nu(\gamma)$</span>.

<div class="callout-box callout-emerald">
  <h4>🛠️ Practical Engineering Takeaway: You Don't Need $\gamma \to 1$ in Practice (Corollary 3.9.1)</h4>
  <p>
    While the steady-state bound vanishes in the limit as $\gamma \to 1$, pushing $\gamma$ arbitrarily close to $1$ in practical RL often causes high variance and training instability. Fortunately, our theorem guarantees that <strong>taking the strict limit $\gamma \to 1$ is unnecessary</strong>: for any desired real-world accuracy tolerance $\rho > 0$ (for example, keeping a robotic manipulator within $1\text{ mm}$ of a target), <strong>there always exists a strictly smaller discount factor $\gamma^\ast \in (0, 1)$ such that any $\gamma \in [\gamma^\ast, 1)$ guarantees convergence within that specification</strong>.
  </p>
</div>

For the full mathematical formulation—including the continuity of the value functional in function space, the universal approximation bounds for deep narrow networks, and the complete stability proofs—please refer to our RLC 2026 paper {% cite dePriester2026RLC %}.
