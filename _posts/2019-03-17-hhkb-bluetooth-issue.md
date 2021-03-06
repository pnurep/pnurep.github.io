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
sitemap :
  changefreq : daily
  priority : 1.0
---

<br><br>

# HHKB Bluetooth 모델 오류현상에 관해

## 도입
해피해킹 블루투스. 이제는 정말 애증의 키보드가 되어버린것 같다. 처음 무접점에 입문하는 키보드로 사서 지금까지  내 키보드 컬렉션에 들어있다. 토프레 스위치의 특유의 키감과 배열은 코딩을 하는 사람에게는 ‘행복’한 기분을 느낄 수 있게 해줄것이다.

<br>

## 문제
하지만 해피해킹 블루투스 모델에는 키보드에는 있어서는 안될 치명적인 오류가 존재한다.
__오입력(채터링)__ 이다.
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
아직 인터넷에서 해당 증상을 말하는 글들은 일본웹에서 본 글들이 대부분이다. 내 검색키워드가 잘못되었는지는 모르겠지만 영어로 구글링을 해봤을때는 관련된 글들이 거의 보이지 않았다. 일본웹에서도 일단 해결법은 찾지 못했다. 현재로선 무선모델을 사용하지 않고 유선모델을 사용한다. 가 해결책일 수도 있겠다.

말도 안돼는 소리긴 하다. 군대식 문제해결법이다. 아마 유선 모델을 놔두고 굳이 블루투스 모델을 찾는 사람들은 나처럼 미니멀한 성향을 갖고있는 사람들일 것이다. 그런 사람들에게 이런 소리를 할 수 밖에 없다는 것이 참 슬프게 느껴진다. 하드웨어적인 문제인지 소프트웨어적인 문제인지 모르겠지만 어떤 방식으로든 이 문제가 해결되었으면 한다.


<br>


## 추가

일단 연속타이핑의 원인은 맥과 HHKB 간의 블루투스 연결이 끊어질때 키를 누르고 있던 상태라면 블루투스 연결이 끊어짐에 따라 key_down 이벤트만 OS 가 받고 key_up 은 OS 에 전달할 수 없는 상황이 되어버린다. 그렇기에 key_down 이벤트가 연속으로 들어가는 것이다.

아래는 일본웹에 올라와 있던 어떤 블로거가 올린 PFU 측에서 보낸 답변이다.

> 이번에 키보드의 연결이 불안정한 건으로 불편을 끼쳐 드려 죄송합니다.
> Bluetooth 인터페이스의 특성상 환경에 따라 소음이나 전파의 간섭 등에 의해 절단되어 버릴 수 있습니다. 키가 눌러 진 상태에서 Bluetooth 연결이 끊어하면 OS가 키의 중단 신호를 감지 할 수 없기 때문에 키 입력이 연속 할 수 있습니다.
>
> 환경에 따라 어쩔 수 없는 부분도 있습니다. 이해부탁드립니다.
> PFU 지원 센터

구매 기간이나 상황에 따라 교환 받은 사람도 있는것 같은데 이 블로거의 경우에는 받아들여지지 않은 모양이다. 대신 본사쪽으로 물건을 보내주면 펌웨어 업데이트를 배송비까지 포함해 무상으로 해주겠다. 라는 답변은 받은 것 같다.

<br>

우선 일본 웹에서 검색해본 내용들로 대처를 해보려고 한다.

### Mac 에서의 조치

기존의 조치
 * HHKB 맥 드라이버 최신버전 설치
 * 2.4G 와이파이 사용해제
 * 이 Mac과 iCloud 기기 간에 Handoff 허용 -> 해제
 * 맥 블루투스 -> 디버그 -> Apple 기기에 연결된 모든 기기를 초기 설정 값으로 재설정
 * 맥 키보드 -> 반복 지연 시간 을 길게 조정. (연속한 key_down 이벤트가 발생할 경우에 대한 피해를 최소화하기 위함. 대신에 ㅋㅋㅋㅋㅋㅋ 를 꾹 눌러서 사용하는 사람들에게는 불편할 수 있다. 굳이 ㅋ 갯수만큼 타이핑을 해야하니)

