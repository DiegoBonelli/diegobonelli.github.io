---
layout: archive
title: "Research"
permalink: /research/
author_profile: true
classes: wide
---

## Section 1
This is some text content for section 1. You can replace it with your own text.

![Image for Section 1](/images/500x300.png)

{% if site.author.googlescholar %}
  <div class="wordwrap">You can also find my articles on <a href="{{site.author.googlescholar}}">my Google Scholar profile</a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.research reversed %}
  {% include archive-single.html %}
{% endfor %}
