---
layout: archive
title: "Projects"
permalink: /projects/
author_profile: true
header:
  og_image: "projects/dissertation.png"
---

Here is a list of projects I have completed / currently working on.

<nbsp>

{% include base_path %}

{% assign ordered_pages = site.projects | sort:"order_number" %}

{% for post in ordered_pages %}
  {% include archive-single.html type="grid" %}
{% endfor %}
