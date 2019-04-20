---
layout: single
categories:
  - Functional Programming
title: Functional Thinking Chapter 6. 전진하라
author: Gold
author-email: pnurep@gmail.com
description: 함수형사고_챕터_6_전진하라
tags:
  - Functional Programming
  - FP
  - Kotlin
  - Scala
  - Haskell
  - Groovy
  - Closure
last_modified_at: 2019-04-20T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# 전진하라

사용하는 언어의 주된 패러다임이 객체지향이라면, 모든 문제의 해법을 객체지향적으로 찾기 마련이다. 하지만 대부분의 현대 언어들은 다중 패러다임을 갖고 있다. 다시 말하자면, 이 언어들은 객체지향형, 메타객체형, 함수형 등의 패러다임을 다 지원한다. 문제에 적합한 패러다임을 사용하는 법을 배우느 것이 더 좋은 개발자로 진화하는 길 중의 하나이다.


## 6.1 함수형 언어의 디자인 패턴

함수형 언어계의 어떤 이들은 디자인 패턴이 개념자체에 결함이 있기 때문에 함수형 프로그래밍에서는 필요가 없다고 주장한다. 패턴의 좁은 정의만 볼 때에는 일리가 있는 말이다. 하지만 그런 주장은 패턴의 사용보다는 의미론에 국한된 것이다. 디자인패턴의 개념은 아직도 건재하다. 하지만 다른 패러다임에서 패턴들은 다른형태로 나타난다. 함수형에서는 빌딩블록과 문제의 접근 방법이 다르기 때문에, 전통적인 GoF 패턴들 중의 일부는 사라지고, 나머지는 근본적으로 다른 방법으로 같은 문제를 풀게된다.

함수형 프로그래밍에서는 전통적인 디자인 패턴들이 다음과 같은 세 가지로 나타난다.

* 패턴이 언어에 흡수된다.
* 패턴 해법이 함수형 패러다임에도 존재하지만, 구체적인 구현 방식은 다르다.
* 해법이 다른 언어나 패러다임에 없는 기능으로 구현된다.

이 세 경우를 하나씩 살펴보자.


## 6.2 함수 수준의 재사용

합성(composition) 은 함수형 프로그래밍 라이브러리에서 재사용의 방식으로 자주 사용된다. 함수형 언어들은 객체지향 언어들보다 더 큰 단위로 재사용을 한다. 그러기 위해서 매개변수로 커스터마이즈되는 공통된 작업들을 추출해낸다. 객체지향 시스템은 메시지 전달(구체적으로 말하자면 메서드 실행)을 통해 소통하는 객체들로 구성되어 있다. 유용한 클래스들과 메시지들을 발견하면, 그 클래스들의 그래프를 재사용을 위해 추출한다.

GoF 디자인 패턴이 소프트웨어 엔지니어링 세계에서 매우 잘 팔리는 책 중 하나라는 것이 놀라운 일은 아니다. 이 책은 바로 추출된 패턴들의 카탈로그인 셈이다. 패턴을 이용한 재사용이 워낙 널리 퍼져서, 다른 책들도 독특한 이름으로 이렇게 추출된 패턴의 목록을 만들곤 한다. 디자인 패턴 운동은 소프트웨어 개발 업계에 명명법과 예제들을 제공하여 큰 성공을 거두었다. 하지만 디자인 패턴을 통한 재사용은 궁극적으로 작은 단위의 재사용이다. 한 가지의 해법은 다른 것과 전혀 상관이 없다. 디자인 패턴으로 해결할 수 있는 문제들은 아주 특정하고, 그런 특정한 문제들을 자주 발견한다면 요긴하게 사용할 수 있다. 하지만 패턴이 그 문제에만 적용되기 때문에 그 사용범위는 좁을 수 밖에 없다.

함수형 프로그래머들도 코드를 재사용하고 싶어 하지만 그들은 다른 빌딩블록을 사용한다. 함수형 프로그래밍은 구조물들 간에 잘 알려진 관계(커플링)를 만들기보다는, 큰 단위의 재사용 메커니즘을 추출하려 한다. 이런 노력은 객체간의 관계를 규정하는 수학의 한 분야인 카테고리 이론에 근거를 둔다. 대부분의 애플리케이션들은 리스트를 사용한다. 그래서 함수형 접근 방법은 리스트를 중심으로 재사용 메커니즘을 구축하며, 이때 사용되는 것이 상황에 따라 달라지고 이동가능한 코드다. 함수형 언어들은 일급함수를 매개변수나 리턴값으로 사용한다.

