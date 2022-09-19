---
layout: page
title: Poetry
permalink: /poetry/
---

<div class="poetry-list">
  {% for poetry in site.poetry reversed %}
  <div class="poetry">
    <a href="{{ poetry.url }}"><img src="{{ poetry.header }}"></a>
    <div class="poetry-title">
      {{ poetry.title | escape }}
    </div>
  </div>
  {% endfor %}
</div>