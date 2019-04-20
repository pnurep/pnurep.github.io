---
layout: single
categories:
  - TIS(Today I Sapziled)
  - Android
title: companion object 의 내부에 있는 lateinit var 필드에 isInitialized 검사시의 NoSuchFieldError 에 대해
author: Gold
author-email: pnurep@gmail.com
description: companion object 의 내부에 있는 lateinit var 필드에 isInitialized 검사시의 NoSuchFieldError 에 대해 알아봅니다
tags:
  - Kotlin
  - Android
last_modified_at: 2019-04-20T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# companion object 의 내부에 있는 lateinit var 필드에 isInitialized 검사시의 NoSuchFieldError 에 대해

<br>

## 개요

안드로이드 개발도중 ```companion object``` 의 내부에 있는 ```lateinit var``` 필드에 접근해 ```isInitialized``` 검사를 했을때 ```java.lang.NoSuchFieldError: No field instance of type Lcom/...``` 에러가 발생한다.

<br>

## 문제원인

코틀린에 내부 버그로 이미 [이슈](https://youtrack.jetbrains.com/issue/KT-21862)가 올라와 있었다.

<br>

## 해결

* 인텔리제이에서 코틀린 버전을 바꿔가면서 실행해본 결과 1.3.11, 1.3.21 버전 둘 다 에러가 발생하지 않았다.
* 하지만 안드로이드의 경우 1.3.21 버전에서는 에러가 발생하지 않았으나 1.3.11 버전에서 에러가 발생하였다.
* 처음에 에러를 발견한 것도 1.3.11 버전에서 였다.
* stackoverflow 에 있는 래퍼형식의 솔루션도 ```isInitialized``` 호출되는 순간 죽는다.

<br>

<b>결론 : 1.3.21 버전을 사용하자(1.3.30 은 현재 버그가 존재한다고 한다).<b>


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























