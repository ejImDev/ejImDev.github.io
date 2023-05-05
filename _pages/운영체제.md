---
title: "Posts by Operating System"
layout: archive
permalink: /OS/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.os %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
