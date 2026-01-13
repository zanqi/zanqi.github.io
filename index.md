---
layout: page
title: "Home"
---

Welcome! I'm Zanqi, a Computer Science student at the University of Washington.

I am a passionate Systems Engineer who loves peeling back the layers of abstraction. Whether it's analyzing leader election latencies in distributed systems or diving into kernel internals, I believe the best way to understand performance is to build it from scratch.

{% if site.show_excerpts %}
  {% include home.html %}
{% else %}
  {% include archive.html title="Posts" %}
{% endif %}