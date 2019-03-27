---
layout: single
categories:
  - Functional Programming
title: Functional Thinking Chapter 5. 진화하라
author: Gold
author-email: pnurep@gmail.com
description: 함수형사고_챕터_5_진화하라
tags:
  - Functional Programming
  - FP
  - Kotlin
  - Scala
  - Haskell
  - Groovy
  - Closure
last_modified_at: 2019-03-27T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# 진화하라

함수형 언어에서의 코드 재사용은 객체지향 언어와는 접근 방법이 다르다. 객체지향 언어는 주로 수많은 자료구조와 거기에 딸린 수많은 연산을 포함한다. 반명에 함수형 언어에서는 적은 수의 자료구조와 많은 연산들이 있기 마련이다. 객체지향 언어는 클래스에 종속된 메서드를 만드는 것을 권장하여 반복되는 패턴을 재사용하려 한다. 함수형 언어는 자료구조에 대해 공통된 변형 연산을 적용하고, 특정 경우에 맞춰서 주어진 함수르 사용하여 작업을 커스터마이즈함으로써 재사용을 권장한다.


<br>


## 5.1 적은 수의 자료구조, 많은 연산자

> 100개의 함수를 하나의 자료구조에 적용하는 것이 10개의 함수를 10개의 자료구조에 적용하는 것보다 낫다. - 앨런펄리스

객체지향적인 명령형 프로그래밍 언어에서 재사용의 단위는 클래스와 그것들이 주고받는 메시지들이다. 이것들은 클래스 다이어그램으로 표시되곤 한다. 이 방면의 중요한 책인 GoF 의 디자인패턴에서는 각 패턴마다 적어도 하나씩의 클래스 도표를 제공한다. OOP 세상에서는 특정한 메서드가 장착된 특정한 자료구조를 개발자가 만들기를 권장한다. 함수형 프로그래밍 언어에서는 이 같은 방식으로 재사용을 하려 하지 않는다. 대신 몇몇 주요 자료구조(list, map, set)와 거기에 따른 최적화된 연산들을 선호한다. 이런 기계장치에 자료구조와 함수를 끼워 넣어서 특정한 목적에 맞게 커스터마이즈하는 것이다. 예를 들면, 앞에서 여러 언어를 통해서 본 filter() 함수는 필터 조건을 결정하는 코드 블록을 플러그인 함수로 받고, 기계장치가 효울적으로 그 조건을 적용하여 필터된 목록을 리턴한다.

함수 수준에서 캡슐화하면 커스텀 클래스 구조를 만드는 것보다 좀 더 세밀하고 기본적인 수준에서 재사용이 가능해진다.

이런 접근 방법의 한 가지 이점이 클로저에서는 이미 나타나고 있다. 일례로 XML 파싱의 경우가 있다. 자바에서는 이 작업을 위한 프레임워크가 수도 없이 있다. 이들 프레임워크에서는 각자의 커스텀한 자료구조와 메서드 형태가 존재한다(ex. SAX 방시식, DOM 방식). 클로저는 XML 을 커스텀 자료구조 대신에 일반 Map 으로 파싱한다. 클로저에는 맵에 적용할 수 있는 도구들이 많이 있어서, 내장된 리스트 컴프리헨션 함수를 사용하면 XPath 스타일로 쿼리하는 것이 아주 쉽다.

```clojure
(use 'clojure.xml)

(def WEATHER-URI "http://weather.yahooapis.com/forecastrss?w=%d&u=f")

(defn get-location [city-code]
    (for [x (xml-seq (parse (format WEATHER-URI city-code)))
    	:when (= :yweather:location (:tag x))]
    (str (:city (:attrs x)) "," (:region (:attrs x)))))

(defn get-temp [city-code]
    (for [x (xml-seq (parse (format WEATHER-URI city-code)))
    	:when (= :yweather:condition (:tag x))]
    (:temp (:attrs x))))

(println "weather for " (get-location 12770744) "is " (get-temp 12770744))
```

