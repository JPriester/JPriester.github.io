---
layout: page
title: "MultiHyRL: 2D Multi-Obstacle Hybrid RL"
description: "Robust Hybrid RL for Obstacle Avoidance against Adversarial Attacks on the Observation Space"
img: assets/img/publication_preview/multihyrlpreview.jpg
importance: 4
category: work
related_publications: true
---

<div class="callout-box callout-emerald">
  <h4>📄 Published at RLC 2024</h4>
  <p>
    This article summarizes our paper, <strong>"MultiHyRL: Robust Hybrid RL for Obstacle Avoidance against Adversarial Attacks on the Observation Space"</strong> by <em>Jan de Priester, Zachary Bell, Prashant Ganesh, and Ricardo G. Sanfelice</em>, published in the <strong>Reinforcement Learning Journal (RLC 2024)</strong>.
    <a href="{{ '/assets/pdf/MultihyrlPaper.pdf' | relative_url }}" target="_blank" style="font-weight: 700; margin-left: 6px;">[Read Full Paper PDF →]</a>
  </p>
</div>

Reinforcement learning (RL) holds tremendous promise for autonomous navigation, yet standard continuous RL policies lack formal robustness guarantees against adversarial perturbations in the observation space—especially for safety-critical tasks like obstacle avoidance.

In this work, we introduce **MultiHyRL**, a hybrid reinforcement learning algorithm featuring hysteresis-based switching over relaxed Voronoi partitions. MultiHyRL overcomes the topological limitations of continuous policies and guarantees robustness against adversarial observation noise in environments with multiple static and dynamic obstacles.

---

### The Topological Obstruction Problem in 2D Navigation

When an autonomous vehicle navigates past an obstacle toward a target, the obstacle acts as a *topological obstruction*, forcing the vehicle to choose between passing clockwise or counterclockwise. Because standard RL policies (such as PPO) parameterize continuous mappings from observations to actions, this binary directional choice creates a vulnerable decision boundary directly in front of the obstacle.

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-12">
    {% include figure.liquid loading="eager" path="assets/img/multihyrl/multihyrl_overview.svg" title="MultiHyRL vs Standard PPO Overview" class="img-fluid rounded" %}
  </div>
</div>
<div class="caption">
  Left: Near the decision boundary (red dashed line), small adversarial observation noise ($\varepsilon_p$) tricks a standard continuous PPO agent into rapidly alternating decisions (chattering) and colliding with the obstacle. Right: MultiHyRL partitions the state space into overlapping regions ($\mathcal{M}_0, \mathcal{M}_1$) using an SVM and enforces hysteresis via discrete logic states $(q, \lambda)$.
</div>

---

### MultiHyRL: Relaxed Voronoi Partitioning &amp; Hysteresis

Rather than relying on a single vulnerable continuous policy, MultiHyRL:
1. **Identifies Topological Critical Points** in the state space where perturbed trajectories locally diverge around an obstacle.
2. **Constructs Overlapping Partitions ($\mathcal{M}_0$ and $\mathcal{M}_1$)** using a Support Vector Machine (SVM) and relaxed Voronoi partitioning across multiple obstacles, creating physical hysteresis buffers between switching boundaries.
3. **Trains Specialized Policies ($\pi_0$ and $\pi_1$)** that strictly pass obstacles clockwise or counterclockwise, orchestrated by a closed-loop hybrid system with two discrete logic variables: $q \in \{0, 1\}$ (active directional policy) and $\lambda$ (currently focused obstacle).

<div class="row justify-content-sm-center mt-4 mb-2">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/publication_preview/hyrlavoidance.jpg" title="Hysteresis Avoidance Regions" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/publication_preview/multihyrlpreview.jpg" title="MultiHyRL Multi-Obstacle Partitioning" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Left: Hysteresis-based obstacle avoidance around a single obstacle ({% cite dePriester2022ACC %}). Right: Multi-obstacle relaxed Voronoi partitioning and closed-loop trajectories in MultiHyRL ({% cite dePriester2024MultiHyRL %}).
</div>

MultiHyRL extends naturally without modification to environments with moving obstacles, varying obstacle radii, and adversarial multi-agent scenarios such as **Capture the Flag**. Furthermore, in our latest work on **[L-MultiHyRL](/projects/l_multihyrl_3d_avoidance/)**, we eliminate the need for global obstacle coordinates entirely by predicting partition membership directly from onboard visual CNN latent features in 3D $\text{SE}(3)$ flight!

For the complete mathematical formulation and stability proofs, please refer to our full paper {% cite dePriester2024MultiHyRL %}.
