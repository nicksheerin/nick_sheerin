---
layout: archive
title: "Projects"
permalink: /projects/
author_profile: true
# header:
#   og_image: "research/ecdf.png"
---

Coming soon!

<nbsp>

{% include base_path %}

{% assign ordered_pages = site.projects | sort:"order_number" %}

{% for post in ordered_pages %}
  {% include archive-single.html type="grid" %}
{% endfor %}