위의 예제는 야후의 날시 서비스에서 주어진 도시의 일기예보를 가져온다. 리스트 컴프리헨션 함수인 for 는 파싱한 XML 을 xml-seq 를 사용하여 검색 가능한 맵으로 변형한 후 쿼리할 수 있는 맵 x 에 넣는다. 술어인 :when 은 필터조건으로 사용된다.

<br>

자료구조에서 값을 하나씩 불러오는 문법을 이해하기 위해서는, 그 속에 무엇이 들었는지를 알아야한다. 날씨 서비스에서 리턴한 자료구조를 파싱한 일부는 다음과 같다.

```clojure
({:tag :yweather:condition, :attrs {:text Fair, :code 34, :temp 62, :date Tue,
 04 Dec 2012 9:51 am EST}, :content nil})
```

클로저는 맵을 사용하는데 최적화되어 있기 때문에, 맵에 들어있는 키워드가 바로 함수가 된다. 예제의 (:tag x) 는 'x 라는 맵에 들어 있는 :tag 키에 해당하는 값을 꺼내라' 를 짧게 쓴 것이다.

맵이나 다른 주요 자료구조를 다루는 방법이 아주 다양하다는 점이 클로저를 처음 배울 때 어렵게 느껴지는 이유 중 하나다. 하지만 이것은 클로저가 대부분의 일을 이렇게 최적화된 자료구조로 풀어나가려 시도하기 때문이다. 클로저에서는 특정한 프레임워크에 파싱된 XML 을 끼워넣기보다는, 이미 여러가지 도구들이 존재하는 자료구조로 XML 을 변환하려고 한다.


<br>


## 5.2 문제를 향하여 언어를 구부리기

대부분의 개발자들은 복잡한 비즈니스 문제를 자바와 같은 언어로 번역하는 것이 그들의 할 일 이라는 착각속에서 일을 한다. 자바가 언어로서 유연하지 못하기 때문에, 아이디어를 기존의 고정된 구조에 맞게 주물러야 하기 때문이다. 그런 개발자가 유연한 언어를 접하면 문제를 언어에 맞게 구부리는 대신 언어를 문제에 어울리게 구부릴 수 있다는 것을 깨닫게 된다. 루비 같은 언어는 DSL 을 주류 언어들보다 잘 지원하기 때문에 이런 것이 가능하다. 현대적 함수형 언어들은 좀 더 진보했다. 스칼라는 내부 DSL 을 지원하기 위해서 설계된 언어이고, 클로저와 같은 모든 리스프 계열 언어들은 개발자가 문제에 맞게 언어를 바꾸는 유연성 면에서 어떤 언어 못지않다. 아래예제는 스칼라의 XML 기능을 사용해서 이전의 날씨문제를 구현해 보았다.

```scala
val theUrl = "https://samples.openweathermap.org/data/2.5/weather?q=London&mode=xml&appid=b6907d289e10d714a6e88b30761fae22"

val xmlString = Source.fromURL(new URL(theUrl)).mkString
val xml = XML.loadString(xmlString)

val city_id = xml \\ "current" \\ "city" \\ "@id"
val city_name = xml \\ "current" \\ "city" \\ "@name"
val country = xml \\ "current" \\ "city" \\ "country"

println(city_id + ", " + city_name + ", " + country.text)
//2643743, London, GB
```

스칼라는 가단성을 위해 설계되었기 때문에 연산자 오버로딩이나 묵시적 자료형 같은 확장이 가능하다. 위의 예제에서는 스칼라를 확장해서 \\ 라는 연산자를 사용하여 XPath 방식의 쿼리를 사용할 수 있었다. 특별히 함수형 언어만의 기능은 아니지만, 언어를 우아하게 문제 도메인으로 바꾸는 기능은 함수형, 선언형 방식의 현대언어에서 흔히 볼 수 있다.


<br>


## 5.3 디스패치 다시 생각하기

이전에 대안적 디스패치 방식의 한 예로 스칼라의 패턴매칭을 살펴봤다. 여기서 대스패치란 넓은 의미로 언어가 작동방식을 동적으로 선택하는 것을 말한다. 이 절에서는 여러 함수형 JVM 언어들의 디스패치 방식이 어떻게 자바보다 간결함과 유연성을 가능하게 하는지를 더 살펴보자.


