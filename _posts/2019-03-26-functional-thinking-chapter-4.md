---
layout: single
categories:
  - Functional Programming
title: Functional Thinking Chapter 4. 열심히보다는 현명하게
author: Gold
author-email: pnurep@gmail.com
description: 함수형사고_챕터_4_열심히보다는_현명하게
tags:
  - Functional Programming
  - FP
  - Kotlin
  - Scala
  - Haskell
  - Groovy
  - Closure
last_modified_at: 2019-03-26T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# 열심히보다는 현명하게

패러다임을 바꾸면 더 적은 노력으로 더 많은 일을 할 수 있는 득을 보게 된다. 함수형 프로그래밍에서 나타나는 많은 구조들이 그렇다. 흔히 볼 수 있는 문제들을 구현할 때 짜등 나던 것들을 제거해준다.

이 장에서는 함수형 언어에서 보편적인 두 가지 기능에 대해서 알아본다. <b>메모이제이션</b>과 <b>게으름</b>이다.

<br>

## 4.1 메모이제이션

메모이제이션이란 단어는 영구의 인공지능 연구학자인 도널드 미치가 연속해서 사용되는 연산값을 함수레벨에서 캐시하는 것을 지칭하는 것으로 처음 사용하였다. 노늘날 메모이제이션은 함수형 프로그래밍 언어에서 흔히 내장된 기능이거나 쉽게 구현할 수 있는 기능이다.

메모이제이션은 다음과 같은 상황에서 유용하다. 시간이 많이 걸리는 연산을 반복적으로 사용해야 한다고 가정해보자. 보편적인 해결 방법은 내장 캐시를 설정하는 것이다. 주어진 매개변수를 사용하여 연산을 할 때마다, 그 값을 매개변수를 키 값으로 하는 캐시에 저장한다. 후에 이 함수가 같은 매개변수로 호출되면 다시 연산하는 대신에 캐시의 값을 리턴한다. 함수 캐싱은 전형적인 컴퓨터 과학의 트레이드오프이다. 이 방법은 좋은 성능을 위해서 메모리를 더 많이 사용한다.(메모리가 충분한 경우가 대부분이다).

캐싱 방법이 제대로 작동하려면 함수가 <b>순수</b>해야 한다. 순수함수란 부수효과가 없는 함수를 말한다. 가변(mutable) 클래스 필드를 참조하지도 않고, 리턴 값 외에는 아무 값도 쓰지 않아야 하며, 주어진 매개변수에만 의존해야 한다. java.lang.Math 의 모든 메서드가 순수함수의 좋은 예이다. 물론, 주어진 매개변수에 대해 항상 같은 값을 리턴하는 함수에 한해서만 캐시된 값을 재사용 할 수 있다.

<br>

### 4.1.1 캐싱

캐싱은 흔한 요구사항이다(동시에 찾기 어려운 오류의 근원이기도 하다). 캐싱에는 크게 두 가지의 형태로 많이 사용된다. 클래스 내부에서의 사용과 외부에서의 사용이다. 더불어 캐싱을 구현하는 두 가지 방법도 알아보자. 수작업으로 만든 상태와 메모이제이션이 그것이다.

<br>


#### 메서드 레벨에서의 캐싱

자연수를 분류하는 Classifier 클래스가 있다고 가정해보자. 분류를 위해 같은 수를 이 클래스의 여러 메소드에 주는 일이 다반사이다. 예를 들어 다음의 코드를 보자.

```java

if (Classifier.isPerfect(n)) print "!"
else if (Classifier.isAbundant(n)) print "+"
else if (Classifier.isDeficient(m)) print "-"

```

이것이 <b>클래스 내부 캐싱</b>(intraclass caching) 의 예이다. 이렇게 구현한 경우, 모든 분류 메서드를 호출할 때마다 매개변수의 합을 계산해야 한다. 이렇게 자주 호출된다면 이는 상당히 비효율적인 접근 방법이다.

<br>

#### 합산 결과를 캐시하기

