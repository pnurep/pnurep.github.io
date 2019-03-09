---
layout: single
categories: Functional_Programming
title: Functional Thinking Chapter 1. 왜
author: Gold
author-email: pnurep@gmail.com
description: 함수형사고_챕터_1_왜
keywords: Functional Programming, FP, Kotlin, Scala, Haskell, Groovy, Closure
last_modified_at: 2019-03-10T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
---

<br>

# Functional Thinking Chapter 1. 왜

잠시 당신이 나무꾼이라고 가정해보자. 당신은 숲에서 가장 좋은 도끼를 가지고 있고, 그래서 가장 일 잘하는 나무꾼이다. 그런데 어느 날 누가 나타나서 나무를 자르는 새로운 패러다임인 전기톱을 알리고 다닌다. 이 사람이 무척 설득력이 있어서 당신은 사용하는 방법도 모르면서 전기톱을 사게된다. 당신은 여태껏 해왔던 방식대로 시동을 걸지도 않고 전기톱으로 나무를 마구 두들겨댄다. 곧 당신은 이 새로운 전기톱은 일시적인 유행일 뿐이라고 단정하고 다시 도끼를 쓰기 시작한다. 그때, 누군가가 나타나서 전기톱의 시동거는 방법을 알려준다.

전혀 새로운 프로그래밍 패러다임의 문제점은 새로운 언어를 배우는 것이 아니다. 이 글을 읽고 있는 모두가 이제껏 수도 없는 컴퓨터언어를 배워오지 않았는가? 문법은 한낱 세부사항일뿐이다. 어려운 점은 바로 <b>다른 방식으로 사고하는 법을 배우는 것이다.</b>

'함수형'으로 코드를 짜는 것은 설계 트레이드오프, 재사용할 수 있는 여러 빌딩블록, 그리고 다른 여러가지의 통찰력과 연결된다.

스칼라나 클로저에 관심이 없고, 지금 사용하는 언어로 앞으로 평생 개발해도 상관없다고 생각하더라도, 언어는 당신 발밑에서 점점 함수형으로 바뀔것이다. 사용하는 언어에 나중에 함수형 패러다임이 도입되었을 때 잘 사용하려면, 지금 배워야 한다.이제 왜 모든 언어가 점차적으로 함수형이 되어가는지를 알아보자.

<br>

## 1.1 패러다임 전환

컴퓨터 과학은 단속적으로 발전한다. 수십 년 전의 훌륭한 아이디어가 어느 날 갑자기 주류에 포함되곤 하는 식이다. 종종 훌륭한 아이디어는 기반이 되는 기술이 쫓아오기를 기다리곤 한다. 자바는 초창기에는 느리고 메모리를 과하게 사용해서 고성능 애플리케이션용으로는 적합하지 않다고 여겨졌지만, 하드웨어 시장의 변화로 선호도가 높아졌다.

함수형 프로그래밍은 객체지향과 개념적으로 같은 궤도를 따른다. 지난 이삼십 년간 학계에서 연구되었고, 현대 프로그래밍 언어들에 조금씩 도입되어왔다. 그렇다고 새로운 문법만 추가한다고 해서 개발자들에게 새로운 사고방식까지 알려줄 수는 없다.

우선 전통적인 프로그래밍 스타일(명령형 루프)과 좀 더 함수형스러운 방법으로 같은 문제를 대조해 보면서 시작해보자. 텍스트 파일을 읽고, 가장 많이 사용된 단어들을 찾고, 그 단어들과 빈도를 정렬된 목록으로 출력한다.


```java
import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Words {

	{% raw %}
    private Set<String> NON_WORDS = new HashSet<String>() {{
        add("add"); add("and"); add("of"); add("to"); add("a");
        add("i"); add("it"); add("in"); add("or"); add("is");
        add("d"); add("s"); add("as"); add("so"); add("but");
        add("be");
    }};
    {% endraw %}

    // 자바로 빈도수 세기
    public Map wordFreq(String words) {
        TreeMap<String, Integer> wordMap = new TreeMap<>();
        Matcher m = Pattern.compile("\\w+").matcher(words);
        while (m.find()) {
            String word = m.group().toLowerCase();
            if (!NON_WORDS.contains(word)) {
                if (wordMap.get(word) == null) {
                    wordMap.put(word, 1);
                } else {
                    wordMap.put(word, wordMap.get(word) + 1);
                }
            }
        }

        return wordMap;
    }

}

```

