---
layout: single
categories:
  - Functional Programming
title: Functional Thinking Chapter 3. 양도하라
author: Gold
author-email: pnurep@gmail.com
description: 함수형사고_챕터_3_양도하라
tags:
  - Functional Programming
  - FP
  - Kotlin
  - Scala
  - Haskell
  - Groovy
  - Closure
last_modified_at: 2019-03-25T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# 양도하라

고백, 나는 절대로 가비지 컬렉션 없는 언어로 일하고 싶지 않다. c++ 같은 언어로 수년간 고생을 할 만큼 했기 때문에 현대 언어의 편리함을 포기할 수 없다. 소프트웨어는 이렇게 발전한다. 우리는 지루한 세부사항을 처리하거나 숨기기 위해서 추상화의 단계를 만든다. 컴퓨터의 성능이 향상됨에 따라서 우리는 더 많은 일들을 언어나 런타임에 떠넘겨왔다. 갭라자들은 느리다는 이유로 인터프리터 언어를 피해왔지만, 이젠 그것들이 어디에서나 사용된다. 함수형 언어의 기능들은 10년전만 해도 사용할 수 없을 만큼 느렸지만, 지금은 그것들이 개발자의 시간과 노력을 최적화 할 수 있게 해주므로 충분히 사용할 만 하다.

가비지 컬렉션 같은 저수준 세부사항의 조작을 런타임에 양도함으로써 찾아야 할 수 많은 오류를 방지해주는 능력이야말로 함수형 사고의 가치라고 하겠다. 대다수의 개발자들은 메모리와 같은 기본 추상적 개념을 문제없이 무시하는 데 익숙하겠지만, 더 놓은 단계에서 나타나는 추상화는 낯설어한다. 하지만 이런 고수준 추상개념들도 기계장치의 지루한 일들을 처리해줌으로써 개발자가 자신의 문제에 고유한 측면을 연구할 시간을 제공한다는 측면에서 똑같은 역할을 수행한다.

이 장에서는 함수형 언어롤 사용하는 개발자가 언어나 런타임에 제어를 양도하는 다섯가지 방식을 살펴보겠다. 이를 통해 개발자는 좀 더 중요한 문제를 풀 시간을 얻을 수 있을것이다.



## 3.1 반복처리에서 고계함수로

이전의 예제에서 반복처리 대신에 map 과 같은 함수를 사용하여 제어를 포기하는 방법을 보여주었다. 여기서 무엇이 양도되는지는 확실하다. 고차함수 내에서 어떤 연산을 할 것인지를 표현하기만 하면, 언어가 그것을 능률적으로 처리할 것이다. 게다가 par 라는 변형자(modifier)를 덧붙이기만 하면 분산처리까지 해준다.

멀티스레드 코드는 짜기도 어렵고 디버그하기도 여려워서 오류가 많이 난다. 스레드를 관리하는 골치아픈 일들을 도우미에게 떠넘기면 개발자는 밑에 깔린 배관작업에 신경을 덜 써도 된다. 그렇다고 개발자가 저수준 추상단계에서 코드가 어떻게 동작하는지 이해하는 것 까지 전부 떠넘겨도 된다는 것은 아니다. 많은 경우에 개발자는 Stream 같은 추상 개념을 사용할때 거기에 함축된 의미를 반드시 알아야 한다. 일례로 많은 개발자들은 자바 8의 Stream API 를 사용할때조차 그 안에서 작동하는 포크/조인 라이브러리의 세부사항을 이해해야 좋은 성능을 낼 수 있다는 사실에 놀라곤 한다. 하지만 일단 이해만 하면 간단하게 사용할 수 있다.

tip. 사용하는 추상화 단계보다 한 단계 아래를 이해하라.

프로그래머들은 효율적으로 일하기 위하여 추상단계에 의존한다. 하드디스크에서 0과 1을 직접 조작해서 컴퓨터 프로그램을 만드는 사람은 없다. 추상화는 낮은 단계의 지저분한 것들을 지워주지만 종종 중요한 사실도 감출 수 있다. 이 문제는 8장에서 깊이 다루기로 하자.



## 3.2 클로저

모든 함수형 언어는 클로저를 포함한다. 하지만 이 언어 기능은 종종 신비에 싸인 것처럼 언급되곤 한다. 클로저(Closure)란 그 내부에서 참조되는 모든 인수에 대한 묵시적 바인딩을 지닌 함수를 지칭한다. 다시 말하면 이 함수(또는 메서드)는 자신이 참조하는 것들의 문맥(Context)을 포함한다. 그루비를 사용한 클로저 블록 바인딩 예제를 살펴보자.

```groovy
class Employee {
    def name, salary
}

def paidMore(amount) {
    return {Employee e -> e.salary > amount}
}

def isHighPaid = paidMore(100000)
```

