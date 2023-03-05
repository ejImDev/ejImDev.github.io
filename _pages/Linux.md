---
title: "Posts by Linux"
layout: archive
permalink: /Linux/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.Linux %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
