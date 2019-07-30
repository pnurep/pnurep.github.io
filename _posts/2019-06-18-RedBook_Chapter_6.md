---
layout: single
categories:
  - Functional Programming
title: Chapter6. 순수 함수적 상태
author: Gold
author-email: pnurep@gmail.com
description: RedBook_Chapter_6
tags:
  - Functional Programming
  - FP
  - Scala
last_modified_at: 2019-07-30T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br><br>

# 순수 함수적 상태

이번 장에서는 상태를 다루는 순수 함수적 프로그램을 작성하는 방법을 난수 발생 분야의 예를 이용해서 알아본다.

## 부수 효과를 이용한 난수발생

스칼라에서 난수를 발생할 때에는 표준 라이브러리의 클래스를 이용하면 된다. 이 클래스는 부수 효과에 의존하는 상당히 전형적인 명령식 API 를 제공한다.


```scala
scala> val rng = new scala.util.Random

scala> rng.nextDouble
res1: Double = 0.8466....

scala> rng.nextDouble
res2: Double = 0.983748...

scala> rng.nextInt
res3: Int = -62334785...

scala> rng.nextInt(10) // 0 이상 9 이하의 정수 난수를 얻는다
res4: Int = 4

```

Random 객체 안에서 일어나는 일을 알지 못한다고 해도, 난수발생기 객체 rng 에는 메서드 호출시마다 갱신되는 어떤 내부 상태가 존재한다고 가정할 수 있다. 그렇지 않다면 nextInt 나 nextDouble 을 호출할 때마다 같은 값을 얻게 될 것이기 때문이다. 상태 갱신은 부수 효과로서 수행되므로 이 메서드들은 참조투명하지 않다.


## 순수함수적 난수 발생

참조 투명성을 되찾는 관건은 상태갱신을 __명시적으로__ 드러내는 것이다. 즉, 상태를 부수효과로서 __갱신하지 말고__, 그냥 새 상태를 발생한 난수와 함께 돌려주면 된다. 예를 들어 발생기의 인터페이스를 이렇게 만들 수 있다.

```scala

trait RNG {
    def nextInt: (Int, RNG)
}

```

이 메서드는 무작위 Int 값을 생성해야 한다. 발생한 난수만 돌려주고 내부 상태를 제자리 변이를 통해서 갱신하는 대신, 이 인터페이스는 난수와 새 상태를 돌려주고 기존 상태는 수정하지 않는다. 이는 다음 상태를 계산하는 관심사와 새 상태를 프로그램의 나머지 부분에 알려주는 관심사를 분리하는 것에 해당한다. 

//함수적 난수 발생기 그림

```scala
case class SimpleRNG(seed: Long) extends RNG {
    def nextInt: (Int, RNG) = {
        val newSeed = (seed * 0x5DEECE66DL + 0xBL) & 0xFFFFFFFFFFFFL
        val nextRNG = SimpleRNG(newSeed)
        val n = (newSeed >>> 16).toInt
        (n, nextRNG)
    }
}
```

```scala
//repl 에서 사용한 예
scala> val rng = SimpleRNG(42)

scala> val (n1, rng2) = rng.nextInt
n1: Int = 16159453
rng2: RNG = SimpleRNG(1059025964525)

scala> val (n2, rng3) = rng2.nextInt
n2: Int = -1281479697
rng3: RNG = SimpleRNG(197491923327988)

```

이 예를 여러 번 되풀이해서 실행해도 항상 같은 값들이 나온다. rng.nextInt 를 호출하면 항상 16159453 과새 RNG 를 돌려주며, 그 RNG 의 nextInt 는 항상 -1281479697 을 돌려준다. 다시 말해서 이 API 는 순수하다.


## 상태 있는 API 를 순수하게 만들기

상태있는 API 를 순수하게 만드는 문제와 그 해법(API 가 실제로 뭔가를 변이하는 대신 다음 상태를 __계산__ 하게 하는 것)이 난수 발생에만 국한되는 것은 아니며 항상 동일한 방식으로 해결 할 수 있다.


예를 들어 다음과 같은 클래스가 있다고 하자.

```scala
class Foo {
    private var s: FooState = ...
    def bar: Bar
    def baz: Int
}
```

bar 와 baz 가 각각 나름의 방식대로 s 를 변이한다고 하자. 한 상태에서 다음 상태로의 전이를 명시적으로 드러내는 과정을 기계적으로 진행해서 이 API 를 순수 함수적 API 로 변환할 수 있다.

