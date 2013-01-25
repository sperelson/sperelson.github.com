---
layout: page
title: I've Just Used
---
{% include JB/setup %}

<div>
  {% for post in site.posts %}
    <div class="span8" style="padding-bottom: 2em;">
		<h3>{{ post.date | date_to_string }} &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h3>
		<summary>{{ post.content | more: "excerpt" }}</summary>
 		<div style="font-size: 200%;"><a href="{{ BASE_PATH }}{{ post.url }}"><h5>read more</h5></a></div>
     </div>
  {% endfor %}
</div>