위의 예제에서는 우선 비 단어 집합을 만들고, wordFreq() 안에서 키/값을 저장할 Map을 생성하고, 단어를 찾는 정규표현식을 작성했다. 이 코드의 대부분은 발견된 단어들을 반복 처리한다. 이런 코딩 방식은 정규표현식 매칭과 같이 컬렉션을 처리하는 언어에서는 상당히 보편화되어 있다.

<br>

자바 8의 Stream API나 람다 블록을 이용한 고차함수를 사용해서 새로 만든 버전인 아래 예제를 보자.

```java

// 자바 8로 단어 빈도수 세기
    private List<String> regexToList(String word, String regex) {
        List<String> wordList = new ArrayList<>();
        Matcher m = Pattern.compile(regex).matcher(word);
        while (m.find())
            wordList.add(m.group());

        return wordList;
    }

    public Map wordFreqInJava8(String words) {
        TreeMap<String, Integer> wordMap = new TreeMap<>();
        regexToList(words, "\\w+").stream()
                .map(w -> w.toLowerCase())
                .filter(w -> !NON_WORDS.contains(w))
                .forEach(w -> wordMap.put(w, wordMap.getOrDefault(w, 0) + 1));

        return wordMap;
    }

```

위 예제에서는 정규표현식 검색 결과를 스트림으로 바꿔서 다음의 각 연산을 수행하게끔했다. 먼저 모든 단어를 소문자로 바꾼다. 비 단어를 걸러낸다. 나머지 단어들의 빈도 수를 측정한다. find()의 결과인 반복자를 regexToList() 메서드를 통해 스트림으로 변환하여 필요한 연산을 차례대로 실행할 수 있게 되었다. 이것은 내가 이 문제에 대해서 생각하는 것과 같은 방식이다. 기존의 자바 명령형 방식에서도 소분자로 만들고, 비 단어를 필터하고, 단어 빈도수를 세는 방식으로 컬렉션을 반복처리 할 수 있겠지만, 그렇게 하면 무척 비효율적일 것이다. 명령형 프로그래밍을 하다보면 효율을 높이기 위해 여러 작업을 한 루프에 넣음으로써, 작업들을 복잡하게 하는 경우가 종종 있다. 함수형 프로그래밍에서는 map() 이나 filter() 같은 고차함수를 통해 추상화의 단계를 높여서 문제를 더욱 명료하게 볼 수 있다.

<br>

## 1.2 언어 트렌드에 발 맞추기

주요 언어들이 변화하는 것을 보면 모두 다 함수형 기능을 더하고 있다. 그루비는 메모이제이션과 같은 고급기능을 포함한 함수형 기능을 추가해왔다. 그리고 마침내 자바에서도 람다블럭이 도입되었고 이미 자바스크립트에서는 많은 함수형 기능이 들어 있다. 이런 패러다임을 배우면 클로저 같은 새 언어를 쓰든 지금 사용하는 언어를 계속 쓰든, 함수형 기능들이 도입되자마자 사용할 수 있게 된다.

<br>

## 1.3 언어 / 런타임에 제어를 양도하기

짧은 컴퓨터과학의 역사 속에서도 주류 기술은 실용적 또는 학문적인 이유로 가지를 치곤 했다. 일례로 90년대에는 PC 가 보편화 되면서 dBase, 클리퍼, 폭스프로, 패러독스 같은 소위 4세대 언어들이 폭발적으로 유행했다. C 나 파스칼같은 3세대 언어와 비교해 볼 때 이 언어들의 장점은 고수준의 추상화였다. 다시 말해, 4세대 언어에서는 명령하나로 3세대 언어에서는 여러개의 명령이 필요했던 일을 할 수 있었다. 함수형 프로그래밍도 이와 유사하게 컴퓨터 과학자들이 새로운 아이디어와 패러다임을 표현할 방법을 모색하던 학계에서 가지를 친 경우라고 할 수 있다.

