---
layout: default
title: Projects
permalink: /projects/
description: Open-source and side projects by Krzysztof Gonia.
---

A few things I've built.

<ul class="talk-list">
  {% assign projects = site.projects | sort: "date" | reverse %}
  {% for project in projects %}
    <li class="talk-list-item">
      <h3><a href="{{ project.url | relative_url }}">{{ project.title }}</a></h3>
      {% if project.description %}<p>{{ project.description }}</p>{% endif %}
    </li>
  {% endfor %}
</ul>

Check out more on my [GitHub profile](https://github.com/{{ site.author.github }}).
