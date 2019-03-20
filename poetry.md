---
layout: page
permalink: /idk/
title: idk
description: not poems
---

<ul class="post-list">
{% for poem in site.poetry reversed %}
    <li>
        <a class="poem-title hover-fade is-hover-invert is-color-{% cycle 1, 2, 3, 4, 5, 6 %}" href="{{ poem.url | prepend: site.baseurl }}">{{ poem.title }}</a>
        <p class="post-meta">{{ poem.date | date: '%B %-d, %Y — %H:%M' }}</p>
    </li>
{% endfor %}
</ul>