위 예제에서는 두 개의 필드가 있는 Employee 클래스가 정의된다. 그리고 amount 매개변수를 받는 paidMore 함수가 정의된다. 이 함수의 리턴 값은 클로저라는 코드 블록이다. 이 함수는 Employee 객체의 인스턴스를 받는다. 이 코드블록에 100000을 매개변수로 주고 isHighPaid 란 인수에 할당한다. 이렇게 하면 100000의 값은 이 코드블록에 영원히 바인딩 된다. 추후에 이 코드블록으로 직원을 평가할 때는 그들의 연봉을 영원히 바인딩 된 그 값을 가지고 저울질하게 된다.

```groovy
def smithers = new Employee(closureGroovyExample, "Fred", 120000)
def homer = new Employee(closureGroovyExample, "Homer", 80000)
println closureGroovyExample.isHighPaid(smithers)
println closureGroovyExample.isHighPaid(homer)
//true, false
```

위의 예제는 두 명의 직원을 생성해서 그들의 연봉이 조건에 맞는지를 판별한다. 클로저가 생성될때에, 이 코드 블록의 스코프에 포함된 인수들을 둘러싼 상자가 같이 만들어진다. 그래서 이름도 클로저라 지어졌다. 이 코드 블록의 모든 인스턴스는 전용 인수조차도 각자 고유한 값을 가진다. 아래 예제처럼 다른 값을 바인딩해서 paidMore 클로저를 하나 더 만들수도 있다.

```groovy
def isHigherPaid = paidMore(200000)
println isHigherPaid(smithers)
println isHigherPaid(homer)
def Burns = new Employee(closureGroovyExample, "Monty", 1000000)
println isHigherPaid(Burns)
//false, false, true
```

클로저는 함수형 언어나 프레임워크에서 코드블록을 다양한 상황에서 실행하게 해주는 메커니즘으로 많이 쓰인다. 클로저를 map() 과 같은 고차함수에 자료변형 코드블록으로 전달하는 것이 대표적인 예이다. 함수형 자바에서는 익명 내부 클래스를 사용하여 '진정한' 클로저의 기능을 흉내내지만, 자바8 이전의 자바는 클로저를 지원하지 않았기 때문에 완벽하지는 않다. '완병하지 않다'는 것이 무슨 뜻인가?

아래 예제를 보면 클로저가 왜 특별한지를 알 수 있다.

```groovy
static def Closure makeCounter() {
    def local_variable = 0
    return { return local_variable += 1 }	//1
}

static def c1 = makeCounter()	//2

c1()	//3
c1()
c1()

static def c2 = makeCounter()	//4

println "C1 = ${c1()}, C2 = ${c2()}"
// C1 = 4, C2 = 1
```

1. 이 함수의 리턴값은 값이 아니라 코드블록이다.
2. c1 은 이제 이 코드블록의 인스턴스를 가리킨다.
3. c1 을 불러오면 내부의 인수가 증가한다. 이때 이 표현을 평가하면 1이 나온다.
4. c2 는 makeCounter() 의 새로운 인스턴스를 가리킨다.
5. 각각의 인스턴스는 local_variable 에 다른 내부상태를 지니고 있다.

여기서 makeCounter() 함수는 우선 적합한 이름을 가진 지역인수를 정하고, 그 인수를 사용하는 코드블록을 리턴한다. 이 makeCounter() 함수의 리턴자료형은 값이 아니라 클로저이다. 이 코드블록은 단지 내부 인수를 증가시키키만 한다. 두 번의 명시적인 리턴은 그루비에서는 불필요한 것이지만, 그것들이 없으면 코드가 더욱 읽기 어려울 것이다.

이 makeCounter() 함수를 c1 이란 인수에 설정해주고 세 번 불러온다. 여기서도 c1.call() 대신에 단순히 코드블록 인수옆에 괄호만 붙이는 그루비의 간단한 구문을 사용하였다. 다음으로 이 코드블록의 새 인스턴스를 c2 에 설정하고, c2 를 통해 makeCounter() 를 다시 실행한다. 마지막으로, c1 과 c2 를 같이 실행한다. 여기서 두 개의 코드 블록이 각각의 local_variable 값을 유지하는 것을 주의해서 보자. 클로저란 단어의 어원이 문맥을 포괄함(enclosing context) 이란 점에서 이 작업의 내용을 추측할 수 있을것이다. 이 지역인수는 함수내부에서 정의되었지만, 코드블록이 이 인수에 바인딩 되어있고, 따라서 코드블록이 존재하는 동안에 이 인수값은 유지되어야 한다.

구현 관점에서 보면, 클로저 인스턴스는 생성될 때 스코프 내에 있던 모든 것을 캡슐화하여 유지한다. 클로저 인스턴스가 가비지 컬렉션으로 사라지면 참조되었던 것들 모두가 같이 없어진다.

이 예제에서는 클로저 바인딩의 내부 원리를 설명하기 위해서 그 내부 상태를 외부에서 조작했지만, 단지 그러기 위해서 클로저를 만드는 것은 좋은 생각이 아니다. 상수와 같이 변하지 않는 값을 바인딩하는 것이 보편적이다.

