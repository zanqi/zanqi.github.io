---
layout: page
title: "Home"
---

<article>
  Welcome! My name is Zanqi. I'm a student in the <a href="https://www.cs.washington.edu/">Computer Science Department</a> of the University of Washington. 
  I am researching in the <a href="https://robotic-manipulation.sciencehub.uw.edu/">Robotic Manipulation Lab</a>.<br><br>

  Here you will find some of my learning over time. I think building and deriving from scratch is the best way to learn. I hope this blog will help you in your learning journey too. <br><br>
</article>



{% if site.show_excerpts %}
  {% include home.html %}
{% else %}
  {% include archive.html title="Posts" %}
{% endif %}