이미 수행된 결과를 재사용하는 것이 코드를 효율적으로 만드는 한 방법이다. 매개변수의 합을 구하는 것이 어렵기 때문에 각 수마다 한 번만 계산하고자 한다. 그러기 위해서는 계산결과를 저장할 캐시를 만들어야 한다.

```groovy
//합을 캐시하기
private sumCache = [ : ]

def sumOfFactors(number) {
    if (!sumCache.containsKey(number)) {
        sumCache[number] = factorsOf(number).sum()
    }

    return sumCache[number]
}
```

위의 예제에서는 sumCache 라는 캐시를 저장할 공간을 만든다. sumOfFactors() 메서드 내부에서는 캐시에 매개변수로 질의를 해보고 합이 들어 있다면 그 값을 바로 리턴한다. 값이 없다면 힘든 계산을 수행하고 리턴값을 캐시에 넣은 후에 리턴한다.


<br>


#### 전부 다 캐시하기

합을 캐싱해서 코드가 많이 빨라졌으니, 모든 중간 결과를 캐시하면 어떤 결과가 나올까?

```groovy
private sumCache = [ : ], factorCache = [ : ]

def sumOfFactors(number) {
    if (!sumCache.containsKey(number)) {
        sumCache[number] = factorsOf(number).sum()
    }

    return sumCache[number]
}

def isFactor(number, potential) {
    number % potential == 0
}

def factorsOf(number) {
    if (!factorCache.containsKey(number)) {
        factorCache[number] = (1..number).findAll{ isFactor(number, it) }
    }

    return factorCache[number]
}

def isPerfect(number) {
    sumOfFactors(number) == 2 * number
}

def isAbundant(number) {
    sumOfFactors(number) > 2 * number
}

def isDeficient(number) {
    sumOfFactors(number) < 2 * number
}

```

<br>

매개변수의 합과 자연수 매개변수들에 대한 캐시를 더했다


버전|결과(작을수록 좋음)
:-|:-:
최적화하지 않은 예 | 577 ms
최적화하지 않은 예(두번째 실행) | 280 ms
합을 캐시한 예 | 600 ms
합을 캐시한 예(두 번째 실행) | 50 ms
전부 다 캐시한 예 | 411 ms
전부 다 캐시한 예(두 번째 실행) | 38 ms


전부 다 캐시한 버전은 첫 시도에서 411 ms 가 걸렸고, 캐시가 꽉 찬 후의 둘째 시도에서는 엄청나게 빠른 38 ms 가 걸렸다. 결과는 매우 좋지만 이 방법은 규모를 늘리기가 어렵다. 다음에 보이는 것처럼 8,000 까지 테스트를 해보면 그 결과는 비참하다.

<br>

> java.lang.OutOfMemoryError: java heap space at java.util.ArrayList<init>(ArrayList.java:112) - 이하 더 비참한 메시지는 생략...

<br>

이 결과에서 볼 수 있듯이, 캐싱코드를 작성하는 개발자는 정확함과 함께 실행 조건도 신경써야 한다. 이것이 '움직이는 부분' 의 적절한 예이다. 코드 내의 상태와 그 의미를 개발자가 항상 관리해야만 한다. 수 많은 언어가 이미 <b>메모이제이션</b> 과 같은 메커니즘을 사용하여 이러한 제약을 극복해냈다.

<br>

### 4.1.2 메모이제이션의 첨가

함수형 프로그래밍은 런타임에 재사용 가능한 메커니즘을 만들어서 움직이는 부분을 최소화하는데 주력한다. 메모이제이션은 프로그래밍 언어에 내장되어 반복되는 함수의 리턴값을 자동으로 캐싱해주는 기능이다. 다시 말하면 위에서 보았던 캐싱을 위해 작성했었던 코드들을 자동으로 제공해준다는 의미이다. 많은 모던랭귀지는 메모이제이션을 지원하는데, 그루비도 예외는 아니다.