썬 마이크로시스템즈에서는 90년대의 하드웨어에서 간신히 실행 가능한 자바를 출시했다. 자바에는 자동화된 가비지컬렉션 같은 개발자를 위한 기능도 더해졌다. 나 역시 더는 가비지 컬렉션이 없는 언어로 코딩하고 싶지 않다. 이제는 고수준의 추상화를 통해 복잡한 비즈니스 문제를 풀 궁리를 하지, 까다로운 저수준문제는 더 이상은 생각하고 싶지 않다. 자바는 메모리 관리에 관한 어려움을 줄여주었고 나는 이를 향유하고 있다.

시간이 갈수록 개발자는 지루한 일들을 언어나 런타임에 점점 더 맡기게 된다. 애플리케이션을 만들면서 직접 메모리를 제어하지 않는다는 것을 조금도 후회하지 않는다. 그런 일에 무관심해졌기 때문에 좀 더 중요한 문제들에 집중할 수 있다. 자바가 메모리 관리 작업을 쉽게 해줬다면, 함수형 프로그래밍 언어는 다른 빌딩블록들을 고수준 추상적 개념으로 대체해준다.



tip. malloc 에 시달리기엔 인생은 너무 짧다.


<br>


## 1.4 간결함

레거시 코드 활용 전략 (2008) 의 저자 마이클 페더스는 함수형과 객체지향형 추상화의 차이점에 대해서 트위터에 140자로 다음과 같이 썼다.

> 객체지향 프로그래밍은 움직이는 부분을 캡슐화하여 코드이해를 돕고, 함수형 프로그래밍은 움직이는 부분을 최소화하여 코드이해를 돕는다.

객체지향 프로그래밍 구조에 대해 생각해보자. 캡슐화, 스코핑, 가시성 등의 메커니즘은 상태(State)변화를 누가 볼 수 있는지에 대한 세밀한 제어를 위해 존재한다. 상태에 스레드까지 곁들이면 골칫덩이는 더욱 커진다. 이러한 메커니즘이 바로 페더스가 말하는 '움직이는 부분'이다. 함수형 언어는 이런 가변(mutable) 상태를 <b>제어</b>하는 메커니즘을 구축하기보다, 그런 '움직이는 부분'을 아예 <b>제거</b>하는데 주력한다. 언어가, 오류가 발생하기 쉬운 기능을 적게 노출하면 그만큼 개발자가 오류를 만들 가능성이 줄어든다는 이론에 따른 것이다.

객체지향 명령형 프로그래밍 언어에서, 재사용의 단위는 클래스와 그 클래스들이 주고받는 통신 메시지이고, 이는 크랠스 다이어그램으로 포착할 수 있다. 'GOF 디자인 패턴' 은 패턴당 적어도 하나의 클래스 다이어그램을 포함하고 있다. OOP의 세계에서는 고유한 자료구조를 작성하는 것을 권장한다. 그 자료구조에 특정 동작을 메서드의 형태로 부착해서 말이다. 함수형 프로그래밍 언어는 같은 방식으로 재사용을 달성하려 하지 않고, 최적화된 동작으로 몇몇 자료구조(list, set, map) 를 이용하는 방식의 재사용을 선호한다. 개발자가 이런 방법을 잘 사용하려면, 특정 용도로 정의된 방법에 자료구조와 고차함수를 함께 넣어야 한다.

위의 예제의 일부를 간략화하여 살펴보자.
```java
regexToList(words, "\\w+").stream()
                .filter(w -> !NON_WORDS.contains(w))
```

list 에서 일부분을 꺼내려면 스트림 형태의 목록과 필터조건이 구현된 고차함수를 넣어서 filter() 메서드를 호출하면 된다. 함수 수준의 캡슐화는, 모든 문제에 대한 새로운 클래스 구조를 구축하는 것보다 세분화되고 기초적인 수준에서 재사용을 가능하게 한다.

함수형 개발자는 적은 수의 자료구조와 그것들을 잘 이해하기 위한 최적화된 방법을 만들기를 선호한다. 객체지향형 개발자는 항상 새로운 자료구조와 그것에 부착된 메서드를 만든다. 클래스와 통신 메시지를 만드는 것이 지배적인 객체지향 패러다임이다.


아파치 커먼즈의 indexOfAny()

