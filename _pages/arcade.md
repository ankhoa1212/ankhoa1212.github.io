---
layout: page
title: arcade
permalink: /arcade/
nav: false
---

## Games that I have played

<div class="projects">
{% if site.enable_arcade_categories %}
{% assign platforms = site.arcade | map: "category" | flatten | compact | uniq | sort %}
<p class="post-tags">
  {% for platform in platforms %}
    <a href="#{{ platform }}">{{ site.data.platform_labels[platform] | default: platform }}</a>
    {% unless forloop.last %}&nbsp;&middot;&nbsp;{% endunless %}
  {% endfor %}
</p>
{% for platform in platforms %}
<a id="{{ platform }}" href=".#{{ platform }}">
  <h2 class="category">{{ site.data.platform_labels[platform] | default: platform }}</h2>
</a>
{% assign categorized_games = site.arcade | where: "category", platform %}
{% assign sorted_games = categorized_games | sort: "importance" %}
<div class="row row-cols-1 row-cols-md-3">
{% for project in sorted_games %}
  {% include projects.liquid %}
{% endfor %}
</div>
{% endfor %}
{% else %}
{% assign sorted_games = site.arcade | sort: "importance" %}
<div class="row row-cols-1 row-cols-md-3">
{% for project in sorted_games %}
  {% include projects.liquid %}
{% endfor %}
</div>
{% endif %}
</div>
