---
layout: page
title: Archivo
description: "Archivo"
permalink: /archive/
category: base
---

<div id="search">
    <form role="search" method="get" action="{{ site.baseurl }}//search/">
        <input id="searchString" name="searchString"
               placeholder="Learn Ionic, Be a Better Developer, etc." type="text">
        <input id="searchButton" name="googleSearchName" type="button" value="Search">
    </form>
</div>

<section id="archive">
  <h3>Durante este año</h3>
  {%for post in site.posts %}
    {% unless post.next %}
      <ul class="post-list">
    {% else %}
      {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
      {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
      {% if year != nyear %}
        </ul>
        <h3>{{ post.date | date: '%Y' }}</h3>
        <ul class="post-list">
      {% endif %}
    {% endunless %}
      <li><a href="{{ site.url }}{{ post.url }}">{{ post.title }}<span class="entry-date">
      <time datetime="{{ post.date | date_to_xmlschema }}">
      {% assign m = post.date | date: "%-m" %}
      {{ post.date | date: "%-d" }}
      {% case m %}
        {% when '1' %}Ene
        {% when '2' %}Feb
        {% when '3' %}Mar
        {% when '4' %}Abr
        {% when '5' %}May
        {% when '6' %}Jun
        {% when '7' %}Jul
        {% when '8' %}Ago
        {% when '9' %}Sep
        {% when '10' %}Oct
        {% when '11' %}Nov
        {% when '12' %}Dec
      {% endcase %}
      {{ post.date | date: "%Y" }}
      </time></span></a></li>
  {% endfor %}
  </ul>
</section>