### 5.3.1 그루비로 디스패치 개선하기

자바에서 조건부 실행은 특별한 경우의 switch 문을 제외하고는 if 문을 사용하게 된다. if 문이 길게 연결되면 가독성이 떨어지기 때문에 자바 개발자들은 주로 GoF 의 팩토리나 추상 팩토리 패턴을 사용한다. 좀 더 유연하게 결정을 표현할 수 있는 언어를 사용하면 이런 패턴들을 사용하지 않고도 간결하게 코드를 짤 수 있다.

그루비에는 자바의 switch 문과 비슷하지만 다르게 실행되는 강력한 switch 문이 있다.

```groovy
static def gradeFromScore(score) {
    switch (score) {
        case 90..100 : return "A"
        case 80..<90 : return "B"
        case 70..<80 : return "C"
        case 60..<70 : return "D"
        case 0..<60 : return "F"
        case ~"[ABCDFabcdf]" : return score.toUpperCase()
        default: throw new IllegalArgumentException("Invalid score : ${score}")
    }
}
```

위의 코드는 점수를 받아서 그에 해당하는 학점을 리턴한다. 라바와는 달리, 그루비의 switch 문은 여러가지 동적자료형을 받을 수 있다. 매개변수 score 는 0 에서 100 까지의 수나 학점이 될 수 있다. 자바처럼 fall-through 형식을 따라 각 경우를 return 이나 break 로 마쳐야 한다. 하지만 자바와는 다르게 그루비에서는 범위(90..100), 열린범위(80..\<90), 정규식(~"[ABCDFabcdf]"), 디폴트 조건을 모두 사용할 수 있다. 그루비는 동적자료형을 사용하므로 매개변수로 다른 자료형을 넣어서 각각 그에 맞게 반응하게 하는 것이 가능하다.

이렇게 강력한 switch 문은 if 문의 연속 사용과 팩토리 패턴의 중간 정도로 생각하고 간편하게 사용할 수 있다. 그루비의 switch 문은 범위나 다른 복합 자료형을 사용할 수 있다는 면에서 스칼라의 패턴매칭과 유사하게 사용된다.


<br>


## 5.4 연산자 오버로딩

함수형 언어의 공통적인 기능은 연산자 오버로딩이다. 이것은 +, -, * 와 같은 연산자를 새로 정의하여 새로운 자료형에 적용하고 새로운 행동을 하게 하는 기능이다. 자바가 처음 만들어질 때는 의도적으로 연산자 오버로딩이 제외되었지만, 자바의 후계 언어들을 비롯해 거의 모든 현대 언어들에는 이 기능이 들어있다.

### 5.4.2 스칼라

스칼라는 연산자와 메서드의 차이점을 없애는 방법으로 연산자 오버로딩을 혀용한다. 즉 연산자는 특별한 이름을 가진 메서드에 불과하다. 따라서 곱셈 연산자를 스칼라에서 오버라이드하려면 * 메서드를 오버라이드 하면 된다.

```scala
final class Complex(val real: Int, val imaginary: Int) extends Ordered[Complex] {

    def +(operand: Complex) =
        new Complex(real + operand.real, imaginary + operand.imaginary)

    def +(operand: Int) =
        new Complex(real + operand, imaginary)

    def -(operand: Complex) =
        new Complex(real - operand.real, imaginary - operand.imaginary)

    def -(operand: Int) =
        new Complex(real - operand, imaginary)

    def *(operand: Complex) =
        new Complex(real * operand.real - imaginary * operand.imaginary,
            real * operand.imaginary + imaginary * operand.real)

    /* ... */

    override def compare(that: Complex): Int = ???
}

```


자바 언어 설계자들은 연산자 오버로딩을 지원하지 않기로 정했다. C++ 를 너무 복잡하게 만들었던 경험 때문이었다. 대부분의 현대 언어들은 복잡한 정의들을 제거했지만, 아직도 그런 복잡한 것의 남용을 예방하려는 의도를 여기저기에서 찾아볼 수 있다.


<br>


## 5.5 함수형 자료구조

