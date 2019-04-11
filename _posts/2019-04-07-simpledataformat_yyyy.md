---
layout: single
categories:
  - TIL(Today I Learned)
  - Android
title: SimpleDataFormat 의 YYYY 에 대해
author: Gold
author-email: pnurep@gmail.com
description: SimpleDataFormat 의 YYYY 에 대해 알아봅니다
tags:
  - Android
  - Java
last_modified_at: 2019-04-07T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# SimpleDataFormat 의 YYYY 에 대해

<br>

## 개요

SimpleDataFormat 을 사용하던 도중 왠 yyyy 에 관련한 에러가 발생했다. 

지금까지 SImpleDataFormat 을 사용할때 YYYY 부분이 소문자이어야 하는지 별로 관심이 없었긴 했다.

<br>

## 문제원인

java 7 까지는 SimpleDataFormat 에서 YYYY 를 대문자로 사용하면 안된다 고 한다.

<br>

## 해결

고로 yyyy 소문자로 변경



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























