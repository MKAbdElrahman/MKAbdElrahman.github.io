---
layout: page
title: home
---

# Mohamed Kamal AbdElrahman

## Some things you should know

* My name is Mohamed
* I work on optimization/inverse design of photonic devices
* I love applied math, physics, computer science and music

## Some things I've worked on

* [LazyFDFD](https://github.com/MKAbdElrahman/LazyFDFD), A MatrixFree Finite Difference Frequency Domain Electromagnetic Solver

## Some things you can click on

[GitHub](https://github.com/MKAbdElrahman), [Twitter](https://twitter.com/Mohamed81834855)
## Some things you can read

<ul>
{% for post in site.posts %}
<li>
  <a href="{{post.url}}">{{post.title}}</a> ({{post.date | date: '%B %Y' }})
</li>
{% endfor %}
</ul>

[RSS feed](/feed.xml)
