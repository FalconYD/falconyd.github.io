---
layout: defaults/page
permalink: index.html
narrow: true
title: Falcon Blog.
---

## Blog 시작.
 개인적으로 공부하는 것을 옮겨 적어보기 위해 가벼운 마음으로 시작하는 블로그.
### Recent Posts

{% for post in site.posts limit:3 %}
{% include components/post-card.html %}
{% endfor %}