```scala
trait {
    def bar: (Bar, Foo)
    def baz: (Int, Foo)
}
```

이 패턴은 계산된 다음 상태를 프로그램의 나머지 부분에 전달하는 책임을 호출자에게 지우는 것에 해당한다. 앞에서 본 순수 RNG 인터페이스에서는 만일 이전의 RNG 를 재사용한다면 이전에 발생한 것과 같은 값을 낸다. 예를 들어 다음 코드에서 i1 과 i2 는 같다.

```scala
def randomPair(rng: RNG): (Int, Int) = {
    val (i1, _) = rng.nextInt
    val (i2, _) = rng.nextInt
    (i1, i2)
}
```

서로 다른 두 수를 만드려면, 첫 nextInt 호출이 돌려준 RNG 를 이용해서 둘째 Int 를 발생해야 한다.

```scala
def randomPair(rng: RNG): ((Int, Int), RNG) = {
    val (i1, rng2) = rng.nextInt
    val (i2, rng3) = rng2.nextInt // 여기서는 rng2 를 사용한다.
    ((i1,i2), rng3) // 난수 두 개를 발생한 후 최종상태를 돌려준다. 이렇게 하면 호출자는 새 상태를 이용해서 난수를 더 많이 발생할 수 있다.
}
```


### 함수형 프로그래밍의 어색함 극복
함수적 프로그램을 작성하다 보면 프로그램을 함수적으로 표현하는 것이 지금처럼 어색하거나 지겨운 상황에 처하기도 한다. 이런 어색함은 아직 무언가를 덜 추상화했기 때문에 비롯된 것이다. 이런상황을 만났다면 코드를 좀 더 살펴보면서 추출할 수 있는 공통패턴을 찾는것이 좋다. 



## 상태 동작을 위한 더 나은  API

앞의 구현들을 다시 살펴보면 모든 함수가 어떤 타입 A 에 대해 RNG => (A, RNG) 형태의 타입을 사용한다는 공통의 패턴을 발견할 수 있다. 한 RNG 상태를 다른 RNG 상태로 변환한다는 점에서, 이런 종류의 함수를 __상태동작(state action)__ 또는 __상태전이(state transition)__ 라고 부른다. 이 상태동작들은 조합기(combinator) 라 불리는 고차함수를 이용해 조합할 수 있다. 상태를 호출자가 직접 전달하는 것은 지루하고 반복적이므로, 조합기가 자동으로 한 동작에서 다른 동작으로 상태를 넘겨주게 하는 것이 바람직하다. 상태동작의 형식을 편하게 지칭하고 형식에 대한 고찰을 단순화하기 위해, 먼저 RNG 상태 동작 자료 형식에 대한 alias 를 만들어두자.

```scala
type Rand[+A] = RNG => (A, RNG)
```

이것은 하나의 상태동작, 즉 어떤 RNG 에 의존하며, 그것을 이용해서 A 를 생성하며, 또한 RNG 를 다른 동작이 이후에 사용할 수 있는 새로운 상태로 전이하는 하나의 프로그램이다. 

이제 RNG 의 nextInt 같은 메서드를 이 새로운 형식의 값으로 만들 수 있다.

```scala
val int: Rand[+A] = _.nextInt
```

Rand 동작들을 조합하되 RNG 상태들을 명시적으로 전달하지 않아도 되는 조합기를 작성하는 것이 가능하다. 예를 들어 다음은 주어진 RNG 를 사용하지 않고 그대로 전달하는, 가장 간단한 형태의 RNG 상태 전이인 unit 동작이다.

```scala
def unit[A](a: A): Rand[A] = 
    rng => (a, rng)
```

또한, 한 상태동작의 출력을 변환하되 상태 자체는 수정하지 않는 map 도 생각할 수 있다. 

```scala
def map[A, B](s: Rand[A])(f: A => B): Rand[B] = 
    rng => {
        val (a, rng2) = s(rng)
        (f(a), rng2)
    }
```

map 의 용법을 보여주는 예로, nonNegativeInt 를 재사용해서 0 보다 크거나 같고 2 로 나누어지는 Int 를 발생하는 함수 nonNegativeEven 이다.

```scala
def nonNegativeEven: Rand[Int] = 
    map(nonNegetiveInt)(i => i - i % 2)
```

