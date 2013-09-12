---
layout: page
title: I've Just
---
{% include JB/setup %}

<div>
  {% for post in site.posts %}
    <div class="span8 searchable" style="padding-bottom: 2em;" data-index="{{ post.tags | textilize | downcase }}">
		<h3>{{ post.date | date_to_string }} &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h3>
		<summary>{{ post.content | split:"<!--more-->" | first }}</summary>
 		<div style="font-size: 200%;"><a href="{{ BASE_PATH }}{{ post.url }}"><h5>read more</h5></a></div>
     </div>
  {% endfor %}
</div>
