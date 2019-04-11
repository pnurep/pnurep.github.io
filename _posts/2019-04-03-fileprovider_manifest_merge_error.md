---
layout: single
categories:
  - TIS(Today I Sapziled)
  - Android
title: Android FileProvider 사용시 AndroidManifest merge error 관해
author: Gold
author-email: pnurep@gmail.com
description: Android FileProvider 사용시 AndroidManifest merge error 관해 알아봅니다
tags:
  - Android
last_modified_at: 2019-04-03T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# Android FileProvider 사용시 AndroidManifest merge error 관해

<br>

## 개요

안드로이드 개발도중 FileProvider 를 새로 넣어줘야 하는 일이 생겨서 통합중

```
AndroidManifest merge error using FileProvider
```
요딴 에러가 뿜뿜 해주셨다.

<br>

## 해결

stackOverFlow 에 있는 몇 가지 방법들을 시도해 봤으나 나아지지 않아서 그냥 예전에 했던 방식 그대로를 했다.
```kotlin
class MyFileProvider : FileProvider()

<provider android:name=“.MyFileProvider” … >
```


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