자바에서는 언어 자체의 예외 생성 및 전파기능을 사용하는 전통적인 방법으로 오류를 처리한다. 만약 구조적인 예외처리 기능이 존재하지 않는다면 어떻게 될까? 대부분의 함수형 언어들은 예외 패러다임을 지원하지 않기 때문에 개발자는 다른 방법으로 오류조건을 표현해야 한다.

예외는 많은 함수형 언어가 준수하는 전제 몇 가지를 깨뜨린다. 첫째, 함수형 언어는 부수효과가 없는 순수함수를 선호한다. 그런데 예외를 발생시키는 것은 예외적인 프로그램 흐름을 야기하는 부수효과다. 함수형 언어들은 주로 값을 처리하기 때문에 프로그램의 흐름을 막기보다는 오류를 나타내는 리턴 값에 반응하는 것을 선호한다.

함수형 프로그램이 선호하는 또 하나의 특성은 <b>참조투명성</b>이다. 호출하는 입장에서는 단순한 값 하나를 사용하든, 하나의 값을 리턴하는 함수를 사용하든 다를 바가 없어야 한다. 만약 호출된 함수에서 예외가 발생할 수 있다면, 호출하는 입장에서는 안전하게 값을 함수로 대체할 수 없을 것이다.

이 절에서는 자바의 일반적인 예외 전파 방식을 우회하는 타입테이프 오류 처리 방식을 살펴본다.


### 5.5.1 함수형 오류 처리

자바에서 예외를 사용하지 않고 오류를 처리하기 위해 해결해야 할 근본적인 문제는 메서드가 하나의 값만 리턴할 수 있다는 제약이다. 물론 메서드는 여러 개의 값을 지닌 하나의 Object 나 그 서브클래스의 레퍼런스를 리턴 할 수 있다. 따라서 Map 을 사용하여 다수의 리턴 값을 지원하게 할 수 있다.

```java
public static Map<String, Object> divide(int x, int y) {
    Map<String, Object> result = new HashMap<\>();
    if (y == 0)
        result.put("exception", new Exception("div by zero"));
    else
        result.put("answer", ((double) (x / y)));

    return result;
}
```

위의 예제에서는 String 을 키로, Object 를 값으로 한 Map 을 만들었다. divide() 메서드는 실패를 "exception" 으로 표시하고, 성공은 "answer" 로 표시한다. 두 방식 모두를 테스트 해보면,

```java
@Test
public void maps_success() {
    Map<String, Object> result = Example5_14.divide(4, 2);
    assertThat(((Double) result.get("answer"))).isEqualTo(2.0);
}

@Test
public void maps_failure() {
    Map<String, Object> result = Example5_14.divide(4, 0);
    assertThat(((Exception) result.get("exception")).getMessage())
    .isEqualTo("div by zero");
}
```

위의 예제에서 map_success 테스트는 리턴된 Map 에 값이 제대로 들어 있음을 확인해본다. map_failure 테스트는 예외 경우를 확인한다.

이 접근 방법에는 문제점이 있다. 첫째, Map 에 들어가는 값은 타입세이프 하지 않기 때문에 컴파일러가 오류를 잡아낼 수 없다. 열거형을 키로 사용하면 조금 좋아지기는 하겠지만, 근본적인 해결책은 아니다. 둘째, 메서드 호출자는 리턴 값을 가능한 결과들과 비교해보기 전까지는 성패를 알 수 없다. 셋째, 두 가지 결과가 모두 리턴 Map 에 존재할 수가 있으므로, 그 경우에는 결과가 모호해진다.

여기서 필요한 것은 타입세이프하게 둘 또는 더 많은 값을 리턴할 수 있게 해주는 메커니즘이다.


### 5.5.2 Either 클래스

함수형 언어에서는 다른 두 값을 리턴해야 하는 경우가 종종 있는데 그런 행동을 모델링하는 자료구조가 Either 클래스이다. Either 는 왼쪽 또는 오른쪽 값 하나만 가질 수 있게 설계되었다. 이런 자료구조를 <b>분리합집합</b>이라고 한다. C 에서 파생된 어떤 언어들은 여러 자료형의 인스턴스를 지닐 수 있는 union 자료형을 제공한다. 분리합집합은 두 자료형이 들어갈 자리가 있지만, 둘 중 하나만 지닐 수 있다.