filter() 메서드에서처럼 코드를 매개변수로 전달하는 기능은 코드 재사용의 다른 접근 방법을 제시해준다. 전통적인 디자인 패턴을 사용한느 객체지향의 관점에서 볼 때는 클래스나 메서드를 만들어서 문제를 푸는 방식이 더 편해 보일 수도 있다. 하지만 함수형 언어들은 어떤 토대(scaffolding)나 보일러플레이트를 사용하지 않고도 같은 결과를 얻을 수 있게 해준다. 궁극적으로 디자인 패턴의 존재 목적은 언어의 결함을 메꾸기 위함일 뿐이다. 쓸데없이 뼈대뿐인 클래스에 래핑하지 않고는 행동을 전달할 수 없는 결함 같은 것 말이다.



### 6.2.1 템플릿 메서드

일급함수를 사용하면 불필요한 구조물들을 없앨 수 있기 때문에 템플릿 메서드 디자인 패턴을 구현하기가 쉬워진다. 템플릿 메서드는 하나의 알고리즘의 뼈대만 정의하고, 세부 절차는 하위 클래스가 주어진 알고리즘의 구조를 바꾸지 않고 정의하게끔 한다.

```groovy
abstract class Customer {

    def plan

    def Customer() {
        plan = []
    }

    def abstract checkCredit()
    def abstract checkInventory()
    def abstract ship()

    def process() {
        checkCredit()
        checkInventory()
        ship()
    }

}
```

위의 예제에서 process() 메서드는 checkCredit(), checkInventory(), ship() 에 의존한다. 이들은 추상메서드이기 때문에 하위 클래스가 그 정의를 제공해야 한다.

일급함수는 다른 여느 자료구조처럼 사용할 수 있으므로, 위의 코드는 코드블록을 사용하여 아래처럼 다시 정의할 수 있다.

```groovy
// 일급 함수를 사용한 템플릿 메서드

class CustomerBlock {

    def plan, checkCredit, checkInventory, ship

    def CustomerBlock() {
        plan = []
    }

    def process() {
        checkCredit()
        checkInventory()
        ship()
    }

}

```

위의 예제에서 알고리즘의 각 단계는 클래스에 할당할 수 있는 성질에 불과하다. 이것이 상세한 구현방법을 언어의 기능으로 감추는 일례다. 함수들의 구현을 나중으로 미루고, 이 패턴을 이 문제의 해법으로 생각할 수 있다. 이 방법이 더 간단함을 알 수 있을 것이다.

앞의 두 해법은 동등하지 않다. 전통적인 템플릿 메서드는 하위 클래스가 추상클래스에서 정해준 메서드를 구현해야 한다. 물론 하위 클래스에서 텅 빈 메서드를 구현할 수도 있지만, 추상 메서드의 정의는 하위 클래스를 구현하는 개발자에게 알려주는 일종의 문서 역할을 한다. 좀 더 유동성이 요구되는 상황에서는 이렇게 고정화된 메서드 선언이 적합하지 않을수도 있다. 예를 들어, 어떤 메서드도 받아서 실행할 수 있는 Customer 클래스를 만들 수 도 있기 때문이다.

코드블록과 같은 기능을 지원하는 언어들은 개발자들이 사용하기 쉽다. 하위 클래스를 구현할 때 몇 단계 건너뛸 수 있게 하는 경우를 생각해보자. 그루비에는 객체가 널인지 확인한 후에 메서드를 실행하는 특별한 보호 접근자(?.) 가 있다. 아래 예제에서 precess() 의 정의를 살펴보자.

```groovy
def process() {
    checkCredit?.call()
    checkInventory?.call()
    ship?.call()
}
```