그루비에서 함수를 메모아이즈하기 위해서는 함수를 클로저로 정의하고, 리턴값이 캐시되는 함수를 리턴하는 memoize() 메서드를 실행해야 한다.

함수를 메모아이즈하는 것은 <b>메타함수</b> 를 적용하는 것이라고 할 수 있다. 즉 리턴값이 아니라 함수에 어떤 것을 적용하는 것이다. 3장에서 거론했던 커링도 하나의 메타함수 기법이다. 그루비는 Closure 클래스에 메모이제이션을 내장했고 다른 언어들은 각기 다른 방법으로 이것을 구현한다.

```groovy
def static sumOfFactors = sumFactors.memoize()

def static sumFactors = {
    number -> factorsOf(number).inject(0, {i, j -> i + j})
}

def static isFactor(number, potential) {
    number % potential == 0
}

def static factorsOf(number) {
    (1..number).findAll{ i -> isFactor(number, i) }
}

def static isPerfect(number) {
    sumOfFactors(number) == 2 * number
}

def static isAbundant(number) {
    sumOfFactors(number) > 2 * number
}

def static isDeficient(number) {
    sumOfFactors(number) < 2 * number
}
```

위의 예제에서는 sumFactors() 메서드를 코드블록으로 만들었다. 이 함수 레퍼런스의 memoize() 메서드를 호출하면 메모아이즈된 함수가 리턴되는데 이 함수를 sumOfFactors 라는 이름에 할당하였다.

<br>

1 부터 1000 까지의 실행결과

버전 | 결과(작을수록 좋음)
:-|:-:
부분적 메모이제이션 | 228 ms
부분적 메모이제이션(두번째 실행) | 60 ms

부분적으로 메모아이즈된 버전의 두 번째 실행은 수작업으로 합을 캐시한 것과 비슷한 정도로 속도가 빨라졌다. sumFactors() 를 코드블록으로 만들고, sumOfFactors() 는 그 코드블록의 메모아이즈된 인스턴스를 가리키게 바꾼 단 두줄의 변경만으로 말이다.

앞에서 전부 다 캐싱해보았던 것처럼, 재사용할 만한 결과를 전부 캐싱하면 어떨까?

```groovy
def static dividesBy = {
    number, potential -> number % potential == 0
}

def static isFactor = dividesBy.memoize()

def static factorsOf(number) {
    (1..number).findAll{ i -> isFactor.call(number, i) }
}

def static sumFactors = {
    number -> factorsOf(number).inject(0, {i, j -> i + j})
}

def static sumOfFactors = sumFactors.memoize()

def static isPerfect(number) {
    sumOfFactors(number) == 2 * number
}

def static isAbundant(number) {
    sumOfFactors(number) > 2 * number
}

def static isDeficient(number) {
    sumOfFactors(number) < 2 * number
}
```

<br>

1 부터 1000 까지의 실행결과

버전 | 결과(작을수록 좋음)
:-|:-:
부분적 메모이제이션 | 228 ms
부분적 메모이제이션(두번째 실행) | 60 ms
전체 메모이제이션 | 956 ms
전체 메모이제이션 | 19 ms


전부 다 메모아이즈 한 버전은 첫 번째 실행에서는 속도가 줄지만 다음번부터는 항상 더 빠르게 실행된다. 하지만 이것은 적은 수에 한해서만이다. 많은 수에 대해 실행하면 성능이 급격하게 줄어든다. 메모아이즈된 버전은 8,000 개의 수를 실행하다가 메모리가 바닥나고 만다. 명령형 기법이 잘 작동하기 위해서는 안전장치와 함께 실행 조건에 대한 조심스러운 배려가 필요하다. 이것 역시 하나의 움직이는 부분인 셈이다. 메모이제이션을 사용하면 이런 최적화는 함수수준에서 일어난다.

