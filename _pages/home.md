---
title: Home
permalink: /
classes: wide
---

I am French engineer with a master in Telecommunications from INSA Lyon.<br />
After working for 5 years in IT consulting at [Wavestone](https://wavestone.com), where I supported large companies to conduct their IT transformation in various topics (SD-WAN, IPv6, Industrial security, IoT), I decided to take some time to define and realize my own data science curriculum thanks to online resources.<br />
This is a personal website I started during my training in data sciences. <br />



If you want to know what I am currently working on, head to my [now page](/now/).

My [Curriculum Vitae](/assets/cv/CV_MAINGRET_Baptiste_2019.pdf) :fr: (:gb: on my LinkedIn).

> Links to my LinkedIn and GitHub are in the left side-bar.

<h3 class="archive__subtitle">{{ site.data.ui-text[site.locale].recent_posts | default: "Recent Posts" }}</h3>


  {% for post in site.posts limit:5 %}
    {% include archive-single.html %}
  {% endfor %}