위의 예제에서 checkCredit(), checkInventory(), ship() 에 해당하는 함수를 구현하는 개발자는 이 함수들을 그냥 비워둘 수 도 있다. ?. 같은 문법적 설탕 덕분에, 개발자들은 길게 열거된 if 블록같은 반복적이고 읽기 어려운 코드는 언어에 양도할 수 있고, 보일러플레이트 코드를 표현이 풍부한 코드로 대체할 수 있다. ?. 전혀 함수형의 성질이 없지만, 골치 아픈 일들을 런타임에 맡기는 예제로는 안성맞춤이다. 고차함수가 있기 때문에 커맨드패턴이나 템플릿 패턴 같은 고전적인 패턴에서 자주 사용되는 보일러플레이트 코드가 필요 없어진다.


### 6.2.2 전략

일급함수의 사용으로 간편해진 디자인 패턴으로는 전략패턴을 들 수 있다. 전략패턴은 각자 캡슐화되어 서로 교환 가능한 알고리즘 군을 정의한다. 이것은 클라이언트에 상관없이 알고리즘을 바꿔서 사용할 수 있게 해주는 패턴이다. 일급함수를 사용하면 전략을 만들고 조작하기가 쉽다.

```groovy
interface Calc {
    def product(n, m)
}

class CalcMult implements Calc {

    @Override
    def product(Object n, Object m) {
        return n * m
    }

}

class CalcAdds implements Calc {

    @Override
    def product(Object n, Object m) {
        def result = 0
        n.times {
            result += m
        }
        result
    }

}
```

위의 예제에서 두 수의 곱을 인터페이스로 정의하고, 곱셈과 덧셈을 각각 사용한 두 가지 구체클래스를 구현했다. 다음 예제에서는 테스트 케이스를 만들어 이 전략들을 테스트한다.

```groovy
// 곱 전략 테스트하기
class StrategyTest {

    def listOfStrategies = [new CalcMult(), new CalcAdds()]

    @Test
    public void product_verifier() {
        listOfStrategies.each { s ->
            assertEquals(10, s.product(5, 2))
        }
    }

}
```

두 전략 모두 같은 값을 리턴한다. 코드 블록을 일급함수로 사용하여, 이전 예제에서의 보일러플레이트 코드의 대부분을 제거할 수 있다.


### 6.2.3 플라이웨이트 패턴과 메모이제이션

플라이웨이트 패턴은 많은 수의 조밀한 객체의 참조들을 공유하는 최적화 기법이다. 참조들을 객체 풀에 생성하여 특정 뷰를 위해 사용한다.

플라이웨이트는 같은 자료형의 모든 객체를 대표하는 하나의 객체, 즉 표준 객체라는 아이디어를 사용한다. 다시 말하면, 소비자 상품의 표준 상품은 같은 모든 상품을 대표한다.

애플리케이션 내에서 각 사용자를 위해 상품목록을 모두 생성하기보다는, 표준 상품들의 목록을 하나 만들고 각 사용자는 원하는 상품의 참조를 가지는 식이다.

```groovy
//컴퓨터 종류를 모델링한 간단한 클래스
class Computer {
    def type
    def cpu
    def memory
    def hardDrive
    def cd
}

class Desktop extends Computer {
    def driveBays
    def fanWattage
    def videoCard
}

class Laptop extends Computer {
    def usbPorts
    def dockingBay
}

class AssignedComputer extends Computer {
    def computerType
    def userId

    public AssignedComputer(computerType, userId) {
        this.computerType = computerType
        this.userId = userId
    }
}
```

모든 컴퓨터들의 사양이 같고, Computer 인스턴스를 각 사용자마다 생성하는 것이 비효율적이라고 가정하자, AssignedComputer 는 컴퓨터와 사용자를 연계해준다.

이 코드를 효율적으로 만드는 보편적인 방법은 팩토리 패턴과 플라이웨이트 패턴을 같이 사용하는 것이다. 아래에서 싱글턴 팩토리를 사용하여 표준 컴퓨터 종류를 만들어보자.

```groovy
/// 컴퓨터의 플라이웨이트 인스턴스를 만드는 싱글턴 팩토리
class CompFactory {
    
    def types = [:]
    static def instance
    
    private ComputerFactory() {
        def laptop = new Laptop()
        def tower = new Desktop()
        types.put(“MacbookPro”, laptop)
        types.put(“SunTower”, tower)
    }
    
    static def getInstance() {
        if (instance == null)
            instance = new CompFactory()
        
        instance
    }
    
    def ofType(computer) {
        types[computer]
    }
    
}
```

