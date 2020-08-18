---
classes: wide
collection: nfl
entries_layout: grid
layout: collection
permalink: /nfl/
sidebar:
  nav: nfl
title: NFL Home
---

<table class="sortable" id="teamTable">
  {% for row in site.data.NFL %}
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
Comments extracted from [r/nfl](https://reddit.com/r/nfl)
<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="/plots/NFL/NFL.html" height="640" width="100%"></iframe>