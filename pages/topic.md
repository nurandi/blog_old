---
layout: page
permalink: /topic/
title: "Articles by Topic"
author_profile: true
---

<h1>Popular</h1>

{% capture tags %}{% for tag in site.tags %}{{tag[0]}}{{','}}{% endfor %}{% endcapture %}
{% assign sortedtags = tags | downcase | split:"," | sort %}

<ul class="archive-tag-list">
  {% for tag in sortedtags %}
    {% assign count = 0 %}
    {% unless tag == previous %}
  
      {% for post in site.posts %}
        {% for t in post.tags %}
           {% capture tdown %}{{ t | downcase }}{% endcapture %}
           {% if tdown == tag %}
              {% assign count = count | plus: 1 %}
           {% endif %}
        {% endfor %}
      {% endfor %}  

      <li>
        <span class="archive-tag-term"><a href="#{{ tag | slugify }}" class="archive-tag-linkbtn">{{ tag }}</a></span>
        <span class="archive-tag-count">({{ count }})</span>
      </li>
      
    {% endunless %}
    {% assign previous = tag %}
  {% endfor %}
</ul>

<hr style="margin-bottom:20px">

<section class="archive-post-list">

	{% for tag in sortedtags %}
    {% unless tag == previous %}
      <h1 id="{{ tag | slugify }}">{{ tag }}</h1>
          <ul>
          {% for post in site.posts %}
            {% for t in post.tags %}
               {% capture tdown %}{{ t | downcase }}{% endcapture %}
               {% if tdown == tag %}
                  <li>
                    <span class="archive-post-title"><a href="{{ post.url }}">{{ post.title }}</a></span>
                    <span class="archive-post-date"> {{ post.date | date: '%d %b %Y' }}</span>
                  </li>
               {% endif %}
            {% endfor %}
          {% endfor %}
          </ul>
    {% endunless %}
    {% assign previous = tag %}
	{% endfor %}
  
</section>
