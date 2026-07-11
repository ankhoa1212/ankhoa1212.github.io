---
layout: page
title: games
permalink: /games/
description: Games I've built, mostly for coursework and a senior capstone project.
nav: false
---

<!-- pages/games.md -->
<div class="projects">
  {% assign sorted_games = site.games | sort: "importance" %}
  <div class="row row-cols-1 row-cols-md-3">
  {% for project in sorted_games %}
    {% include projects.liquid %}
  {% endfor %}
  </div>
</div>
