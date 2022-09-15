---
layout: page
title: Profession
permalink: /profession/
---

{% for company in site.data.companies %}
<a href="{{ company.url }}"><img src="{{ company.image }}"></a>
<table>
{% assign professions_of_company = site.data.professions | where: "company", company.id %}
{% for year in (2015..2022) reversed %}
{% assign professions_of_year = professions_of_company | where: "year", year %}
{% if professions_of_year.size > 0 %}
{% for profession in professions_of_year %}
<tr>
<td>
{{year}}
</td> 
<td>
  {% if profession.title %}
  <b>{{ profession.title }}</b>
  {% else %}
  <i>Undisclosed project</i>
  {% endif %}
  <br/>
  {{ profession.summary }}
</td>
</tr>
{% endfor %}
{% endif %}
{% endfor %}
</table>
{% endfor %}