스칼라에는 아래에서 보듯이 Either 가 포함되어 있다.

```scala
type Error = String
type Success = String
def call(url: String):Either[Error, Success] = {
    val response = WS.url(url).get.value.get
    if (valid(response))
        Right(response.body)
    else
        Left("Invalid response")
}
```

Either 는 주로 오류처리에서 사용된다. Either 는 스칼라의 전체 생태계에 자연스럽게 녹아든다. 자주 사용하는 경우 중의 하나는 Either 의 인스턴스에 패턴매칭을 적용하는 것이다.

```scala
getcontent(new URL("...")) match {
	case Left(msg) => println(msg)
    case Right(source) =>source.getLines.foreach(println)
}
```

자바에 내장되지는 않았지만, 제네릭을 사용하면 간단한 대용품 Either 클래스를 만들 수 있다.

```java
public class Either<A,B> {
    private A left = null;
    private B right = null;

    private Either(A a,B b) {
        left = a;
        right = b;
    }

    public static <A,B> Either<A,B> left(A a) {
        return new Either<A,B>(a,null);
    }

    public A left() {
        return left;
    }

    public boolean isLeft() {
        return left != null;
    }

    public boolean isRight() {
        return right != null;
    }

    public B right() {
        return right;
    }

    public static <A,B> Either<A,B> right(B b) {
        return new Either<A,B>(null,b);
    }

    public void fold(F<A> leftOption, F<B> rightOption) {
        if(right == null)
            leftOption.f(left);
        else
            rightOption.f(right);
    }
}
```


위의 Either 클래스는 생성자가 private 이기 때문에 실제생성은 정적 메서드인 left(A a) 와 right(B b) 가 담당한다. 이 클래스의 메서드들은 클래스 멤버들을 조사하고 꺼내오는 도우미들이다.

Either 를 사용하면 타입 세이프티를 유지하면서 예외 또는 제대로 된 결과 값을 리턴하는 코드를 만들 수 있다. 함수형의 보편적인 관례에 따라 Either 클래스의 <b>왼쪽</b>이 예외, <b>오른쪽</b>이 결과 값이다.

```java
//로마숫자 파싱하기
public static Either<Exception, Integer> parseNumber(String s) {
    if (!s.matches("[IVXLXCDM]+"))
        return Either.left(new Exception("Invalid Roman numeral!"));
    else
        return Either.right(/* 변환된 숫자 */);
}
```

parseNumber() 메서드는 오류 조건이 발견되면 그것을 Either의 left에 저장하고 결과를 성공적으로 얻을 수 있으면 결과를 right로 저장한다.

이것은 Map 자료구조를 주고받던 것에 비하면 큰 발전이다. 우선 예외를 얼마든지 세부화 할 수 있으면서도 타입세이프티를 지킬 수 있다. 또한 제네릭을 통한 메서드 선언으로 오류를 분명하게 알 수 있다. 메서드의 결과는 예외든 답이든 Either 의 값을 푸는 한 단계의 우회만 거치면 얻어진다. 마지막으로, 이런 우회 덕분에 게으름을 구현하는 것이 가능해진다.

<br>

#### 게으른 파싱과 함수형 자바

Either 클래스는 함수형 알고리즘에 자주 사용되며, 따라서 함수형 자바 프레임워크에서는 Either 클래스를 자체적으로 구현하고 있다. 하지만 다른 함수형 자바 구조물과 같이 사용될 수 있게 만들어져있다. 따라서 Either 와 함수형 자바의 P1 클래스를 조합해서 사용하면 게으른 오류 평가를 구현할 수 있다.

함수형 자바에서 P1 클래스는 매개변수가 없는 _1() 란 간단한 메서드의 래퍼이다. P1 은 함수형 자바에서 코드블록을 실행하지 않고 여기저기 전해어서 원하는 컨텍스트에서 실행하게 해주는, 일종의 고차함수다.

자바에서 예외는 던지는 순간 그 객체가 만들어진다. 게으른 평가 메서드를 리턴하면 예외의 생성을 지연할 수 있다.

