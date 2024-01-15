---
title: Projects
narrow: true
permalink: list/projects.html
show_profile: true
---

Projects

{% for project in site.projects %}
- [{{ project.title }}]({{ site.baseurl }}{{ project.url }})
{% endfor %}