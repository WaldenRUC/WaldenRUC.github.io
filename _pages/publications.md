---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

{% include base_path %}

You can also find my articles on [link]({{site.author.googlescholar}}).

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
