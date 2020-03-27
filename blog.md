---
layout: home_blog
permalink: /blog/
---
{% assign sorted_cats = site.categories | sort %}
{% for category in sorted_cats %}
{% assign sorted_posts = category[1] | reversed %}
<h2 id="{{category[0] | uri_escape | downcase }}">{{category[0] | capitalize}}</H2>
<ul>
  {% for post in sorted_posts %}
 	<li><a href="{{ site.url }}{{ site.baseurl }}{{  post.url }}">{{  post.title }}-{{ post.date | date: "%Y-%m-%d"}}</a></li>
  {% endfor %}
</ul>
{% endfor %}