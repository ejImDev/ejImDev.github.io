---
title: "Posts by Error"
layout: archive
permalink: /Etc/Error/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.error %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
