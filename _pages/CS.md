# ---
# title: "Posts by CS"
# layout: categories
# permalink: /CS/
# author_profile: true
# ---

---
title: "Posts by CS"
layout: archive
permalink: /CS/
---

{% assign posts = site.categories.CS %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
