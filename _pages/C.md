---
title: "Posts by C Programming"
layout: archive
permalink: /C/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.c %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
