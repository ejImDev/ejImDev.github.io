---
title: "Posts by JAVA"
layout: archive
permalink: /Java/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.java %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