자바8 이전이나, 함수는 있지만 클로저를 지원하지 않는 다른 언어를 사요ㅗㅇ해서 이와 같은 기능을 가장 가깝게 구현할 수 있는 법을 아래에서 볼 수 있다.

```java

public static void main(String[] args) {
    Counter counter = Counter.makeCounter();

    counter.execute();
    counter.execute();
    counter.execute();

    System.out.println(counter.execute());
}

class Counter {

    public int varField;

    Counter(int var) {
        this.varField = var;
    }

    public static Counter makeCounter() {
        return new Counter(0);
    }

    public int execute() {
        return ++varField;
    }

}
```

여러가지 변형된 Counter 클래스(익명 클래스나 제네릭을 사용한) 구현이 가능하지만, 어떤 경우에나 개발자가 직접 내부 상태를 관리해야 한다. 왜 클로저의 사용이 함수적 사고를 예시하는지가 여기에서 분명해진다. 런타임에 내부 상태의 관리를 맡겨버리는 것이다. 직접 필드를 생성하고 그 상태를 관리하기 보다는(멀티스레드 환경에서는 훨씬 더 끔찍할 것이다) 언어나 프레임워크가 보이지 않게 그 상태를 관리할 수 있도록 놔두라.

tip. 언어로 하여금 상태를 관리하게 하라.

클로저는 지연실행(deferred execution)의 좋은 예이다. 클로저 블록에 코드를 바인딩 함으로써 그 블록의 실행을 나중으로 연기할 수 있다. 이 기능은 여러모로 쓸모가 있다. 예를 들어, 클로저 블록을 정의할때는 필요한 값이나 함수가 스코프에 없지만, 나중에 실행할 때는 있을수가 있다. 실행 문맥을 클로저 안에 포장하면 적절한 때까지 기다렸다가 실행할 수 있게 된다.

명령형 언어는 상태로 프로그래밍 모델을 만든다. 그 좋은 예가 매개변수를 주고받는 것이다. 클로저는 코드와 문맥을 한 구조로 캡슐화해서 행위의 모델을 만들 수 있게 해준다. 이렇게 만들어진 클로저는 마치 전통적인 자료구조처럼 주고받을 수도 있고, 적절한 시간과 장소에서 실행할 수도 있다.

tip. <b>상태</b> 대신 <b>문맥</b>을 잡으라



## 3.3 커링과 부분 적용

커링과 부분적용은 20세기 수학자인 하스켈 커리 등의 작업을 통해 수학에서 유래한 언어 기술이다. 이 기술들은 여러 종류의 언어에서 볼 수 있고, 특히 모든 함수형 언어에 포함되어 있다. 커링이나 부분적용은 함수나 메서드의 인수의 개수를 조작할 수 있게 해준다. 주로 인수 일부에 기본값을 주는 방법을 사용한다. 이를 인수고정 이라고도 부른다. 대부분의 함수형 언어들은 커링과 부분적용을 지원하지만 구형방법은 가지각색이다.


### 3.3.1 정의와 차이점

언뜻 보기에는 커링과 부분적용이 같은 효과를 내는 것처럼 보인다. 두 가지 방법 모두 몇몇 인수를 이미 주어진 값으로 고정한 함수를 만드는 데 사용된다.

* <b>커링</b>(currying)은 다인수(multiargument)함수를 일인수(single-argument) 함수들의 체인으로 바꿔주는 방법이다. 이것은 그 변형 과정이지 변형된 함수를 실행하는것을 지칭하는것이 아니다. 함수의 호출자가 몇 개의 인수를 고정할지를 결정하며 적은 수의 인수를 가지는 함수를 유도해낸다.
* <b>부분적용</b>(partial application) 은 주어진 다인수 함수를 생략될 인수의 값을 미리 정해서 더 적은 수의 인수를 받는 하나의 함수로 변형하는 방법이다. 이 방법은 이름이 의미하듯이 몇몇 인수에 값을 미리 적용하고 나머지 인수만 받는 함수를 리턴한다.

커링이나 부분적용 모두 몇몇 인수의 값만 주면 인수가 몇 개 빠져도 호출할 수 있는 함수를 리턴해준다. 다만 커링은 체인의 다음 함수를 리턴하는 반면에, 부분적용은 주어진 값을 인수에 바인딩 시켜서 인수가 더 적은 하나의 함수를 만들어준다. 이 차이점은 인수의 수가 둘 이상인 함수를 살펴보면 명확해진다.

예를들어 process(x, y, z) 의 완전히 커링된 버전은 process(x)(y)(z) 이다. 여기에서 process(x)와 process(x)(y)는 인수가 하나인 함수이다. 첫 인수만 커링을 하면 process(x) 의 리턴값은 인수가 하나인(여기서는 y) 또 하나의 함수이다. 이 함수의 리턴값은 또 하나의 일인수 함수이다. 반면에 부분적용을 사용하여 변환하면 인수숫자가 적은 함수가 남는다. process(x, y, z) 의 인수 하나를 부분적용하면 인수 두 개 짜리의 process(y, z) 가 된다.