```java
//함수형 자바를 사용하여 게으른 파서 생성하기
public static P1<Either<Exception, Integer>> parseNumber(final String s) {
    if (!s.matches("[IVXLXCDM+]")) {
        return new P1<Either<Exception, Integer>>() {
            @Override
            public Either<Exception, Integer> _1() {
                return Either.left(new Exception("Invalid!"));
            }
        };
    } else {
        return new P1<Either<Exception, Integer>>() {
            @Override
            public Either<Exception, Integer> _1() {
                return Either.right(1);
            }
        };
    }
}
```

```java
//함수형 자바의 게으른 파서 테스트
@Test
public void parse_lazy() {
    P1<Either<Exception, Integer>> result =
            RomanNumeralParser.parseNumberLazy("XLII");
    assertThat(result._1().right().intValue()).isEqualTo(42);
}

@Test
public void parse_lazy_exception() {
    P1<Either<Exception, Integer>> result =
            RomanNumeralParser.parseNumberLazy("FOO");
    assertThat(result._1().isLeft()).isEqualTo(true);
    assertThat(result._1().left().getMessage()).isEqualTo(INVALID_ROMAN_NUMERAL);
}
```

parse_lazy 테스트에서는 반드시 _1() 을 호출해서 결과를 풀어야 한다. 이 결과는 Either 의 <b>오른쪽</b> 값이고 거기서 리턴 값을 구할 수 있다. parse_lazy_exception 테스트는 <b>왼쪽</b> 값을 확인하고 풀어서 예외의 메시지를 알아낼 수 있다.

예외와 그 스택 트레이스는 나중에 Either 의 왼쪽 값을 _1() 를 사용하여 풀기 전에 생성되지 않는다. 이런 게으른 예외는 생성자의 실행을 지연하게 해준다.

<br>

#### 디폴트 값을 제공하기

Either 를 예외 처리에 사용하여 얻는 이점은 게으름만이 아니다. 디폴트 값을 제공한다는 것이 다른 이점이다.

```java
//적당한 디폴트 리턴 값 제공하기
private static final int MIN = 0;
private static final int MAX = 1000;

public static Either<Exception, Integer> parseNumberDefaults(final String s) {
    if (!s.matches("[IVXLXCDM+]")) {
        return Either.left(new Exception("Invalid Roman numeral"));
    } else {
        int number = new RomanNumeral(s).toInt();
        return Either.right(new RomanNumeral(number >= MAX ? MAX : number).toInt());
    }
}
```

이에 대한 테스트로

```java
@Test
public void parse_defaults_normal() {
    Either<Exception, Integer> result =
            RomanNumeralParser.parseNumberDefaults("XLII");

    assertEquals(42, result.right().intValue());
}

@Test
public void parse_defaults_triggered() {
    Either<Exception, Integer> result =
            RomanNumeralParser.parseNumberDefaults("MM");
    assertEquals(1000, result.right().intValue());
}
```

이전의 구현에서 MAX 보다 큰 로마숫자를 허용하지 않는다고 가정했고, 더 큰 숫자를 지정하려고 하면 디폴트로 MAX 값을 가지게 했다. parseNumberDefaults() 에 의해 Either 의 오른쪽에 디폴트 값이 들어간다.

<br>

#### 예외 조건을 래핑하기

Either 를 사용하면 예외를 래핑하여 구조적인 예외 처리를 함수형으로 변환할 수도 있다.

```java
// 다른 곳에서 던진 예외를 처리하기
public static Either<Exception, Integer> divide(int x, int y) {
    try {
        return Either.right(x / y);
    } catch (Exception e) {
        return Either.left(e);
    }
}
```

예외를 래핑하는 경우의 테스트는 아래와 같다.

```java
@Test
public void catching_other_people_exceptions() {
    Either<Exception, Integer> result = Example5_26.divide(4, 2);
    assertEquals(((long) 2), ((long) result.right().intValue()));

    Either<Exception, Integer> fail = Example5_26.divide(4, 0);
    assertEquals("/ by zero", fail.left().getMessage());
}
```

