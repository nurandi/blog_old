---
layout: page
title: Blog Articles
permalink: /blog/
author_profile: true
---

<section class="archive-post-list">

   {% for post in site.posts %}
       {% assign currentDate = post.date | date: "%Y" %}
       {% if currentDate != myDate %}
           {% unless forloop.first %}</ul>{% endunless %}
           <h1>{{ currentDate }}</h1>
           <ul>
           {% assign myDate = currentDate %}
       {% endif %}
       <li>
          <span class="archive-post-title"><a href="{{ post.url }}">{{ post.title }}</a></span>
          <span class="archive-post-date"> {{ post.date | date: '%d %b' }}</span>
       </li>
       {% if forloop.last %}</ul>{% endif %}
   {% endfor %}

</section>