CompFactory 클래스는 가능한 모든 종류의 컴퓨터의 캐시를 만들고, 적당한 인스턴스를 ofType() 메서드를 통해서 전달한다. 자바에서 흔히 볼 수 있는 전형적인 싱글턴 팩토리이다.

여기서 싱글턴의 사용은 패턴을 런타임에 양도하는 또 하나의 좋은 예를 보여준다. 아래 예제에서는 그루비가 지원하는 @Singleton 어노테이션을 통해 CompFactory 를 더 단순화해 보자.

```groovy
// 단순화한 싱글턴 팩토리
@Singleton(strict = false) class ComputerFactory {
    def types = [:]
    
    private ComputerFactory() {
        def laptop = new Laptop()
        def tower = new Desktop()
        types.put(“MacbookPro”, laptop)
        types.put(“SunTower”, tower)
    }
    
    def ofType(computer) {
        types[computer]
    }
}
```

이 클래스의 유닛테스트는 이 팩토리가 표준 인스턴스를 리턴한다는 것을 테스트한다.

```groovy
//표준 종류 테스트하기
class ComputerFactoryTest {

    @Test
    public void comp_factory() {
        def bob = new AssignedComputer(
                ComputerFactory.instance.ofType(“MacBookPro”), “Bob”)
        def steve = new AssignedComputer(
                ComputerFactory.instance.ofType(“MacBookPro”), “Steve”)

        assertTrue(bob.computerType == steve.computerType)
    }

}
```

인스턴스마다 공통된 정보를 따로 저장하는 것은 좋은 아이디어이다. 나는 이 아이디어를 유지 한 채로 함수형 프로그래밍으로 넘어가고 싶다. 하지만 상세한 구현은 상당히 다르다. 이것ㄷ이 패턴의 의미를 보존하면서 구현을 다르게(가능한 좀 더 간단하게) 하는 한 예이다.

이전에 보았듯이, 메모아이즈 된 함수는 런타임이 값을 캐시할 수 있게 한다.

```groovy
// 플라이웨이트를 메모아이즈하기
def computerOf = { type ->
    def of = [MacBookPro: new Laptop(), SunTower: new Desktop()]
    return of[type]
}

def computerOfType = computerOf.memoize()
```

위에서 표준종류는 computerOf 함수로 정의된다. 메모아이즈된 인스턴스를 생성하기 위해서는 memoize() 메서드를 호출하기만 하면 된다. 아래 예제에서 두 접근 방식을 비교하는 유닛테스트를 볼 수 있다.

```groovy
//접근 방식의 비교

@Test
    public void flyweight_computers() {
        def bob = new AssignedComputer(
                ComputerFactory.instance.ofType(“MacBookPro”), “Bob”)
        def steve = new AssignedComputer(
                ComputerFactory.instance.ofType(“MacBookPro”), “Steve”)
        assertTrue(bob.computerType == steve.computerType)

        def sally = new AssignedComputer(
                computerOfType(“MacBookPro”), “Sally”)
        def betty = new AssignedComputer(
                computerOfType(“MacBookPro”), “Betty”)
        assertTrue(sally.computerType == betty.computerType)
    }
```

결과는 같지만 상세한 구현의 큰 차이를 볼 수 있다. 전통적인 디자인 패턴에서는, 두 개의 메서드를 구현한 새 클래스를 팩토리로 사용한다. 함후형 버전에서는 하나의 메서드를 구현한 후에 메모아이즈 된 버전을 리턴한다. 캐싱처럼 세부적 사항을 런타임에 맡기면 직접 구현한 코드가 실패할 기회가 적어진다. 여기서는 플라이웨이트 패턴의 의미를 보존하면서 아주 간단하게 구현하였다.


### 6.2.4 팩토리와 커링

디자인 패턴 차원에서 보면, 커링은 함수의 팩토리처럼 사용된다. 함수형 프로그래밍 언어에서 보편적인 기능은 함수를 여느 자료구조 처럼 사용할 수 있게 해주는 일급함수들이다. 이 기능 덕분에, 주어진 조건에 따라 다른 함수들을 리턴하는 함수를 만들 수 있다. 이것이 사실상 팩토리의 본질이다. 예를 들어, 두 개의 수를 더하는 일반적인 함수가 있다면, 커링을 팩토리로 사용하여 매개변수에 1을 더하는 함수(증가기)를 만들 수 있다.

