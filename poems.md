---
layout: page
title: Poems
permalink: /poems/
---

<div class="poems">
  {% for poem in site.poems reversed %}
  <div class="poem">
    <a href="{{ poem.url }}"><img src="{{ poem.header }}"></a>
    <div class="poem-title">
      {{ poem.title | escape }}
    </div>
  </div>
  {% endfor %}
</div>