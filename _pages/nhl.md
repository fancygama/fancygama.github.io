---
classes: wide
collection: nhl
entries_layout: grid
layout: collection
permalink: /nhl/
sidebar:
  nav: nhl
title: NHL Home
---

<table class="sortable" id="teamTable">
  {% for row in site.data.NHL %}
    {% if forloop.first %}
    <tr>
      {% for pair in row %}
        <th class="sorttable_numeric">{{ pair[0] }}</th>
      {% endfor %}
    </tr>
    {% endif %}

    {% tablerow pair in row %}
      {{ pair[1] }}
    {% endtablerow %}
  {% endfor %}
</table>
<br>
Comments extracted from [r/hockey](https://reddit.com/r/hockey)
<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="/plots/NHL/NHL.html" height="640" width="100%"></iframe>