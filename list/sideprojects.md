---
title: SideProjects
narrow: true
permalink: list/sideprojects.html
show_profile: true
---

Side Projects

{% for project in site.sideprojects %}
- [{{ project.title }}]({{ site.baseurl }}{{ project.url }})
{% endfor %}

<!--Projects

{% for project in site.projects %}
- [{{ project.title }}]({{ site.baseurl }}{{ project.url }})
{% endfor %}-->