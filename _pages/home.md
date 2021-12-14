---
title: Home
permalink: /
date: 2021-12-14
---

French engineer with a master in Telecommunications from INSA Lyon.<br />

* 5 years in IT consulting at [Wavestone](https://wavestone.com) where I supported large companies to conduct their IT transformation in various topics (SD-WAN, IPv6, Industrial security, IoT).<br />

* 1 year on my own data science curriculum thanks to online resources, and am now working in this field.<br />

* 1 year [Institut de Cancérologie de Lorraine](https://www.icl-lorraine.fr/) mostly doing data integration, managing the Enterprise Service Bus, and when the time and resources allow it machine learning projects.<br />

> Now looking for a remote job in machine learning/software engineering.

> If you want to know what I am currently working on, head to my [now page](/now/).


<h3 class="archive__subtitle">{{ site.data.ui-text[site.locale].recent_posts | default: "Recent Posts" }}</h3>


  {% for post in site.posts limit:5 %}
    {% include archive-single.html %}
  {% endfor %}
