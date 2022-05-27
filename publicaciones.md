---
layout: home
---

<ul>
{% for post in site.posts %}
<li>
    {% if post.link %}
    <h3 class="link-post">
    <a href="{{ site.baseurl }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
    <a href="{{ post.link }}" target="_blank" title="{{ post.title }}"><i class="fa fa-link"></i></a>
    </h3>
    {% else %}
    <h3><a href="{{ site.baseurl }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}<p class="date">{{
        post.date | date: "%B %e, %Y" }}</p></a></h3>
    <p>{{ post.excerpt | strip_html | truncate: 160 }}</p>
    {% endif %}
    <br>
</li>
{% endfor %}
</ul>
