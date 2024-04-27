---
layout: archive
title: "Research"
permalink: /research/
author_profile: true
classes: wide
---

PROVA


### Anchor Tag (aka. Link)

I am a Ph.D. candidate in Finance at the Norwegian School of Economics. My research interests are asset pricing, market microstructure, and financial intermediation. 
This is an example of a [link](http://github.com "Github"). <div href="#" class="btn">Click me!</div>

{% if site.author.googlescholar %}
  <div class="wordwrap">You can also find my articles on <a href="{{site.author.googlescholar}}">my Google Scholar profile</a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.research reversed %}
  {% include archive-single.html %}
{% endfor %}
