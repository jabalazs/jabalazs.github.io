---
layout: default
title: Blog
permalink: /blog/
---

<div class="home">
  {% for post in site.posts %}
    <article class="post">
      <h2 class="post-title"><a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h2>

      <time datetime="{{ post.date | date_to_xmlschema }}" class="post-date">{{ post.date | date: '%B %d, %Y' }}</time>

      {{ post.content }}

      <hr>
    </article>
  {% endfor %}

    <!-- Pagination links -->
    <div class="pagination">
        {% if paginator.previous_page %}
            <a href="{{ paginator.previous_page_path }}" class="previous">Previous</a>
        {% else %}
            <span class="inactive previous">Previous</span>
        {% endif %}
        {% if paginator.next_page %}
            <a href="{{ paginator.next_page_path }}" class="next">Next</a>
        {% else %}
            <span class="inactive next">Next</span>
        {% endif %}
    </div>
</div>