### 상태 동작들의 조합

위의 map 이 intDouble 과 doubleInt 를 구현할 정도로 강력하지는 않다. 여기에 필요한 것은 두 RNG 동작을 단항 함수가 아니라 이항 함수로 조합하는 새로운 조합기 map2 이다.

```scala
//연습문제 6.6
def map2[A, B, C](ra: Rand[A], rb: Rand[B])(f: (A, B) => C): Rand[C]
```

map2 를 한 번만 작성해 두면 이를 이용해서 임의의 RNG 상태 동작들을 조합할 수 있다. 예를 들어, A 타입의 값을 발생하는 동작과 B 타입의 값을 발생하는 동작이 있다면, 그 둘을 조합해서 A 와 B 의 쌍을 발생하는 동작을 얻을 수 있다.

```scala
def both[A, B](ra: Rand[A], rb: Rand[B]): Rand[(A, B)] = 
    map2(ra, rb)((_, _))
```

이제 이를 이용해서 intDouble 과 doubleInt 를 좀 더 간결하게 구현할 수 있다.

```scala
def randIntDouble: Rand[(Int, Double)] = 
    both(int, double)

def randDoubleInt: Rand[(Double, Int)] = 
    both(double, int)
```

### 내포된 상태 동작

지금까지의 예에서 하나의 패턴이 보이는데, 이를 잘 추출한다면 RNG 값을 명시적으로 언급하거나 전달하지 않는 구현이 가능하다. map, map2 덕분에 함수들을 좀 더 간결하고 우아하게 구현할 수 있게 되었다. 그러나 map 과 map2 로는 그리 잘 작성할 수 없는 함수들도 있다. 그런 함수 중 하나가 0 이상, n 미만의 정수 난수를 발생하는 nonNegativeLessThan 이다.

가장 먼저 떠오르는 구현 방법은 음이 아닌 정수 난수를 n 으로 나눈 나머지를 돌려주는 것이다.
```scala
def nonNegativeLessThan(n: Int): Rand[Int] = 
    map(nonNegativeInt) { _ % n }
```

이렇게 하면 요구된 범위의 난수가 발생하긴 하지만, Int.MaxValue 가 n 으로 나누어 떨어지지 않을 수도 있으므로 전체적으로 난수들이 치우치게 된다. 따라서, nonNegativeInt 가 32 비트 정수를 벗어나지 않는 n 의 최대 배수보다 큰 수를 발생했다면 더 작은 수가 나오길 바라면서 발생기를 __다시 시도__ 해야한다. 다음이 이를 반영한 구현이다.

```scala
def nonNegativeLessThan(n: Int): Rand[Int] = 
    map(nonNegativeInt) { i =>
        val mod = i % n
        if (i + (n-1) - mod >= 0) mod else nonNegativeLessThan(n)(???) //Int 가 32 비트 Int 를 벗어나지 않는 n 의 최대 배수보다 크면 난수를 재귀적으로 다시 발생한다.
    }
```

올바른 방향으로 가고 있긴 하지만, nonNegativeLessThan(n) 의 형식이 그 자리에 맞지 않는다는 점이 문제다. 이 함수는 Rand[Int] 를 돌려주어야 하며, 그것은 RNG 하나를 인수로 받는 __함수__ 임을 기억하기 바란다. 그런데 지금은 그런 함수가 없다. 이를 해결하려면 nonNegativeInt 가 돌려준 RNG 가 nonNegativeLessThan 에 대한 재귀적 호출에 전달되도록 어떤식으로든 호출들을 연결해야 한다. 한 가지 방법은 다음처럼 map 을 사용하지 않고 명시적으로 전달하는 것이다.

```scala
def nonNegativeLessThan(n: Int): Rand[Int] = { rng => 
    val (i, rng2) = nonNegativeInt(rng)
    val mod = i % n
    if (i +(n-1) - mod >= 0)
        (mod, rng2)
    else nonNegativeLessThan(n)(rng2)
}
```

그러나 이러한 전달을 처리해 주는 조합기가 있다면 더 좋을 것이다. map 이나 map2 는 그런 조합기가 아니다. 필요한 것은 좀 더 강력한 조합기인 flatMap 이다.