```groovy
//함수 팩토리로 사용되는 커링

def adder = { x, y -> x + y }

def incrementer = adder.curry(1)

println(incrementer(7))

```

우선 첫 매개변수를 1로 커링하여, 변수 하나만 받는 함수를 리턴한다. 실직적으로 함수 팩토리를 만든 셈이다.

```scala
// 스칼라에서의 재귀적 필터링

def filter(xs: List[Int], p: Int => Boolean): List[Int] =
    if (xs.isEmpty) xs
    else if (p(xs.head)) xs.head :: filter(xs.tail, p)
    else filter(xs.tail, p)

def dividesBy(n: Int)(x: Int) = x % n == 0  // (1)

val nums = List(1, 2, 3, 4, 5, 6, 7, 8)
println(filter(nums, modN(2)))				// (2)
println(filter(nums, modN(3)))

(1) 커링할 함수를 정의한다.
(2) filter 는 컬렉션(nums)과 일인수 함수(dividesBy() 함수) 를 매개변수로 받는다.
```

위의 예제에서 패턴의 관점에서 흥미로운 점은 dividesBy() 에 커링이 ‘아주 쉽게’ 적용된다는 점이다. dividesBy() 는 두 매개변수를 받아 둘째 매개변수가 첫째 매개변수로 나뉘는지에 따라 true 나 false 를 리턴하는 함수이다. 하지만 이 함수가 filter() 메서드를 호출할 때는 하나의 매개변수만 받는다. 매개변수를 하나만 준 결과는 filter() 메서드에서 술어로 사용되는 커리된 함수이다.

이 예제는 이 절의 시작에서 언급한 함수형 프로그래밍에서늬 패턴의 두 가지 형태를 보여준다. 첫째, 커링이 언어나 런타임에 내장되어 있기 때문에, 함수 팩토리의 개념이 이미 녹아들어 있어 다른 구조물이 필요없다. 둘째, 다양한 구현 방법에 대한 중요성을 보여준다. 대부분의 자바 개발자들에게는 커링을 사용하는 것이 생소할 것이다. 그들은 같은 코드를 여기저기 쉽게 사용할 수 없을 뿐 아니라, 일반화된 함수에서 특정한 함수를 만들어낼 생각조차 할 수 없었을 것이다. 사실, 대부분의 명령형 개발자들은 이 상황에서 디자인 패턴을 쓸 생각을 하기 어려울 것이다. 디자인 패턴이란 문제를 풀기위해 구조물에 의존하므로 간단히 구현하기 어려운 큰 문제들을 푸는 방법이라고 생각하는 반면, 일반화된 함수에서 특정한 dividesBy() 함수를 만드는 것은 작은 문제라고 생각하기 때문이다. 커링을 본래 의도대로 사용하면 이미 사용되는 이름 이외에 다른 이름을 형식적으로 만들 필요도 없다.

tip. <b>일반적</b> 함수에서 <b>특정한</b> 함수를 만들때는 커링을 사용하라.


## 6.3 구조형 재사용과 함수형 재사용

1 장에서 본 인용문을 다시 떠올려보자

> 객체지향 프로그래밍은 움직이는 부분을 캡슐화하여 코드이해를 돕고, 함수형 프로그래밍은 움직이는 부분을 최소화하여 코드이해를 돕는다.

특정한 추상 개념을 매일 사용하면, 머릿속에 점차적으로 스며들어 문제를 해결하는 방법을 변화시킨다. 이 절에서는 리팩토링을 통한 코드 재사용과 거기에 따르는 추상개념의 영향력을 알아보자.

객체지향의 한 가지 목적은 캡슐화와 상태조작을 쉽게 하는 것이다. 그래서 객체지향형 추상화는 문제 해결을 위해 주로 상태를 이용한다. 움직이는 부분인 클래스와 클래스간의 상호관계를 주로 사용하게 된다.

함수형 프로그래밍은 구조물들을 연결하기보다는 부분들로 구성하여 움직이는 부분을 최소화 하려고 노력한다. 객체지향 언어의 경험만 있는 개발자들은 이 미묘한 개념의 차이를 쉽게 보지 못한다.


### 6.3.1 구조물을 사용한 코드 재사용

