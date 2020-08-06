---
classes: wide
collection: nba
entries_layout: grid
layout: collection
permalink: /nba/
sidebar:
  nav: nba
title: NBA
---

<style>
	th{
	    border:2px solid #000000;
	}

	td{
	    border:1px solid #000000;
	}
</style>

<script src="https://www.kryogenix.org/code/browser/sorttable/sorttable.js"></script>

<table class="sortable">
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