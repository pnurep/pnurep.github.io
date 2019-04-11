---
layout: single
categories:
  - TIL(Today I Learned)
  - Android
title: 안드로이드 릴리즈 모드에서의 디버깅 방법에 대해
author: Gold
author-email: pnurep@gmail.com
description: 안드로이드 릴리즈 모드에서의 디버깅 방법에 대해 알아봅니다
tags:
  - Android
last_modified_at: 2019-04-10T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# 안드로이드 릴리즈 모드에서의 디버깅 방법에 대해

<br>

## 개요

릴리즈 모드에서 인수테스트를 하던 도중, 디버그 모드에서는 발생하지 않던 런타임에러가 발생하는 것을 확인하였다.

하지만 릴리즈 빌드에서는 디버그 모드일때 처럼 디버거를 붙일 수 없다. 어떻게 하면 릴리즈 모드에서도 디버깅이 가능할까?

<br>

## 해결

토스트를 띄운다거나 로그를 찍는다거나 디바이스 모니터에서 로그를 확인한다거나 하는 방법이 있겠지만 나는 디버거를 붙여보고 싶었다.


```
// Manifest 파일에서.
<application
        tools:ignore=“HardcodedDebugMode”
        android:debuggable=“true”
```

```groovy
// build.gradle(app)
buildTypes {
        release {
            debuggable true
        }
}
```

를 해주면 디버거를 붙일 수 있게되고 브레이크 포인트를 찍어서 디버깅을 할 수 있게 된다.
하지만 브레이크 포인트를 찍은 모든 부분에 걸리는 건 아니었다. 액티비티 단 정도는 걸리는걸 확인했으나 그 바로 아래 프래그먼트나 싱글턴 오브젝트에는 걸리지 않았다.

여하튼 이걸로 문제해결.

<br>

p.s. 디버깅 다 하고 위에서 어트리뷰트들 추가했던거 다시 다 반드시 원복 해야한다. 특히 매니페스트에 추가한것은 저대로 그냥 올리면 스토어에서 받지도 않는다.


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