명령형 및 객체지향형 프로그래밍 스타일에서는 구조물과 메시징이 빌딩블록이다. 객체지향 코드를 재사용하려면, 대상이 되는 코드를 다른 클래스로 옮기고 상속을 통해 접근해야 한다.

코드 재사용과 그 파급효과를 설명하기 위해, 코드 구조와 스타일을 볼 수 있는 자연수 분류기를 다시 살펴보겠다. 자연수의 약수를 사용하여 그 수가 소수인지를 확인하는 코드를 만들 수도 있다. 두 문제 모두 약수를 사용하기 때문에 리팩토링의 좋은 후보이자, 코드 재사용을 설명할 적절한 예이다.

```java
public class ClassifierAlpha {
    
    private int number;

    public ClassifierAlpha(int number) {
        this.number = number;
    }
    
    public boolean isFactor(int potential_factor) {
        return number % potential_factor == 0;
    }
    
    public Set<Integer> factors() {
        HashSet<Integer> factors = new HashSet<>();
        for (int i = 0; i <= Math.sqrt(number); i++) {
            if (isFactor(i)) {
                factors.add(i);
                factors.add(number / i);
            }
        }
        return factors;
    }
    
    static public int sum(Set<Integer> factors) {
        Iterator it = factors.iterator();
        int sum = 0;
        while(it.hasNext())
            sum += ((Integer) it.next());
        
        return sum;
    }
    
    public boolean isPerfect() {
        return sum(factors()) - number == number;
    }
    
    public boolean isAbundant() {
        return sum(factors()) - number > number;
    }
    
    public boolean isDeficient() {
        return sum(factors()) - number < number;
    }
    
}
```


우리의 목적은 코드 재사용을 살펴보는 것이다. 이는 다음의 소수를 구하는 코드로 자연스럽게 이어진다.

```java
{% raw %}
public class PrimeAlpha {

    private int number;

    public PrimeAlpha(int number) {
        this.number = number;
    }

    public boolean isPrime() {
        Set<Integer> primeSet = new HashSet<Integer>() {{
            add(1); add(number); }};

        return number > 1 && factors().equals(primeSet);
    }

    public boolean isFactor(int potential_factor) {
        return number % potential_factor == 0;
    }

    private Set<Integer> factors() {
        HashSet<Integer> factors = new HashSet<>();
        for (int i = 0; i < Math.sqrt(number); i++) {
            if (isFactor(i)) {
                factors.add(i);
                factors.add(number / i);
            }
        }
        return factors;
    }

}
{% endraw %}
```

위의 예제에서 주목할 만한 점이 몇 가지 있다. 첫째는 isPrime() 메서드의 약간 이상한 초기화 코드이다. 이것은 자바에서 인스턴스 생성에 사용되는 특이한 코드로, 코드 블록을 클래스 내부지만 메서드 선언문 밖에 넣어서 생성자 밖에서 인스턴스를 만드는 인스턴스 초기화의 일례이다.

다음으로 주목해야 할 것은 isFactor() 와 factors 메서드이다. 이것들은 ClassifierAlpha 클래스에서 본 것들과 동일하다. 두 가지의 해법을 독립적으로 구현한 결과가 사실상 같음을 깨닫게 하는 자연스러운 결과다.


#### 중복코드를 제거하는 리팩토링

이런 종류의 중복된 코드를 제거하는 방법은 하나의 Factors 클래스로 리팩토링 하는 것이다.

```java
// 리팩토링한 공통 코드

public class FactorsBeta {
    
    protected int number;

    public FactorsBeta(int number) {
        this.number = number;
    }
    
    public boolean isFactor(int potential_factor) {
        return number % potential_factor == 0;
    }
    
    public Set<Integer> getFactors() {
        HashSet<Integer> factors = new HashSet<>();
        for (int i = 0; i <= Math.sqrt(i); i++) {
            if (isFactor(i)) {
                factors.add(i);
                factors.add(number / i);
            }
        }
        return factors;
    }
    
}
```

위의 코드는 IDE 에서 제공하는 상위 클래스 추출 기능을 사용하여 리팩토링한 것이다. 추출된 두 개의 메서드 모두 numbers 멤버변수를 사용하기 때문에, 이것을 상위클래스로 끌어올렸다. 리팩토링 할 때, IDE 는 이 변수의 접근법(접근자, 보호 스코프 등)에 대해 내게 물어봤다. 이에 대해 나는 클래스에 number 를 넣고 생성자로 하여금 그 값을 정하게 하는 보호스코프를 선택했다.