flatMap 을 이용하면 Rand[A] 로 무작위 A 를 발생하고 그 A 의 값에 기초해서 Rand[B] 를 선택할 수 있다. nonNegativeLessThan  에서는 nonNegativeInt 가 생성한 값에 기초해서 재시도 여부를 선택하는 데 flatMap 을 이용했다.

이제 처음에 나온 예제를 다시 살펴보자. 순수 함수적 API 를 이용해서 검사성이 좀 더 좋은 주사위 굴림 함수를 만들 수 있을까?

다음은 nonNegativeLessThan 을 이용한 rollDie 의 구현으로, 이전 버전에 있던 '하나 모자라는 오류' 는 여전히 남아있다.
```scala
def rollDie: Rand[Int] = nonNegativeLessThan(6)
```

이 함수를 여러 RNG 상태들로 검사해보면, 함수의 반환값이 0 이 되는 RNG 를 상당히 빨리 발견할 수 있다.
그리고 동일한 SimpleRNG(5) 난수 발생기를 이용하면 이러한 실패 상황을 신뢰성 있게 재현할 수 있다. 이제는 상태가 사용 후 파괴되는 것을 걱정할 필요가 없다. 이 버그는 간단히 교정된다.

```scala
def rollDie: Rand[Int] = map(nonNegativeLessThan(6))(_ + 1)
```


## 일반적 상태 동작 자료 형식

지금까지 작성한 함수들은 사실 난수 발생에만 국한된 것이 전혀 아니다. 이들은 상태 동작에 대해 작용하는 범용 함수들로, 상태의 구체적인 종류는 신경 쓰지 않는다. 예를 들어 map 은 자신이 RNG 상태 동작을 다루는지 상관하지 않으며, 따라서 이 함수에 다음과 같은 좀 더 일반적인 서명을 부여할 수 있다.
```scala
def map[S, A, B](a: S => (A, S))(f: A => B): S => (B, S)
```

서명을 이렇게 바꾸어도 map 의 구현은 수정할 필요가 없다! 우리가 보지 못했을 뿐 좀 더 일반적인 서명이 처음부터 있었던 것이다.
이제 임의의 상태를 처리할 수 있는, Rand 보다 더 일반적인 형식을 생각해 보자.
```scala
type State[S, +A] = S => (A,S)
```

여기서 State 는 __어떤 상태를 유지하는 계산__ 즉 __상태 동작__ 또는 __상태 전이__ 를 대표한다. 심지어 __명령문__ 을 대표한다고 할 수도 있다. 이를, 다음과 같이 함수를 감싼 독립적인 클래스의 형태로 작성해도 좋을 것이다.
```scala
case class State[S, +A](run: S => (A, S))
```

어떤 형태인지가 아주 중요한 것은 아니다. 중요한 것은 이제 상태있는 프로그램의 공통 패턴들을 갈무리 하는 범용함수를 작성하는데 사용할 수 있는 단일한, 범용적인 형식이 생겼다는 점이다. 마지막으로 이제 Rand 는 그냥 State 의 형식 별칭으로 두면 된다.
```scala
type Rand[A] = State[RNG, A]
```


## 순수 함수적 명령형 프로그래밍

이전에서 우리는 특정한 패턴을 따르는 함수들을 작성했다. 상태 동작을 실행하고, 그 결과를 val 에 배정하고, 그 val 을 사용하는 또 다른 상태 동작을 실행하고, 그 결과를 또 다른 val 에 배정하는 등으로 이어졌다. 그런데 그런 방식은 __명령형__ 프로그래밍과 아주 비슷하다. 명령형 프로그래밍 패러다임에서 하나의 프로그램은 일련의 명령문들로 이루어지며, 각 명령문은 프로그램의 상태를 수정할 수 있다. 앞에서 한 것이 바로 그런 방식이다. 단, 실제로는 명령문이 아니라 상태동작 State 이고, 이것은 사실 함수이다. 함수로서의 상태동작은 그냥 인수를 받음으로써 현재 프로그램 상태를 읽고, 그냥 값을 돌려줌으로써 프로그램 상태를 수정한다.

__명령형 프로그래밍과 함수형 프로그래밍은 상극이 아닌가?__
절대 아니다. 함수형 프로그래밍은 단지 사이드이펙트가 없는 프로그래밍일 뿐임을 기억하자. 명령형 프로그래밍은 일부 프로그램 상태를 수정하는 명령문들로 프로그램을 만드는 것이고, 앞에서 보았듯이 부수효과 없이 상태를 유지, 관리 하는것도 전적으로 타당하다. 함수형 프로그래밍은 명령형 프로그래밍의 작성을 아주 잘 지원한다. 게다가, 참조 투명성 덕분에 그런 프로그램을 등식적으로 추론할 수 있다는 추가적인 장점도 있다.

