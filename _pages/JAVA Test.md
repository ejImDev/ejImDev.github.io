---
title: "Posts by JAVA Test"
layout: archive
permalink: /Java/Test/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.javaTest %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