중복된 코드를 분리 및 제거하고 나면, 자연수 분류기와 소수 찾기는 아주 간단해진다. 아래 예제는 리팩토링된 자연수 분류기다.

```java
// 리팩토링으로 간단해진 분류기

public class ClassifierBeta extends FactorsBeta {

    public ClassifierBeta(int number) {
        super(number);
    }

    public int sum() {
        Iterator it = getFactors().iterator();
        int sum = 0;
        while (it.hasNext())
            sum += ((Integer) it.next());

        return sum;
    }

    public boolean isPerfect() {
        return sum() - number == number;
    }

    public boolean isAbundant() {
        return sum() - number > number;
    }

    public boolean isDeficient() {
        return sum() - number < number;
    }

}
```

```java
// 리팩토링으로 간단해진 소수 찾기
{% raw %}
public class PrimeBeta extends FactorsBeta {
    
    public PrimeBeta(int number) {
        super(number);
    }
    
    public boolean isPrime() {
        Set<Integer> primeSet = new HashSet<Integer>() {{
            add(1); add(number); }};
        
        return getFactors().equals(primeSet);
    }
    
}
{% endraw %}
```

리팩토링 시 number 변수의 접근 방식을 어떻게 정하든 이 문제에서는 커플링된 여러 개의 클래스를 다뤄야 한다. 이것은 문제의 부분들을 분리해주기 때문에 종종 이롭기도 하지만, 상위 클래스를 바꾸면 하위 클래스에도 영향을 주는 문제점이 있다.

이것이 <b>커플링</b> 을 통한 코드 재사용의 일례이다. 상위 클래스의 number 변수와 getFactors() 메서드를 공유함으로써 두 클래스를 묶어버리는 방법이다. 다시 말하면, 언어에 내장되어 있는 커플링 법칙을 사용하여 작동한다. 객체지향은 커플링된 상호작용 스타일을 정의한다. 그 예로 상속을 통해 멤버변수에 접근하는 것을 들 수 있다. 따라서 커플링 방식을 정하는 규칙들이 있다. 이런 규칙들은 동작을 일관되게 이해하는 데 도움이 될 수 있다. 상속을 사용하는 것이 좋지 않다고 주장하려는 것은 아니다. 이 방식이 객체지향 언어에서 더 나은 성질을 가진 다른 추상개념 대신에 남용되고 있다는 뜻이다.


#### 합성을 사용한 코드 재사용

아래 예제는 2 장에서 짠 조금 더 함수형인 자연수 분류기다.

```java
public class NumberClassifier {

    public static boolean isFactor(final int candidate, final int number) { // (1)
        return number % candidate == 0;
    }

    public static Set<Integer> factors(final int number) {  // (2)
        Set<Integer> factors = new HashSet<>();
        factors.add(1);
        factors.add(number);
        for (int i = 2; i < number; i++)
            if (isFactor(i, number))
                factors.add(i);

        return factors;
    }

    public static int aliquotSum(final Collection<Integer> factors) {
        int sum = 0;
        int targetNumber = Collections.max(factors);
        for (int n : factors) {                         // (3)
            sum += n;
        }
        return sum - targetNumber;
    }

    public static boolean isPerfect(final int number) { // (4)
        return aliquotSum(factors(number)) == number;
    }

    public static boolean isAbundant(final int number) {
        return aliquotSum(factors(number)) > number;
    }

    public static boolean isDeficient(final int number) {
        return aliquotSum(factors(number)) < number;
    }

}

(1) 모든 메서드는 number 를 매개변수로 받아야한다. 그 값을 유지할 내부상태는 없다.
(2) 모든 메서드는 순수함수이기 때문에 public static 이다. 그렇기 때문에 자연수 분류 문제라는 범위 밖에서도 유용하다.
(3) 일반적이고 합리적인 변수의 사용으로 함수 수준에서의 재사용이 쉬워졌다.
(4) 이 코드는 캐시가 없기 때문에 반복적으로 사용하기에 비능률적이다.
```

isPrime() 메서드를 공유된 상태 없이 순수함수를 사용하게 바꾼 함수형 버전의 isPrime() 메서드가 아래의 예제이다.

