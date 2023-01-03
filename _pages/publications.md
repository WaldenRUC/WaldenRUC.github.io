---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

{% for post in site.publications reversed %}
  {% if forloop.first %}
    You can also find my articles on [Google Scholar](https://scholar.google.cz/citations?hl=zh-CN&user=KwTK6AoAAAAJ)
  {% endif %}
  {% include archive-single.html %}
{% endfor %}
