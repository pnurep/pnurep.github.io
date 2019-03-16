---
layout: single
categories:
  - Blogging
title: HHKB Bluetooth 모델 오류현상에 관해
author: Gold
author-email: pnurep@gmail.com
description: HHKB Bluetooth 모델의 이슈에 관해 알아봅니다.
tags:
  - Happy Hacking
  - HHKB
  - HHKB Bluetooth
  - Happy Hacking Bluetooth
last_modified_at: 2019-03-17T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
---

<br><br>

# HHKB Bluetooth 모델 오류현상에 관해

## 도입
해피해킹 블루투스. 이제는 정말 애증의 키보드가 되어버린것 같다. 처음 무접점에 입문하는 키보드로 사서 지금까지  내 키보드 컬렉션에 들어있다. 토프레 스위치의 특유의 키감과 배열은 코딩을 하는 사람에게는 ‘행복’한 기분을 느낄 수 있게 해줄것이다. 

<br>

## 문제
하지만 해피해킹 블루투스 모델에는 키보드에는 있어서는 안될 치명적인 오류가 존재한다.
__오입력__ 이다.
HHKB 블루투스 전 제품에 동일한 증상이 있는것 까지는 모르겠다. 하지만, 나를 포함해 주변에 HHKB 블루투스 모델을 사용하고 있는 사람들과 인터넷에 올라와 있는 글 들을 종합해 볼때 이것은 명백한 설계오류를 갖고 있다. 라고 밖에는 생각할 수 없다. 

여기서 말하는 오입력이란 무엇인가.
HHKB 블루투스 모델을 사용하는 도중, 갑자기 키를 눌러도 입력이 되지 않으며 사용자가 가장 마지막으로 입력했었던 키가 연속적으로 입력(key_down) 된다.
한마디로 마지막에 a 키를 눌렀다면 aaaaaaaaaaaaaaa 이런 식으로 입력이 되고 일정시간 지속 후 증상이 멎는것과 동시에 블루투스 연결이 끊어진다.

<br>

## 조사
내 주 작업환경이 맥이라 나는 지금까지 맥의 블루투스에 문제가 있어서가 아닐까라고 생각하고 있었다(2.4 G 와이파이 간섭문제라던지 여러 블루투스 장비를 연결했을 경우 끊김 현상 등). 하지만 이번만큼은 해피해킹의 문제라고 생각할 수 있을것같다.

아마존 재팬에 올라와 있는 HHKB 블루투스 모델에 대한 [상품페이지](https://www.amazon.co.jp/Hacking-Keyboard-Professional-%E8%8B%B1%E8%AA%9E%E9%85%8D%E5%88%97%EF%BC%8F%E5%A2%A8-PD-KB600B/product-reviews/B01DVH7C0O/ref=cm_cr_dp_d_hist_1?ie=UTF8&filterByStar=one_star&reviewerType=all_reviews#reviews-filter-bar)이다.


이 페이지에서 별 1점짜리 리뷰를 보면 정말 대부분이 오입력 및 끊김증상에 대해 말하고 있다.

<br>

아래 블로그들도 같은 증상을 말하고 있다.

[블로그리뷰](https://did2memo.net/2016/10/11/hhkb-bt-power-on-failure/)

[블로그리뷰 1](https://brwafe2.blogspot.com/2016/07/HHKB-BT-malfunction.html)

글들을 보면 PFU 도 이런 증상에 대해 인지는 하고있으나 그에 대한 대응은 그저 문제가 발생하는 경우에 한해 환불 혹은 교환정도에 그치는 것 같다. 나는 이 건에 대해 PFU 측에 문의를 보내놓았기는 했으나 답변이 올지는 미지수이다.

<br>

## 결론
아직 인터넷에서 해당 증상을 말하는 글들은 일본웹에서 본 글들이 대 부분이다. 내 검색키워드가 잘못되었는지는 모르겠지만 영어로 구글링을 해봤을때는 관련된 글들이 거의 보이지 않았다. 일본웹에서도 일단 해결법은 찾지 못했다. 현재로선 무선모델을 사용하지 않고 유선모델을 사용한다. 가 해결책일 수도 있겠다. 말도 안돼는 소리긴 하다. 군대식 문제해결법이다. 아마 유선 모델을 놔두고 굳이 블루투스 모델을 찾는 사람들은 나처럼 미니멀한 성향을 갖고있는 사람들일 것이다. 그런 사람들에게 이런 소리를 할 수 밖에 없다는 것이 참 슬프게 느껴진다. 하드웨어적인 문제인지 소프트웨어적인 문제인지 모르겠지만 어떤 방식으로든 이 문제가 해결되었으면 한다.



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}



