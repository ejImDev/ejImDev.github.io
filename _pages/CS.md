---
title: "Posts by CS"
layout: archive
permalink: /CS/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.cs %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
