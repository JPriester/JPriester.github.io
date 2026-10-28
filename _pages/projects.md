---
layout: page
title: research
permalink: /projects/
description: In-depth articles exploring our core research directions, interactive simulations, and hardware validations.
nav: true
nav_order: 1
display_categories: [work]
horizontal: false
---

<div class="projects">
  <div class="callout-box" style="margin-top: 0; margin-bottom: 2rem;">
    <h4>🧭 Research Articles &amp; Interactive Deep Dives</h4>
    <p>
      Each article below provides an intuitive, visual explanation of one of our key research contributions—complete with interactive simulations, architectural diagrams, and links to the underlying mathematical proofs.
    </p>
  </div>

  {% assign sorted_projects = site.projects | where: "category", "work" | sort: "importance" %}
  <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
</div>
