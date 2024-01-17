---
title: "Posts by Book_P"
layout: archive
permalink: /sp/bookP/
author_profile: true
sidebar:                 
        nav: "sidebar-category"
types: posts
---

{% assign posts = site.categories.book_p %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