위의 나눗셈에서는 ArithmeticException 이 야기될 가능성이 있다. 예외가 생기면 Either 의 왼쪽에 넣고, 그렇지 않으면 오른쪽 값으로 결과를 리턴한다. Either 를 사용하면 Checked Exception 을 포함한 모든 예외들을 함수형으로 바꿀수 있다.


```java
// 게으른 예외 처리
public static P1<Either<Exception, Integer>> divideLazily(final int x, final int y) {
    return new P1<Either<Exception, Integer>>() {
        @Override
        public Either<Exception, Integer> _1() {
            try {
                return Either.right(x / y);
            } catch (final Exception e) {
                return Either.left(e);
            }
        }
    };
}
```

아래는 게으른 예외처리의 테스트를 보여준다.

```java
@Test
public void lazily_catching_other_people_exceptions() {
    P1<Either<Exception, Integer>> result = Example5_28.divideLazily(4, 2);
    assertEquals(2, result._1().right().intValue());

    P1<Either<Exception, Integer>> fail = Example5_28.divideLazily(4, 0);
    assertEquals("/ by zero", fail._1().left().getMessage());
}
```

자바는 이런 개념을 근본적으로 포함하지 않기 때문에 Either 클래스를 모델링하기가 번거롭다. 그래서 제네릭과 클래스를 자용하여 억지로 구현해야 했다. 스칼라와 같은 언어는 Either 나 그와 유사한 구조물이 내장되어 있다. 클로저나 그루비는 리턴 값을 쉽게 생성할 수 있는 동적 타이핑 언어이기 때문에 Either 와 같은 것을 굳이 기본적으로 포함하지 않아도 된다.


<br>


### 5.5.3 옵션 클래스

Either 는 두 부분을 가진 값을 간편하게 리턴할 수 있는 개념이다. 이것은 이 장의 뒤에서 볼 수 있듯이, 트리 모양 자료구조와 같은 일반적인 자료구조를 만드는 데 유용하다. 여러 언어나 프레임워크에는 함수에서 리턴할 때 사용할 수 있는 Either 와 유사한 Option 이란 클래스가 있다. 이것은 적당한 값이 존재하지 않을 경우를 의미하는 none, 성공적인 리턴을 의미하는 some 을 사용하여 예외조건을 더 쉽게 표현한다.

```java
public static Option divide(double x, double y) {
    if (y == 0) {
        return Option.none();
    } else {
        return Option.some(x / y);
    }
}
```

아래는 위의 테스트이다.

```java
@Test
public void option_test_success() {
    Option result = Example5_30.divide(4.0, 2);
    assertEquals(2.0, ((Double) result.some()), 0.1);
}

@Test
public void option_test_failure() {
    Option result = Example5_30.divide(4.0, 0);
    assertEquals(Option.none(), result);
}
```

Option 은 Either 의 left, right 와 유사하지만 적당한 리턴값이 없을 수 있는 메서드를 위해 none() 과 some() 을 가지고 있다. Option 클래스는 Either 의 간단한 부분집합이라고 볼 수 있다. Either 어떤 값이든 저장할 수 있는 반면, Option 은 주로 성공과 실패의 값을 저장하는 데 쓰인다.


### 5.5.4 Either 트리와 패턴 매칭

트리 모양의 구조물을 만들면서 Either 를 좀 더 살펴보자. 이렇게 하여 스칼라의 패턴 매칭과 유사한 순회 메서드를 만들 것이다. 자바에서 제네릭을 사용하면 Either 는 왼쪽과 오른쪽에 각각 다른 자료형을 받는 하나의 자료구조가 된다. 앞에서 사용했던 Either 는 Exception(왼쪽) 또는 Interger(오른쪽) 를 포함한다.

이 자료구조를 사용하여 트리모양의 구조를 만들어 볼 것이다. 하지만 Either 같은 자료구조의 이점을 알기 위해서는 먼저 패턴매칭에 대해 다뤄야 한다.


<br>


#### 스칼라 패턴매칭

스칼라의 훌륭한 기능 중의 하나는 디스패치에 패턴 매칭을 사용할 수 있다는 것이다. 아래 함수를 통해 살펴보자. 이 함수는 점수를 학점으로 환산한다.

