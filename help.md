---
layout: page
title: Help
permalink: /help/
---

<div>

<h3 class="help-category">General</h3>

{%- if site.posts.size > 0 -%}

  {% assign sortedPosts = site.categories.general | sort: 'date' %}

  {%- for post in sortedPosts -%}

      <a class="help-link" href="{{ post.url | relative_url }}">
        {{ post.title | escape }}
      </a>

  {%- endfor -%}

{%- endif -%}  

</div>

<div>

<h3 class="help-category">Classes</h3>

{%- if site.posts.size > 0 -%}

  {% assign sortedPosts = site.categories.classes | sort: 'title' %}

  {%- for post in sortedPosts -%}

      <a class="help-link" href="{{ post.url | relative_url }}">
        {{ post.title | escape }}
      </a>

  {%- endfor -%}

{%- endif -%}

</div>