이 두가지 방법은 종종 같은 결과를 낳는다. 하지만 이 중요한 차이점이 자주 오해되곤 한다. 설상가상으로 그루비는 이 두 방법을 모두 지원하면서 둘 다 커링이라고 부른다. 스칼라는 부분적용과 PartialFunction 클래스를 모두 지원하는데 이 두가지는 이름은 비슷하지만 별개의 개념이다.


#### 그루비

그루비는 커링을 Closure 클래스의 curry() 함수를 사용하여 구현한다.

```groovy
def product = { x, y -> x * y }

def quadrate = product.curry(4) // 1
def octate = product.curry(8)   // 2

println "4x4: ${quadrate.call(4)}"	// 3
println "8x5: ${octate(5)}"		// 4

//4x4: 16
//8x5: 40
```

1. curry() 가 매개변수 하나를 고정하고 일인수 함수를 리턴한다.
2. octate() 함수는 항상 주어진 매개변수의 8배수를 리턴한다.
3. quadrate() 함수는 일인수 함수의고 Closure 클래스의 call() 메소드를 통해서 호출이 가능하다.
4. 그루비에는 호출을 간편하게 해주는 문법적 설탕이 있다.

위의 예제에서 product 는 매개변수 두 개를 받는 코드블록으로 정의된다. 그루비에 내장된 curry() 메서드를 사용하여 두 개의 코드블록(quadrate, octate) 을 만든다. 그루비는 이 코드블록을 호출하기 쉽게 해준다. 명시적으로 call() 메서드를 사용할 수도 있지만, 언어가 제공하는 octate(5) 처럼 코드블록 이름 다음에 매개변수를 괄호 안에 적는 구문 구조를 사용할 수도 있다.

이름과는 달리 curry() 는 밑에 깔린 클로저블록을 조작하는 부분적용으로 구현되어 있다. 하지만 다음 예제처럼 부분적용을 사용하여 다인수함수를 일련의 일인수 함수로 만들어서 커링을 흉내낼 수도 있다.

```groovy
def volume = { h, w, l -> h * w * l }
def area = volume.curry(1)
def lengthPA = volume.curry(1, 1)        // 1
def lengthC = volume.curry(1).curry(1)   // 2

println "The volume of the 2x3x4 rectangular solid is ${volume(2, 3, 4)}"
println "The area of the 3x4 rectangular is ${area(3, 4)}"
println "The length of the 6 line is ${lengthPA(6)}"
println "The length of the 6 line via curried function is ${lengthC(6)}"
```

1. 부분적용
2. 커링

위의 코드블록은 직육면체의 부피를 계산한다. 그리고 부피에서 높이를 1로 고정하여 직사각형의 면적을 구하는 코드블록을 만든다. 부피함수를 사용해서 직선의 길이를 구하는 코드블록을 구하려면 부분적용이나 커링을 모두 사용할 수 있다. lengthPA 는 처음 두 매개변수를 1로 고정하는 부분적용의 방식을 사용한다. lengthC 는 커링을 두 번 적용하여 같은 결과를 얻는다. 미묘한 차이가 있지만 결과는 마찬가지다. 하지만 함수형 개발자 근처에서 이 두가지를 횬용하면 분명히 고쳐주려 들 것이다. 불행하게도 그루비는 서로 밀접한 관계가 있는 이 두 개념을 하나로 사용한다.

함수형 프로그래밍은 명령형 언어가 다른 매커니즘으로 실현하는 것과 똑같은 목적을 달성할 수 있는 다른 빌딩블록을 제공한다. 이 빌딩블록들의 상호관계는 심사숙고하여 정해진것이다. 함수의 합성(composition)은 함수형 언어에서 흔히 쓰이는 조합 기술이다. 우선 아래 코드를 보자.

```groovy
//그루비에서 복합함수 만들기

def product = { x, y -> x * y }

def quadrate = product.curry(4)
def octate = product.curry(8)

def composite = { f, g, x -> return f(g(x)) }
def thirtyTwoer = composite.curry(quadrate, octate)

println "composition of curried functions yields ${thirtyTwoer(2)}"
```

위의 예제에서는 두 개의 함수를 합성하는 코드 블록을 만들었다. 여기서 합성이란 한 함수를 호출하여 다른 함수를 리턴하게 함을 말한다. 부분적용과 이 코드블록을 사용하여 thirtyTwoer 코드블록을 만든다.


#### 스칼라

스칼라는 제약이 있는 함수를 정의할 수 잇는 트레이트와 함께 커링과 부분적용을 모두 지원한다.

##### 커링

스칼라에서는 여러개의 인수목록을 여러개의 괄호로 정의할 수 있다. 함수를 정해진 인수의 수보다 적은 인수로 호출하면 그 리턴값은 나머지 인수를 받는 함수가 된다. 아래의 예제에서 스칼라 참고 문서에 나오는 예제를 살펴보자.

