---
layout: page
permalink: /publications/
title: publications
description: Publications organized by high-level research direction, with links to interactive articles and PDFs.
nav: true
nav_order: 2
---

{% include bib_search.liquid %}

<div class="publications">

  <div class="section-header" style="margin-top: 1.5rem;">
    <span class="section-kicker">Direction I</span>
    <h2 style="margin-bottom: 0.25rem;">Hysteresis-Based Reinforcement Learning &amp; Hybrid Supervision</h2>
    <p class="section-desc">
      Eliminating topological decision-boundary chattering in 2D and 3D autonomous navigation ($\text{SE}(3)$ &amp; $\text{SO}(3)$) against adversarial observation perturbations using hysteresis-based RL and predictive timer supervisors.
      <a href="{{ '/projects/l_multihyrl_3d_avoidance/' | relative_url }}" style="font-weight: 600; margin-left: 6px;">[Read Hysteresis-Based RL Article →]</a>
    </p>
  </div>
  {% bibliography --query @*[category=robust_hybrid]* --group_by none %}

  <div class="section-header">
    <span class="section-kicker">Direction II</span>
    <h2 style="margin-bottom: 0.25rem;">Foundations &amp; Intrinsic Stability of Reinforcement Learning</h2>
    <p class="section-desc">
      Proving that the unmodified discounted reinforcement learning objective intrinsically guarantees closed-loop practical asymptotic stability for continuous control tasks—modeling discounting and neural network approximation errors as bounded disturbances.
      <a href="{{ '/projects/intrinsic_stability_rl/' | relative_url }}" style="font-weight: 600; margin-left: 6px;">[Read Intrinsic Stability Article →]</a>
    </p>
  </div>
  {% bibliography --query @*[category=foundations]* --group_by none %}

  <div class="section-header">
    <span class="section-kicker">Direction III</span>
    <h2 style="margin-bottom: 0.25rem;">Physical Robotics &amp; Real-World Robustness Validation</h2>
    <p class="section-desc">
      Demonstrating that theoretical and simulation-tested hysteresis robustness scales reliably to physical hardware—including zero-shot sim-to-real <strong>L-MultiHyRL quadrotor flight experiments</strong> (listed under Direction I above) and hysteresis-based contact and force regulation on a 6-DoF robotic manipulator.
      <a href="{{ '/projects/l_multihyrl_3d_avoidance/#3-physical-flight-validation-on-a-modalai-sentinel-quadrotor' | relative_url }}" style="font-weight: 600; margin-left: 6px;">[See Quadrotor Flight Validation →]</a>
    </p>
  </div>
  {% bibliography --query @*[category=robotics]* --group_by none %}

  <div class="section-header">
    <span class="section-kicker">Earlier Research</span>
    <h2 style="margin-bottom: 0.25rem;">Applied Physics &amp; Acoustic Metamaterials</h2>
    <p class="section-desc">
      Optimization of thermo-viscous frequency stop-bands in micro-slit resonant acoustic metamaterials.
    </p>
  </div>
  {% bibliography --query @*[category=early_research]* --group_by none %}

</div>