```java
public class StringUtils {

    public static int indexOfAny(String str, char[] searchChars) {
        if (isEmpty(str) || ArrayUtils.isEmpty(searchChars))
            return INDEX_NOT_FOUND;  // 안전장치

        int csLen = str.length();  // 초기화
        int csLast = csLen - 1;
        int searchLen = searchChars.length;
        int searchLast = searchLen - 1;

        for (int i = 0; i < csLen; i++) {  // 외부 반복
            char ch = str.charAt(i);
            for (int j = 0; j < searchLen; j++) {  // 내부 반복
                if (searchChars[j] == ch) {  // 결정, 결정, 또 결정
                    if (i < csLast && j < searchLast && isHighSurrogate(ch)) {
                        if (searchChars[j + i] == str.charAt(i + 1)) {
                            return i;
                        }
                    } else {
                        return i;
                    }
                }
            }
        }

        return INDEX_NOT_FOUND;
    }

}
```

indexOfAny() 메서드는 문자열과 배열을 받아들이고 배열 내의 어느 문자이든 문자열에서 처음으로 발견되는 위치를 리턴한다.

```java
StringUtils.indexOfAny("zzabyycdxx", 'z', 'a') == 0
StringUtils.indexOfAny("zzabyycdxx", 'b', 'y') == 3
StringUtils.indexOfAny("aba", 'z') == -1
```

위 예제에서 볼 수 있듯이 z 나 a 는 zzabyycdxx 의 위치 0에서 처음 발견된다. 또 b 나 y 는 위치 3에서 처음 나타난다. 이 문제의 본질은 다음과 같이 설명할 수 있다. searchChars 각각에 대해, 대상 문자열 안에서 처음 발견되는 위치를 찾으라, 스칼라로 구현된 firstIndexOfAny 메서드는 아래처럼 훨씬 간단하다.

```scala
def firstIndexOfAny(input: String, searchChars: Seq[Char]) : Option[Int] = {
            def indexedInput = (0 until input.length).zip(input);
            val result = for (pair <- indexedInput;
                              char <- searchchars;
                              if (char == pair._2)) yield (pair._1)
                if (result.isEmpty)
                    None
                else
                    Some(result.head)
    }
```

위 예제에서는 우선 입력 문자열의 색인 버전을 만들었다. 스칼라의 zip() 메서드는 입력 문자열의 길이만큼의 자연수 컬렉션을 받아서 문자열의 문자들과 결합하여 숫자와 문자의 짝으로 이루어진 새로운 컬렉션을 만든다. 일단 색인 컬렉션을 만들면, 스칼라의 for() 평가를 사용하여 검색 대상 문자 컬렉션을 살펴보고, 다음에는 색인 컬렉션에서 그 짝 요소를 가져올 수가 있다. 스칼라에서는 컬렉션 요소를 간단히 가져올 수 있기 때문에 현재의 검색 결과를 컬렉션의 두 번째 항목과 비교할 수가 있다. 조건이 맞으면, 짝 요소의 색인 부분인 (pair._1) 을 리턴한다.

자바를 사용할 때는 null 때문에 문제가 많이 발생한다. null 은 제대로 된 리턴 값인다, 아니면 값이 없다는 뜻인가? 스칼라를 포함한 많은 함수형 언어는 Option 클래스를 사용하여 이런 모호함을 피한다. Option 클래스는 리턴 값이 없음을 표현하는 None 과 리턴 값을 포함하는 Some 을 포함한다. 예제와 같은 문제는 첫 번째 검색결과만을 묻기 때문에, 결과 컬렉션의 첫 번째 요소인 result.head 를 리턴하게 했다.

원래 문제는 검색 결과 중 첫 번째 만을 요구하지만, 결과를 모두 반환하는 버전을 만드는 것도 아주 간단하다. 모든 결과를 반환하게끔 예제를 바꾸려면, 아래처럼 리턴 값의 자료형을 바꾸고 래퍼를 제거하기만 하면 된다.

```scala
//게으른 검색 목록 반환하기
def indexOfAny(input: String, searchChars: Seq[Char]) : Seq[Int] = {
	def indexedInput = (0 until input.length).zip(input)
    for (pair <- indexedInput;
    	 char <- searchChars;
         if (char == pair._2)) yield (pair._1)
}
```

API 를 제약하기 보다는 사용자가 몇 개의 값을 원하는지 선택할 수 있게 하는것이 좋다. firstIndexOfAny("zzabyycdxx", "by") 를 실행해보면 결과는 3이 리턴되고 indexOfAny("zzabyycdxx", "by") 를 실행하면 Vector(3, 4, 5) 가 리턴된다.




<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