명령형 버전에서는 개발자가 코드를 소유하고 책임을 진다. 함수형 언어는, 가끔 특별한 상황을 위한 변형된 함수나 매개변수를 도입할 때도 있지만, 주로 표준에 맞는 구조에 적합한 일반적인 도구를 만든다. 함수형 언어의 기초적인 요소이기 때문에, 그 레벨에서 최적화가 된 고급기능을 공짜로 얻는 셈이다. 적은 수에 대해 메모아이즈한 버전은 수작업으로 만든 캐싱코드를 성능면에서 가볍게 능가한다. 언어 설계자들은 자기 자신의 규칙도 어길 수 있다. 다시 말해 그들은 개발자들이 접근할 수 없는 부분까지 접근할 수 있기 때문에 아무나 할 수 없는 최적화를 할 수 있다. 따라서 그들이 만든 것보다 더 효율적인 캐시를 만드는 것은 거의 불가능하다. 언어가 캐싱을 더 효율적으로 지원하기도 하지만, 개발자는 그런 책임을 런타임에서 양도하고 더 높은 수준에서 추상화된 문제들에 대해 고민해야 한다.

수작업으로 캐시를 만드는 것은 간단하다. 하지만 이 방법은 코드에 내부 상태를 더하고 복잡하게 만든다. 함수형 언어의 메모이제이션 같은 기능을 사용하면 함수 레벨에서 캐시를 더할 수 있어서, 코드를 거의 바꾸지 않고도 명령형보다 더 좋은 성능을 얻게된다. 함수형 프로그래밍은 움직이는 부분을 제거하여 개발자가 중요한 문제를 푸는데 집중할 수 있게 해준다.

메모아이즈하는 대상의 불변성에 대해 다시 한번 강조해야겠다. 메모아이즈된 함수의 결과가 매개변수 이외의 어떤것에라도 의존하면 기대하는 결과를 항상 얻을 수는 없다. 메모아이즈된 함수에 부수효과가 있다면 캐새된 값이 리턴되어도 그 코드를 믿을 수 없기 때문이다.

tip. 메모아이즈된 함수는
* 사이드 이펙트가 없어야 하고
* 외부 정보에 절대로 의존하지 말아야 한다.

런타임이 갈수록 정교해지고, 사용할 수 있는 자원이 풍부해지면서, 모든 주류 언어에 메모이제이션과 같은 고급 기능들이 일반화되고 있다. 일례로 자바8은 내장된 메모이제이션이 없지만 새로 도입된 람다 기능을 사용하면 쉽게 구현할 수 있다.

<br>

### 4.2 게으름

표현의 평가를 가능한 최대로 늦추는 기법인 게으른 평가는 함수형 프로그래밍 언어에서 많이 볼 수 있는 기능이다. 게으른 컬렉션은 그 요소들을 한꺼번에 미리 연산하는 것이 아니라, 필요에 따라 하나씩 전달해준다. 이렇게 하면 몇 가지 이점이 있다. 우선 시간이 많이 걸리는 연산을 반드시 꼭 필요할 때까지 미룰 수 있게 된다. 둘째로, 요청이 계속되는 한 요소를 계속 전달하는 무한 컬렉션을 만들 수 있다. 셋째로, 맵이나 필터같은 함수형 개념을 게으르게 사용하면 효율이 높은 코드를 만들 수 있다. 자바는 버전 8 이전까지는 게으름을 지원하지 않았지만 몇몇 프레임워크나 파생언어는 지원한다.

```
//의사코드
print length([2+1, 3*2, 1/0, 5-4])
```

이 코드를 실행해보면 프로그래밍 언어가 엄격한지 혹은 게으른지(관대)에 따라 결과가 다를 것이다. 엄격한 언어에서 이 코드를 실행하면(또는 컴파일하면) 세 번째 요소 때문에 DivideByZero 예외가 발생할 것이다. 관대한 언어에서는 결과가 제대로 4로 나올것이다. 어쨌거나 length() 를 호출한 것이기 때문이다.

<br>

#### 4.2.5 게으름의 이점