```java
// 함수형 소수 찾기

public class FPrime {

    public static boolean isPrime(int number) {
        Set<Integer> factors = Factors.of(number);
        return number > 1 &&
                factors.size() == 2 &&
                factors.contains(1) &&
                factors.contains(number);
    }

}
```

명령형 버전에서 했던 것처럼 중복된 코드를 Factors 클래스로 추출한 버전이 아래 예제이다 factors() 메서드는 가독성을 위해 of() 로 이름ㅇ르 바꿨다.

```java
// 함수형으로 리팩토링한 Factors 클래스

public class Factors {

    public static boolean isFactor(int number, int potential_factor) {
        return number % potential_factor == 0;
    }

    public static Set<Integer> of(int number) {
        HashSet<Integer> factors = new HashSet<>();
        for (int i = 1; i < Math.sqrt(number); i++) {
            if (isFactor(number, i)) {
                factors.add(i);
                factors.add(number / i);
            }
        }
        return factors;
    }

}
```

함수형 버전의 모든 상태는 매개변수로 주어지기 때문에, 이 추출한 클래스에는 공유된 상태가 없다. 일단 클래스를 추출해내면, 그것을 사용하여 함수형 분류기와 소수찾기를 리팩토링할 수 있다. 아래 예제가 리팩토링한 자연수 분류기다.

```java
// 리팩토링한 자연수 분류기

public class FClassifier {

    public static int sumOfFactors(int number) {
        Iterator<Integer> it = Factors.of(number).iterator();
        int sum = 0;
        while (it.hasNext())
            sum += it.next();
        return sum;
    }

    public static boolean inPerfect(int number) {
        return sumOfFactors(number) - number == number;
    }

    public static boolean isAbundant(int number) {
        return sumOfFactors(number) - number > number;
    }

    public static boolean isDeficient(int number) {
        return sumOfFactors(number) - number < number;
    }

}
```

두 번째 버전을 더 함수형으로 만들기 위해 특별한 라이브러리나 언어를 사용하지 않았음을 주시하라. 단지 코드의 재사용을 위해 커플링 대신에 합성을 사용하였다. 두 예제 모두 Factors 클래스를 사용하지만, 그 사용은 전적으로 개별 메서드에 포함된다.

커플링과 합성의 차이점은 작지만 중요하다. 이 예와 같은 간단한 경우에는 코드 구조물의 골격이 그대로 드러나는 것을 볼 수 있다. 하지만 복잡한 코드베이스를 리팩토링할 때는 객체지향 언어의 코드 재사용 방식인 이런 커플링이 여기저기 나타난다. 무성하게 커플링된 구조물들을 이해해야 하는 어려움 때문에 객체지향 언어에서는 코드 재사용이 피해를 많이 입었다. 그 결과 객체 관계 매핑(ORM) 이나 위젯 라이브러리처럼 특정 기술 도메인에서의 코드 재사용이 제약을 받게 되었다.

물론 IDE 에서 상위 클래스와 하위 클래스를 protected 멤버로 커플링하는 기능을 제공하므로 이를 이용하여 명령형 버전을 더 개선할 수도 있었다. 하지만 나는 커플링을 도입하기보다는 합성을 사용하고자 한다. 함수형 프로그래머로 생각한다는 것은 코딩의 모든 방면을 다르게 생각하는 것이다. 코드 재사용은 개발자에겐 당연한 목표이지만, 명령형 추상 개념은 함수형 프로그래머들이 사용하는 방법과는 다른 방법으로 이 문제를 해결하려 든다.

이 장 앞에서 디자인 패턴이 함수형 프로그래밍과 만나는 방법들의 윤곽을 살펴봤다. 첫째, 디자인 패턴은 언어나 런타임에 흡수될 수 있다. 팩토리, 전략, 싱글턴, 템플릿 메서드 패턴의 예를 통해서 이 점을 설명하였다. 둘째, 패턴들은 그 의미를 보존하면서 다른 방법으로 구현될 수 있다. 플라이웨이트 패턴을 클래스와 메모이제이션을 사용하여 구현한 예로 이 점을 설명하였다. 셋째, 함수형 언어와 런탕미은 전적으로 다른 기능을 가질 수 있고, 그것들을 사용하면 같은 문제라도 완전히 다른 방식으로 풀어나갈 수 있다.



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}
