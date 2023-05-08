---
layout: page
title: Design
permalink: /design/
---

<div class="home">

  {%- if site.posts.size > 0 -%}
<!--    <ul class="post-list"> -->
      {%- for post in site.categories.examples -%}
<!--      <li> -->
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
          {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
          <span class="post-meta">{{ post.date | date: date_format }}</span>
        </h3>
        {%- if site.show_excerpts -%}
          <p> {{ post.excerpt | remove: '<p>' | remove: '</p>' }} <span class="read-more-link"> <a href="{{ site.baseurl }}{{ post.url }}">.. Read more</a> </span> </p>
        {%- endif -%}
<!--      </li> -->
      {%- endfor -%}
<!--    </ul> -->

  {%- endif -%}

</div>