map 이나 map2 같은 조합기를 구현했고, 결국에는 한 명령문에서 다음 명령문으로의 상태 전파를 처리하는 flatMap 에 까지 도달했다. 그런데 그 과정에서 명령형 프로그램의 성격은 많이 사라졌다.
다음 예를 생각해 보자(Rand[A] 는 State[RNG, A] 의 형식별칭이라고 가정한다)

```scala
val ns: Rand[List[Int]] = 
    int.flatMap(x => //int 는 하나의 정수 난수를 발생하는 Rand[Int] 형식의 값이다
        int.flatMap(y => 
            ints(x).map(xs => //ints(x) 는 길이가 x 인 목록을 생성한다
                xs.map(_ % y)))) // 목록의 모든 요소를 y 로 나눈 나머지로 치환한다
```

그런데 이 코드는 작동방식을 이해하기가 좀 어렵다. 다행히 map 과 flatMap 이 정의되어 있으므로, for-함축을 이용해서 명령형 스타일을 복구할 수 있다.

```scala
val ns: Rand[List[Int]] = for {
    x <= int    // 정수 난수 x 를 발생한다
    y <- int    // 또 다른 정수 난수 y 를 발생한다
    xs <- ints(x)    // 길이가 x 인 목록 xs 를 생성한다
} yield xs.map(_ % y)    // xs  의 각 요소를 y 로 나눈 나머지로 치환한 목록을 돌려준다
```

이 코드는 읽기 쓰기가 훨씬 쉽다. 이 코드가 어떤 상태를 유지하는 명령형 프로그램이라는 점이 코드의 형태 자체에 잘 반영되어 있기 때문이다. 그리고 이는 이전과 같은 코드이다.
for-함축(flatMap) 을 활용하는 이런 종류의 명령형 프로그래밍을 지원하는 데 필요한 것은 기본적인 State 조합기 두 개 뿐이다. 상태를 읽는 조합기와 상태를 쓰는 조합기만 있으면 된다. 현재 상태를 얻는 조합기가 get 이고 새 상태를 설정하는 조합기가 set 이라고 할때, 상태를 임의의 방식으로 수정하는 조합기를 다음과 같이 구현할 수 있다.
```scala
def modify[S](f: S => S): State[S, Unit] = for {
    s <- get    // 현재 상태를 얻어서 s 에 배정한다
    _ <- set(f(s))    //  새 상태를 s 에 f 를 적용한 결과로 설정한다
} yield ()
```

이 메서드는 주어진 상태를 함수 f 로 수정하는 State 동작을 돌려준다. 그리고 상태 이외의 반환값이 없음을 알리기 위해 Unit 을 산출한다. get 동작은 그냥 입력 상태를 전달하고 그것을 반환값으로 돌려준다.

```scala
def get[S]: State[S, S] = State(s => (s, s))
```

set 동작은 새 상태 s 를 받는다. 결과로 나오는 동작은 입력상태를 무시하고 그것을 새 상태로 치환하며, 의미있는 값 대신 () 를 돌려준다.

```scala
def set[S](s: S): State[S, Unit] = State(_ => ((), s))
```

이 두 간단한 동작과 이전에 작성한 State 조합기들만 있으면 그 어떤 종류의 상태기계나 상태있는 프로그램이라도 순수함수적 방식으로 구현할 수 있다.


## 요약

이번 장에서는 상태를 가진 프로그램을 순수 함수적 방식으로 작성하는 주제를 건드려 보았다. 동기부여 예제로 난수 발생기를 사용했지만, 전반적인 패턴은 다른 여러 분야에서도 등장한다. 착안은 간단하다. 상태를 인수로 받고 새 상태를 결과와 함께 돌려주는 순수함수를 사용한다는 것이다. 나중에 부수효과에 의존하는 명령식 API 를 만나게 되면 그것을 순수함수적 버전으로 바꿀 수 있는지 시도해 보기 바란다. 이번 장에서 작성한 함수들을 활용하면 그러한 변환이 훨씬 쉬울 것이다.


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}

