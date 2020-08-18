---
classes: wide
collection: mlb
entries_layout: grid
layout: collection
permalink: /mlb/
sidebar:
  nav: mlb
title: MLB Home
---

<table class="sortable" id="teamTable">
  {% for row in site.data.MLB %}
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
Comments extracted from [r/baseball](https://reddit.com/r/baseball)
<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="/plots/MLB/MLB.html" height="640" width="100%"></iframe>