새로운 조치
 * 맥 네트워크 -> 왼쪽 패널중 Bluetooth PAN 선택 -> 아래 톱니버튼 클릭 후 서비스 비활성화
 * 맥 재부팅 후 __키보드를 끄고__ DIP 스위치 6 번을 ON 으로 한 후 다시 페어링
 * HHKB BT 본체 리셋
   * 키보드의 전원이 켜진상태에서 Fn 키 + Q 키 를 동시에 누른다. -> 파란색 LED 가 점멸된다.
   * Fn + Z + BackSpace(영어 배열의 경우 Delete) 를 동시에 누른다. -> 주황색 LED 가 잠시 켜진 후 꺼진다.
   * HHKB BT 재설정 완료

 * [NVRAM 리셋](https://support.apple.com/ko-kr/HT204063)

<br>

위의 방법으로 해당 블로거는 연결이 갑자기 끊어지는 문제는 잡을 수 있었다고 한다. 하지만 문자 연속 입력 문제는 완벽히 사라지지 않았고 오히려 작업 환경을 바꾸는 것으로 해결을 보았다고 한다(문제가 해결되었다는 블로거도 존재함).

블루투스와 같은 무선연결은 다른 전파에 의한 혼선 가능성이 있으므로 여러 전파가 혼재한 카페등에서 작업하지 않고, 같이 사용중이던 Mx AnyWhere 2S 를 사용하지 않았더니 연속입력문제가 사라졌다고 말하고 있다(마우스를 못쓰게 되는게 해결이냐;;).

<br>

일단 상기한 방법이 현재까지 찾은 방법이므로 시도해 보고 잘 되면 좋은거고 안되면 팔고 type-s 로 넘어가던지... 조금 생각을 해 봐야겠다.

<br>


\+ ’19.03.21. 추가
* pfu 측에서 온 답변을보면 대체적으로 상기한 내용에서 큰 차이는 없지만 “로지텍 사의 디바이스와의 궁합이 좋지 못한것 같은 보고를 받고있다” 라는 내용을 확인할 수 있었다.
* 상기한 추가적인 해결법을 적용하고 3 ~ 4 일 정도 경과했는데 지금까지 단 1 회의 끊김이 있었다. 기존 하루에도 최소 5번은 끊기던것과 비교했을때 아주 괄목할만한 결과다.


\+ '19.03.29. 추가
* 새로운 증상을 발견. 오랜시간 방치 후 다시 슬립모드에서 깨우면 연속오입력 증상이 생긴다. 독특한 부분은, 기존의 오입력과는 다르게 입력이 된다는 점이다. 즉 마지막으로 눌렀던 키 x 는 연속오입력을 하는 상태 + 해피해킹에서 문자입력가능 의 중첩상태다. 일단 hhkb 와의 연결을 끊거나 맥의 블루투스를 끄면 증상은 사라진다. -> 이런 증상을 검색하다 본 것 같다. 찾아봐야겠다... -> dip 스위치 6 번을 일단 off 로 함.
* 맥의 블루투스 문제로 애플측에 문의해본 결과 16 년형 맥북프로에서 usb-c 포트에 어댑터나 케이블 등을 연결시에 Wi-Fi 및 Bluetooth 의 연결이 끊기거나 원할하게 되지 않는 것에 대한 보고된 내용이 있었다고 한다. 증상이 있다면 제품의 개발사쪽에 연락해 해당하는 이슈가 있었는지 확인을 하는것을 권장한다고 한다. 애플 내에서 판매되는 제품은 그런 이슈는 없다고 단언함.


<br>


### Windows 환경에서의 조치

혹시 모를 윈도우 사용자들을 위한 방법도 남긴다.

* 전원 OFF 상태로 DIP 스위치 6을 ON으로 한 후, 키보드의 전원을 투입하여 다시 페어링을하고 동작이 안정 있는지 확인하십시오.
* 제어판의 장치 관리자에서 Bluetooth 무선을 열고 드라이버를 더블 클릭하십시오. Windows 표준 드라이버를 사용하는 경우에는 "Generic Bluetooth Radio"입니다. 전원 관리 탭을 열고 "전원을 절약하기 위해 컴퓨터가 이 장치를 끌 수 있도록한다"의 체크를 해제합니다. -> OK 버튼
* 제어판의 "네트워크 및 공유 센터" 를 시작하고 "어댑터 설정 변경"을 클릭하십시오. Bluetooth 네트워크 연결을 마우스 오른쪽 클릭하여 "사용 안 함"을 클릭하십시오. 컴퓨터를 다시 시작한 후 키보드를 연결하여 입력을 시도해보십시오. 그래도 효과가 보이지 않을 경우에는 다음의 설정도 확인 바랍니다.
* 제어판 → 하드웨어 및 소리 → 전원 옵션 → 전원 관리 옵션 설정 편집 (Windows7의 경우)보다 "고급 전원 설정 변경 "을 클릭하십시오. 상세 설정 화면의 무선 어댑터 설정 → 절전 모드로 로그인 하시고 설정에서 "최대 성능"을 선택하십시오. 컴퓨터를 다시 시작한 후 키보드를 연결하여 입력을 시도해보십시오.



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}



