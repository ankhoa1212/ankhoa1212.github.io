---
layout: page
title: arcade
permalink: /arcade/
nav: false
---

## Games that I have played

<div class="projects">
{% assign sorted_games = site.arcade | sort: "importance" %}
<div class="row row-cols-1 row-cols-md-3">
{% for project in sorted_games %}
  {% include projects.liquid %}
{% endfor %}
</div>
</div>
