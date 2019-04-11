---
layout: single
categories:
  - TIS(Today I Sapziled)
  - Android
title: Ui AutomatorViewer 가 Please export ANDROID_SWT to… 에러를 뿜으며 안될 때에 관해
author: Gold
author-email: pnurep@gmail.com
description: Ui AutomatorViewer 가 Please export ANDROID_SWT to… 에러를 뿜으며 안될 때에 관해 알아봅니다
tags:
  - Android
  - Java
  - uiautomatorviewer
last_modified_at: 2019-03-31T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# Ui AutomatorViewer 가 Please export ANDROID_SWT to… 에러를 뿜으며 안될 때에 관해

<br>

## 개요

오랜만에 Ui AutomatorViewer 를 사용해 보려고 클릭했더니 이게 왠걸.
```
Please export ANDROID_SWT to point to the folder containing swt.jar for your platform
```

요딴 에러를 뿜뿜하며 열리지를 않는다.

<br>

## 문제원인

자바 버전도 바꿔줘 봤지만 헛수고.

그냥 에러를 바로 검색하지 말고 읽는게 더 빠를 뻔했다. ANDROID_SWT 에 swt.jar 파일의 경로가 들어가 있어야 하는데 그게 안되어서 에러가 발생했던것.

<br>

## 해결

터미널을 열고 ```open ~/.bashrc``` 를 열어 
```
export ANDROID_SWT = ${ANDROID_SDK}/tools/lib/x86_64
```
을 추가해준다. 해당 경로에 swt.jar 파일이 있다.


나는 zsh 쉘을 사용하지만 .zshrc 에서 .bashrc 를 로그인시마다 업데이트 하게 해놓았기 때문에 bash 쪽에만 추가를 해주었다.

그리고 ```$ source ~/.bashrc``` 로 새로고침 해주면 문제해결.



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























