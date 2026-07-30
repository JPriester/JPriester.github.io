---
layout: page
permalink: /publications/
title: publications
description: publications organized by research area.
nav: true
nav_order: 2
---

<!-- _pages/publications.md -->

<!-- Bibsearch Feature -->

{% include bib_search.liquid %}

<div class="publications">

<h2 class="category">Foundations & Theory of Reinforcement Learning</h2>
{% bibliography --query @*[category=foundations]* --group_by none %}

<h2 class="category">Robust & Hybrid Reinforcement Learning</h2>
{% bibliography --query @*[category=robust_hybrid]* --group_by none %}

<h2 class="category">Robotics & Applied Control</h2>
{% bibliography --query @*[category=robotics]* --group_by none %}

<h2 class="category">Early Research / Applied Physics</h2>
{% bibliography --query @*[category=early_research]* --group_by none %}

</div>

