---
layout: single
categories:
  - TIS(Today I Sapziled)
  - Android
title: Kitkat 에서 Vector 관련한 InflateException Binary XML file line… 에러에 대해
author: Gold
author-email: pnurep@gmail.com
description: Kitkat 에서 Vector 관련한 InflateException Binary XML file line… 에러에 대해 알아봅니다
tags:
  - Android
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

# Kitkat 에서 Vector 관련한 InflateException: Binary XML file line… 에러에 대해

<br>

## 개요

com.santalu.emptyview.EmptyView 라는 라이브러리에서 ```InflateException: Binary XML file line``` 에러를 만남.

<br>

## 문제원인

벡터드로워블 관련 이슈로, 라이브러리 내부적으로 드로워블을 가져올때 하위기종에 대한 호환성을 생각하지 않은듯 하다. 이슈에도 올라와 있는데 안고친듯;;(무슨 배짱이야)

<br>

## 해결

그래도 내부적으로 세터 메서드가 있었기 때문에 직접 코드적으로 적용해 줬더니 해결되었다.

```groovy
// 일단 기본적인 벡터를 사용하기 위한 준비…
defaultConfig {
    vectorDrawables.useSupportLibrary = true
}
```

<br>


1 법
```kotlin
AppCompatResources.getDrawable(activity!!, R.drawable.ic_sentiment_very_dissatisfied)
```

2 법
```kotlin
//Application 단의 oncreate() 에서
AppCompatDelegate.setCompatVectorFromResourcesEnabled(true)

ContextCompat.getDrawable(activity!!, R.drawable.ic_sentiment_very_dissatisfied)
```

3 법
```kotlin
// 이 방법도 있나보다
VectorDrawableCompat.create(getResources(), drawableRes, null)
```


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























