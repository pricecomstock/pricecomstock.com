---
layout: page
title: comedy
permalink: /comedy/
---

{% for project in site.portfolio %}

{% if project.tags contains "comedy" %}

{% if project.redirect %}
<div class="project">
    <div class="thumbnail blankbox-color-{% cycle 1, 2, 3, 4, 5, 6 %}">
        <a href="{{ project.redirect }}" target="_blank">
        {% if project.img %}
        <img class="thumbnail" src="{{ project.img }}"/>
        {% else %}
        <div class="thumbnail"></div>
        {% endif %}    
        <span>
            <h1>{{ project.title }}</h1>
            <br/>
            <p>{{ project.description }}</p>
        </span>
        </a>
    </div>
</div>
{% else %}

<div class="project ">
    <div class="thumbnail blankbox-color-{% cycle 1, 2, 3, 4, 5, 6 %}">
        <a href="{{ site.baseurl }}{{ project.url }}">
        {% if project.img %}
        <img class="thumbnail" src="{{ project.img }}"/>
        {% else %}
        <div class="thumbnail"></div>
        {% endif %}    
        <span>
            <h1>{{ project.title }}</h1>
            <br/>
            <p>{{ project.description }}</p>
        </span>
        </a>
    </div>
</div>

{% endif %}
{% endif %}

{% endfor %}
