---
layout: page
title: ray@github
tagline: 
---
{% include JB/setup %}

{% for post in site.posts %}

# [{{ post.title }}]({{ BASE_PATH }}{{ post.url }})
---
{{ post.date | date_to_string }}

{{ post.content }}

{% endfor %}