```scala
def filter(xs: List[Int], p: Int => Boolean): List[Int] =
    if (xs.isEmpty) xs
    else if (p(xs.head)) xs.head :: filter(xs.tail, p)
    else filter(xs.tail, p)

def modN(n: Int)(x: Int) = x % n == 0

val nums = List(1, 2, 3, 4, 5, 6, 7, 8)
println(filter(nums, modN(2)))
println(filter(nums, modN(3)))

//List(2, 4, 6, 8)
//List(3, 6)
```

위의 예제의 filter() 함수는 재귀적으로 주어진 필터 조건을 적용한다. modN() 함수는 두 개의 인수목록으로 정의된다. modN() 을 filter() 의 인수로 호출할 때에는 인수 하나를 전달한다. 여기서 filter() 함수는 두 번째 인수로 Int 인수를 받아 Boolean 을 리턴하는 함수를 받는다. 이 함수의 시그니처는 여기서 전달된 커링된 함수의 시그니처와 같다.


##### 부분적용함수

스칼라에서는 아래예제에서 볼 수 있듯이 부분적용을 사용할 수도 있다.

```scala
def price(product: String) : Double =
    product match {
        case "apples" => 140
        case "oranges" => 223
    }

def withTax(cost: Double, state: String) : Double =
    state match {
        case "NY" => cost * 2
        case "FL" => cost * 3
    }

val locallyTaxed = withTax(_: Double, "NY")
val costOfApples = locallyTaxed(price("apples"))

assert(Math.round(costOfApples) == 280)
```

위에서는 먼저 제품과 가격 사이의 매핑을 리턴하는 price() 함수를 만든다. 다음으로 그 비용과 판매 지역인 state 를 인수로 받는 withTax() 함수를 만든다. 여기서 필요없는 인수(state)를 계속해서 끌고 다니지 않고, 그것을 부분적용해서 고정한 함수를 리턴한다. 이 locallyTaxed() 함수는 cost 하나만을 인수로 받는다.


##### 부분 (제약이 있는) 함수

스칼라의 PartialFunction 트레이트는 패턴매칭에 자연스럽게 사용하려고 설계된 것이다. 이름은 비슷하지만 PartialFunction 은 부분적용함수를 생성하지는 않는다. 대신에 특정한 값이나 자료형에만 적용되는 함수를 만드는데 이것을 사용할 수 있다.

case 블록도 부분함수를 적용하는 한 방법이다. 다음예제는 전형적인 match 연산자가 없는 스칼라의 case 구문을 사용한다.

```scala
val cities
    = Map("Atlanta" -> "GA"
        , "New York" -> "New York"
        , "Chicago" -> "IL"
        , "San Francisco" -> "CA"
        , "Dallas" -> "TX")

    cities map { case (k, v) => println(k + " -> " + v) }
```

위의 예제에서는 먼저 도시와 주의 관계를 표현하는 맵을 만든다. 다음에 map() 함수를 컬렉션에 적용하면 map() 함수가 키와 값을 떼어내어 출력하게 된다. 스칼라에서는 case 명령문이 포함된 블록으로 익명함수를 정의할 수 있다. 사실 case 를 사용하지 않고도 익명함수를 정의할 수 있지만, 아래와 같은 이점이 있다.

```scala
List(1, 3, 5, "seven") map { case i: Int => i + 1}  // 작동하지 않음
// scala.MatchError: seven (of class java.lang.String)

List(1, 3, 5) collect { case i: Int => i + 1 }

assert(List(2, 4, 6) == (List(1, 3, 5, "seven") collect { case i: Int => i + 1 }))
```

위의 예제에서 "seven" 을 증가시킬 하면 MatchError 가 발생한다. 즉, 이종 컬렉션에는 case 를 map 함수에서 사용할 수 없다. 하지만 collect 는 제대로 작동한다. 왜 이런 차이가 있으며, 오류는 어디로 사라져버렸는가?

case 블록은 부분적용함수가 아니라 부분함수를 정의한다. <b>부분함수</b>는 허용되는 값의 범위가 국한되어 있다. 일례로 수학함수 1/x 는 x가 0 이면 유효하지 않다.

부분함수는 허용되는 값을 제한하는 방법을 제공한다. 위의 예제에서 collect() 를 부를때 case 가 정수에는 정의되어 있지만 문자열에는 정의되지 않았기 때문에 "seven" 문자열은 collect 되지 않을 것이다. 부분함수를 정의하려면 다음예제에서 보듯이 PartialFunction 트레이트를 사용할 수도 있다.

```scala
val answerUnits = new PartialFunction[Int, Int] {
    def apply(d: Int) = 42 / d
    def isDefinedAt(d: Int) = d != 0
}

assert(answerUnits.isDefinedAt(42))
assert(!answerUnits.isDefinedAt(0))
assert(answerUnits(42) == 1)
//answerUnits(0)
// java.lang.ArithmeticException: / by zero
```