```scala
val VALID_GRADES = Set("A", "B", "C", "D", "F")

def letterGrade(value: Any): String = value match {
    case x: Int if (90 to 100).contains(x) => "A"
    case x: Int if (80 to 90).contains(x) => "B"
    case x: Int if (70 to 80).contains(x) => "C"
    case x: Int if (60 to 70).contains(x) => "D"
    case x: Int if (0 to 60).contains(x) => "F"
    case x: String if VALID_GRADES(x.toUpperCase) => x.toUpperCase
}
```

위의 letterGrade() 함수 전체가 주어진 값에 대한 match 이다. 각각 선택된 값에 대해, 패턴 방호 조건이 매개변수의 자료형 및 주어진 조건에 매칭되는 값을 얻게 해준다. 이런 문법의 장점은 번거롭게 if 문을 연달아 사용하는 것보다 깔끔하게 선택조건을 구분하게 해준다는 것이다.

패턴매칭은 스칼라의 <b>케이스 클래스</b>와 같이 사용된다. 케이스 클래스는 패턴매칭과 함께 순차적인 결정과정을 제거해주는 특성을 가진 클래스이다.

```scala
class Color(val red: Int, val green: Int, val blue: Int)

case class Red(r: Int) extends Color(r, 0, 0)

case class Green(g: Int) extends Color(0, g, 0)

case class Blue(b: Int) extends Color(0, 0, b)

def printColor(c: Color) = c match {
    case Red(v) => println("Red: " + v)
    case Green(v) => println("Green: " + v)
    case Blue(v) => println("Blue: " + v)
    case col: Color => {
        print("R: " + col.red + ", ")
        print("G: " + col.green + ", ")
        print("B: " + col.blue)
    }
    case null => println("invalid color")
}
```

위의 예제에서는 먼저 기본 Color 를 만들고, 단색 버전들을 케이스 클래스로 만들었다. 어떤 색이 함수에 넘겨졌는지를 알기 위해 match 를 사용하여 가능한 모든 값에 대해 패턴매칭을 시도한다. 마지막 경우는 null 을 처리한다.


스칼라의 케이스 클래스

객체지향 시스템, 특히 다른 시스템과 정보를 교환해야 하는 시스템들에서는 자료만 가진 간단한 클래스들이 많이 사용된다. 이런 클래스들이 많아지기 때문에 스칼라는 케이스 클래스란것을 만들게 되었다. 케이스 클래스는 다음과 같은 구문적 장점이 편의를 제공한다.

* 클래스 이름을 사용한 팩토리 클래스를 만들 수 있다. 예를 들어 new 키워드를 사용하지 않고도 새 인스턴스를 만들 수 있다. val bob = Person("Bob", 42)
* 클래스의 모든 변수들이 자동적으로 val 이 된다. 다시 말하면 불변형 내부 변수로 유지된다.
* 컴파일러가 적당한 디폴트 equals(), hashcode(), toString(), 메서드를 자동으로 생성해 준다.
* 컴파일러가 불변 객체를 지원하기 위하여, 새로운 복제 객체를 리턴하는 copy() 메서드를 자동으로 생성해준다.

케이스 클래스는, 자바의 후계자들이 단지 구문적인 문제점만 고치지 않고, 현대식 소프트웨어가 어떻게 작동하는지를 이해하고 거기에 알맞게 기능을 변화해나간다는 사실을 보여주는 좋은 예이다. 또한 언어가 시간이 지남에 따라 진화한다는 좋은 예이기도 하다.


#### Either 트리

추상화된 트리 | 설명
-|:-
empty | 셀에 아무 값도 없음
leaf | 셀에 특정 자료형의 값이 들어있음
node | 다른 leaf 나 node 를 가리킴


Either 의 추상개념은 원하는 갯수만큼 슬롯을 확장할 수 있다. 일례로 Either<Empty, Either<Leaf, Node>> 처럼 선언할 수도 있다. 여기서 Either는 제일 왼쪽에는 Empty, 가운데는 Leaf, 제일 오른쪽에는 Node를 가지는 관례를 따른다. 재귀 함수를 이용해 각 트리를 순회하여 패턴 매칭에 사용될 수 있다.



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}
