---
layout: default
---

{% for post in site.posts %}
  {% include post_excerpt.html %}
{% endfor %}
