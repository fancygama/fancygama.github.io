---
classes: wide
collection: nba
entries_layout: grid
layout: collection
permalink: /nba/
sidebar:
  nav: nba
title: NBA Home
---

<table class="sortable" id="teamTable">
  {% for row in site.data.NBA %}
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
Comments extracted from [r/nba](https://reddit.com/r/nba)
<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="/plots/NBA/NBA.html" height="640" width="100%"></iframe>