PartialFunction 트레이트로 answerUnits 를 유도해내고, 두 함수를 정의한다. apply() 와 isDefinedAt() 이다. apply() 함수는 리턴값을 계산한다. PartialFunction 의 정의에 꼭 필요한 isDefinedAt() 함수는 인수가 적당한지를 결정하는 조건으로 사용한다.

위 예제의 answerUnits 는 case 블록을 사용하면 좀 더 간결하게 만들 수 있다.

```scala
def pAnswerUnits: PartialFunction[Int, Int] =
    { case d: Int if d != 0 => 42 / d }

assert(pAnswerUnits(42) == 1)
//pAnswerUnits(0)
// scala.MatchError: 0 (of class java.lang.Integer)
```

위에서는 case와 방호조건(guard condition) 을 함께 사용하여 인수를 제한하고 결과를 리턴한다. 여기서 한 가지 눈여겨 볼 것은 이 경우에는 패턴매칭을 사용했기 때문에 ArithmeticException 이 아니라 MatchError 가 발생한다.

부분함수는 수치형 자료형에만 국한된 것이 아니다. Any 를 포함한 모든 자료형에 사용할 수 있다. 다음예제에서 구현한 증가함수를 살펴보자.

```scala
def inc: PartialFunction[Any, Int] =
    { case i: Int => i + 1 }

assert(inc(41) == 42)
//inc("Forty-one")
// scala.MatchError: Forty-one (of class java.lang.String)

assert(inc.isDefinedAt(41))
assert(!inc.isDefinedAt("Forty-one"))

assert(List(42) == (List(41, "cat") collect inc))
```

위의 예제에서는 어떤 입력이든 받지만 그 일부분에 대해서만 반응하는 부분함수를 정의하였다. 하지만 이 부분함수의 isDefinedAt() 을 부를 수는 있다. PartialFunction 트레이트를 구현하면 isDefinedAt() 이 묵시적으로 정의되기 때문이다. 이전 예제에서 map() 과 collect() 가 다르게 동작하는 것을 보았다. 이는 부분함수의 작동방식 때문이다. collect() 는 부분함수를 받아 isDefinedAt() 을 호출해 맞지않는 요소는 무시한다. 스칼라에서 부분함수와 부분적용함수는 이름은 비슷하지만 서로 다른기능을 제공한다. 예를 들자면 부분함수를 부분적용할 수도 있다.

#### 보편적인 용례

정의도 까다롭고 구현방법도 다양하지만, 커링과 부분적용은 실제로 프로그래밍에서 큰 자리를 차지한다.

##### 함수팩토리

커링 (또는 부분적용) 은 전통적인 객체지향 언어에서 팩토리함수(factory function) 를 구현할 상황에서 사용하면 좋다. 이를 보여주는게 다음 예제에서 그루비로 구현한 간단한 덧셈함수다.

```groovy
def adder = { x, y -> x + y }
def incrementer = adder.curry(1)

println "increment 7: ${incrementer(7)}"    //8
```

위의 예제에서는 덧셈 함수를 사용해서 증가함수를 이끌어낸다.

##### 템플릿 메서드 패턴

Gang of Four (Gof) 의 디자인패턴중에 템플릿 메서드 패턴이 있다. 이 패턴의 목적은 구현의 유연성을 보장하기 위해서 내부의 추상메서드를 사용하는 겉껍질을 정의하는데 있다. 부분적용과 커링이 이 문제를 해결하는데 사용된다. 부분적용을 사용하여 이미 알려진 기능을 제공하고 나머지 인수들은 추후에 구현하도록 남겨두는 것은 이 객체지향 디자인 패턴을 구현하는 것과 흡사하다.



### 3.3.2 재귀

위키피디아에 따르면 <b>재귀</b>란 자신을 재참조하여 같은 프로세스를 반복하는 것을 말한다. 이 또한 런타임에 세부사항을 양도하는 예가 되고, 그래서 함수형 프로그래밍과 깊은 관계가 있다. 구체적으로 말하면 재귀는 같은 메서드를 반복해서 호출하여 컬렉션을 각 단계마다 줄여가면서 반복처리 하는것을 말한다. 이때 조심해야 할 것은 항상 종료조건(기저조건) 을 보장해야 한다는 점이다. 재귀를 사용한 코드는 이해가 쉬운데, 그것은 문제의 핵심이 같은 작업을 계속하여서 목록을 줄여나가는 것이기 때문이다.

#### 목록의 재조명

그루비는 함수형 구조를 더한 것을 포함해, 자바의 컬렉션 라이브러리를 엄청나게 커지게 만들었다. 그루비가 제공하는 것 중 첫 번째는 목록을 다른 각도에서 보는 것이었다. 언뜻 보기에는 별것 아닌 듯하지만, 흥미로운 이점을 제공한다.

배경이 C 나 C 계열 언어(자바포함) 인 개발자들은 목록을 색인된 컬렉션으로 생각한다. 다음예제에서 보듯이, 이렇게 생각하면 색인을 명시적으로 생각하지 않더라도 컬렉션을 반복 처리하기가 용이하다.

