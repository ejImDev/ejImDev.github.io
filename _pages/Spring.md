---
title: "Posts by Spring"
layout: archive
permalink: /Java/Spring/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.spring %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