게으른 목록은 몇가지 이점이 있다. 첫째, 무한수열을 만들 수 있다. 필요할 때까지 값을 평가하지 않아도 되기 때문에, 게으른 컬렉션을 사용하면 무한수열을 모델링 할 수 있다. 둘째, 저장 시 크기가 줄어든다. 컬렉션 전부를 유지하지 않고 순차적으로 다음 값을 유도할 수 있으니 저장소와 실행속도를 맞바꿀 수 있다. 게으른 목록을 사용하고 말고의 결정은 값을 저장하는 것과 새 값을 계산하는 것 사이의 트레이드 오프이다.

게으른 목록의 세번째 이점은 런타임이 좀 더 효율적인 코드를 만들 수 있다는 점이다.

```groovy
static main(args) {
    Example4_20 ex = new Example4_20()

    println(ex.findFirstPalindrome(s1))

    println(ex.findFirstPalindrome(s2))
}

def isPalindrome(s) {
    def sl = s.toLowerCase()
    sl == sl.reverse()
}

def findFirstPalindrome(s) {
    s.tokenize(' ').find{ isPalindrome(it) }
}

def static s1 = "The quick brown fox jumped over anna the dog"

def static s2 = "Bob went to Harrah and gambled with Otto and Steve"
```

isPalindrome() 메서드는 주어진 단어를 소문자로 정규화하고, 그 단어의 글자들을 역순으로 나열해도 원래 단어와 똑같은지를 확인한다. findFirstPalindrome() 은 주어진 문자열에 대해 그루비의 find() 메서드를 사용해 첫번째 회문 단어를 찾으려 시도한다.

아주 긴 문자열에서 첫 번째 팰린드롬을 찾아야 할 경우를 가정해보자. findFirstPalindrome() 메서드를 실행하는 도중에 코드는 이 문자열을 적극적으로 토큰화하여 중간 자료구조를 생성한 후, 거기에 find() 메서드를 적용한다. 그루비의 tokenize() 메서드는 게으르지 않기 때문에 이 경우에는 금방 버려질 큰 임시 자료구조가 만들어질 수도 있다.

스칼라는 약간 다른 방법으로 게으름에 접근한다. 모든 것을 게으르게 만드는 대신, 컬렉션에 게으른 뷰를 제공한다.
```scala
def isPalindrome(s: String) = s == s.reverse

def findPalindrome(s: Seq[String]) = s find isPalindrome

print(findPalindrome(words.view take 1000000))
```

첫 번쨰 팰린드롬을 찾는 것이 목적일 경우 take 메서드로 백만개의 단어를 끄집어내 찾는 것은 아주 비효율적인 일일 것이다. 단어 컬렉션을 게으르게 변환하려면 view 메서드를 사용하면 된다. view 메서드는 컬렉션을 게으르게 순회하게 하여 코드의 효율을 높인다.


<br>


#### 4.2.6 게으른 필드 초기화

게으름에 대해서 끝맺기 전에 두 가지 언어가 비용이 큰 초기화를 게으르게 만드는 데 좋은 기능을 제공한다는 것을 짚고 가자. 스칼라에서는 val 을 선언하는 곳 앞에 lazy 키워드를 사용하면 필드를 적극적 평가에서 적시 평가로 간단히 바꿀 수 있다.

```scala
lazy val x = timeConsumingAndOrSizableComputation()
```

그루비에도 유사한 기능이 있다. 이 기능은 추상구문트리 변형이라는 고급 언어 기능을 사용하며, 사용할 수 있는 변형 중의 하나는 아래 예제에서 볼 수 있는 @Lazy 속성이다.

```groovy
class Person {
    @Lazy pets = ['Cat', 'Dog', 'Bird']
}

def p = new Person()

assert !(p.dump().contains('Cat'))
assert p.pets.size() == 3
assert p.dump().contains('Cat')
```

Person 의 인스턴스 p 는 처음 사용되기 전까지는 Cat 의 값이 정해지지 않는다. 그루비는 클로저 블록을 사용한 자료구조의 값의 초기화를 지원한다.

```groovy
class Person {
    @Lazy List pets = {/* 복잡한 계산 */}()
}
```





<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