```groovy
def numbers = [6, 28, 4, 9, 12, 4, 8, 8, 11, 45, 99, 2]

static def iterateList(listOfNums) {
    listOfNums.each { n ->
        println n
    }
}

println "iterate list"
iterateList(numbers)
```

그루비에는 명시적으로 요소에 접근하기 위해 인덱스를 코드블록에 매개변수로 제공하는 eachWithIndex() 라는 반복자도 있다. 위의 예제에서 iterateList() 메서드는 인덱스를 사용하지는 않지만 다음 그림처럼 리스트를 정렬된 위치의 컬렉션으로 생각한다.

여기에 그림그림

많은 함수형 언어는 목록을 다른 각도에서 바라본다. 다행히 그루비도 이 시각을 갖고 있다. 아래 그림에서 보듯이 목록을 색인된 위치의 컬렉션으로 여기는 대신에, 첫 요소(머리)와 나머지(꼬리)의 조합으로 생각해보자.

여기에 그림그림

리스트를 머리와 꼬리로 생각하면 다음 예제처럼 재귀를 사용한 반복처리가 가능해진다.

```groovy
static def recurseList(listOfNums) {
    if (listOfNums.size == 0) return
    println "${listOfNums.head()}"
    recurseList(listOfNums.tail())
}

recurseList([1, 2, 3, 4, 5])
// 1, 2, 3, 4, 5
```

위의 예제의 recurseList() 메서드는 먼저 매개변수로 주어진 리스트가 비어있는지를 확인한다. 그럴경우에는 그냥 리턴하면 된다. 그렇지 않다면 그루비의 head() 메서드로 목록의 첫 요소를 출력하고 재귀적으로 recurseList() 를 목록의 나머지에 대해 호출한다.

재귀는 종종 플랫폼마다 기술적 한계가 있기 때문에 만병통치약이 될 수 없다. 하지만 길지 않은 목록을 처리하는 데에는 안전하다.

언어가 진화하면 이런 제약이 줄어들거나 아예 없어진다. 그런 날을 기대하면서 이러한 시각이 코드의 구조에 미치는 영향에 대해 알아보자. 재귀 버전의 이점이 금방 와 닿지 않을지도 모르지만, 목록을 필터하는 문제를 보면 이점이 더 확실해진다. 다음 예제에서는 목록과 술어를 받는 필터 메서드를 보자.

```groovy
static def filter(list, predicate) {
    def new_list = []
    list.each {
        if (predicate(it))
            new_list << it
    }

    return new_list
}

def modBy2 = { n -> n % 2 == 0 }

def l = filter(1..20, modBy2)

println l

[2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
```

위의 예제코드는 복잡하지 않다. 우선 보유하고 싶은 요소를 담을 보유인수를 생성한다. 그리고 리스트의 각 요소를 술어조건으로 점검해서 필터된 요소들만 들어있는 리스트를 리턴한다. 필터조건을 명시한 코드블록은 filter() 함수에 제공한다.

이제 이 필터메서드를 재귀적으로 구현하는것을 고려해보자

```groovy
static def filterR(list, predicate) {
    if (list.size() == 0) return list
    if (predicate(list.head()))
        [] + list.head() + filterR(list.tail(), predicate)
    else
        filterR(list.tail(), predicate)
}

println filterR(1..20, { it % 2 == 0 })
```

우선 주어진 리스트의 길이가 0 이면 즉시 리턴한다. 그렇지 않으면, 리스트의 head 를 필터조건에 비교해 통화하면 준비된 목록에 추가하고, 아니면 재귀적으로 tail 을 필터한다.

filter() 와 filterR() 의 차이정은 '누가 상태를 관리하는가?' 란 중요한 질문을 조명한다. 명령형 버전에서는 개발자가 관리한다. 이름이 new_list 인 새 인수를 생성하고, 계속 추가해야 한다. 끝나면 그것을 리턴해야한다. 재귀버전에서는 언어가 메서드 호출시마다 리턴값을 스택에서 쌓아가면서 관리한다. filterR() 메서드의 종료경로는 항상 return 이고 이렇게 중간값을 스택에 쌓는다. 개발자는 new_list 에 대한 책임을 양도하고 언어 자체가 그것을 관리해준다.

tip 재귀는 상태관리를 런타임에 양도할 수 있게 해준다.

filterR() 에서 살펴본 것과 같은, 커링과 재귀를 결합한 필터방법이 스칼라 같은 함수형 언어에서 사용하기에 적격이다. 다음예제에서 좀 더 알아보자.


```scala
// 스칼라에서의 재귀적 필터
object Example3_23 {

    def main(args: Array[String]): Unit = {

        val nums = List(1, 2, 3, 4, 5, 6, 7, 8)

        println(filter(nums, dividesBy(2)))  // 2
        println(filter(nums, dividesBy(3)))

    }

    def filter(xs: List[Int], p: Int => Boolean): List[Int] =
        if (xs.isEmpty) xs
        else if (p(xs.head)) xs.head :: filter(xs.tail, p)
        else filter(xs.tail, p)

    def dividesBy(n: Int)(x: Int) = x % n == 0  // 1

}
```

