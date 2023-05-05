---
title: "Posts by Diary"
layout: archive
permalink: /Etc/Diary/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.diary %} {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
