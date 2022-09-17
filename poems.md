---
layout: page
title: Poems
permalink: /poems/
---



<div class="poems">
  {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
  {% for poem in site.poems reversed %}
  <div class="poem">
    <a href="{{ poem.url }}"><img src="{{ poem.header }}"></a>
    <div class="poem-title">
      {{ poem.title | escape }}
    </div>
  </div>
  {% endfor %}
</div>