1. 커링할 함수를 정의한다.
2. filter 는 컬렉션(nums)과 일인수 함수(커링된 dividesBy() 함수) 를 매개변수로 받는다.

스칼라의 목록 생성 연산자는 두 경우 모두 리턴 조건을 쉽게 이해할 수 있다. 위의 코드는 스칼라 참고 문서의 재귀와 커링에 대한 예제이다. filter() 메서드는 술어함수인 매개변수 p 를 통해 재귀적으로 정수 목록을 필터한다. 이 메서드는 목록이 비었는지를 확인하고 비었으면 단번에 리턴한다. 아니면 헤드를 술어를 통해 점검해서 필터된 목록에 포함되어야 할지를 결정한다. 이 점검을 통과하면 그 리턴 값은 헤드가 첫번째 요소이고 필터된 테일이 나머지인 새 목록이다. 만약에 첫번째 요소가 조건을 통과하지 못하면 리턴값은 단순히 필터된 테일뿐이다.

가비지 컬렉션처럼 대단한 발전은 아니지만 재귀는 프로그래밍 언어의 중요한 흐름을 조명해준다. '움직이는 부분'의 관리를 런타임에 양도하는 것이다. 개발자가 중간 값을 건드리지 못하게 하면 결국 그로 인한 오류도 생기지 않을 것이다.

아마도 많은 개발자들이 현재 재귀를 사용하고 있지 않을것이다. 명령형 언어들이 지원을 잘 하지 못해서 사용하기가 어렵기 때문이기도 하다. 함수형 언어들은 깔끔한 구문과 지원을 통해 재귀를 간단한 코드 재활용의 도구로 사용할 수 있게 한다.


##### 꼬리 호출 최적화

스택 증가는 재귀가 좀 더 보편화되지 못하는 주된 이유 중 하나이다. 재귀는 보통 중간 값을 스택에 보관하게끔 구현되는데, 재귀에 최적화되지 않은 언어에서는 스택 오버플로우를 유발하게 된다. 스칼라나 클로저 같은 언어들은 이 제약을 몇 가지 방법으로 우회한다. 런타임이 이 문제를 처리하는데 도움을 줄 수 있는 방법 중 하나는 꼬리 호출 최적화(tail-call optimization)이다.

재귀 호출이 함수에서 마지막 단계이면, 런타임이 스택을 증가시키지 않고 스택에 놓여 있는 결과를 교체할 수 있다. 많은 함수형 언어는 수택 증가 없이 재귀를 구현한다.


### 3.4 스트림과 작업 재정렬

명령형에서 함수형 스타일로 바꾸면 얻는 것 중 하나가 런타임이 효율적인 결정을 할 수 있게 된다는 점이다. 2 장에서 보았던 companyProcess 의 자바8 버전을 다시 살펴보자

```java
public String cleanNamesP(List<String> names) {
    if (names == null)
        return "";

    return names
            .stream()
            .map(e -> capitalizeString(e))
            .filter(n -> n.length() > 1)
            .collect(Collectors.joining(","));
}
```

위의 코드는 이전에 했었던 코드와 약간 달라져 있다. 이전의 코드와 작업의 순서가 달라졌는데, 여기서는 map() 작업이 filter() 보다 먼저 실행된다. 명령형 사고로는 당연히 필터 작업이 맵 작업보다 먼저 와야 한다. 그래야 맵 작업의 양이 줄어든다. 하지만 함수형언어(자바 8 이나 함수형 자바 프레임워크를 포함) 에는 Stream 이란 추상 개념이 정의되어 있다. Stream 은 여러모로 컬렉션과 흡사하지만 바탕값(backing value)이 없다. 대신 원천에서 목적지까지 값들이 흐르게끔 한다. 위의 예제에서 원천은 names 컬렉션이고 목적지는 collect() 함수이다. 이 두 작업 사이에서 map() 과 filter() 는 <b>게으른</b> 함수이다. 다시 말하자면 이들은 실행을 가능하면 미룬다. 이들은 목적지에서 요구하지 않으면 결과를 내려고 시도하지도 않는다.

영리한 런타임은 게으른 작업들을 제정렬 할 수 있다. 예제에서 런타임은 필터를 맵 작업전에 실행하여 게으른 작업을 효율적으로 재정렬 할 수도 있다. 자바에 추가된 다른 많은 함수형 기능처럼 filter() 같은 함수에 주어진 람다 블록에 부수효과가 없어야 한다. 그렇지 않으면 예측할 수 없는 결과를 초래하게 된다.

런타임에 최적화를 맡기는 것이 양도의 중요한 예이다. 시시콜콜한 세부사하응ㄴ 버리고 문제 도메인의 구현에 집중하게 되는 것이다.





<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























