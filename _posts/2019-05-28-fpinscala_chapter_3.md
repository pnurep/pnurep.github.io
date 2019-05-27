---
layout: single
categories:
  - Functional Programming
title: Functional Programming in Scala Chapter 3. 함수적 자료구조
author: Gold
author-email: pnurep@gmail.com
description: fpinscala_chapter_3
tags:
  - Functional Programming
  - FP
  - Scala
last_modified_at: 2019-05-28T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# 함수적 자료구조

함수형 프로그램은 변수를 갱신하거나 변경가능한 자료구조를 수정하는 일이 없다는 특징을 갖는다. 그렇다면 함수형 프로그래밍에서 사용할 수 있는 자료구조는 어떤 것이 있을까?

<br>

## 3.1 함수적 자료구조의 정의

함수적 자료구조란 오직 순수 함수만으로 조작되는 자료구조이다. 순수 함수는 자료를 그 자리에서 변경하거나 기타 부수효과를 수행하는 일이 없어야 한다. 따라서 함수적 자료구조는 정의에 의해 불변(immutable)하다. 예를 들어 빈 리스트는 정수 값 3 이나 4 처럼 영원히 불변값이다. 그리고 3 + 4 를 평가하면 3 과 4 가 변경, 수정되는 일 없이 새로운 정수 7 이 나오는 것 처럼, 두 개의 리스트를 연결하면 입력으로 받은 두 리스트는 변경되는 일 없이 새로운 목록이 만들어진다.

그런데 자료구조가 그런 식으로 조작된다면 여분의 복사가 많이 일어나 오버헤드가 발생하지 않을까? 답은 '그렇지 않다' 이다.


```scala
//단일 연결 리스트
sealed trait List[+A] // 타입 A 에 대해 매개변수화된 List 자료형식
case object Nil extends List[Nothing] // 빈 리스트를 나타내는 List 자료 생성자
case class Cons[+A](head: A, tail: List[A]) extends List[A] // 비어있지 않은 리스트를 나타내는 또 다른 자료생성자. tail 은 또 다른 List[A] 로, Nil 일 수도 있고, 다른 Cons 일 수도 있다.

// List 의 companion object. 리스트의 생성과 조작을 위한 함수들을 담는다.
object List {

    def sum(ints: List[Int]): Int = ints match {  // 패턴매칭을 이용해서 리스트의 정수들을 합하는 함수.
        case Nil => 0   // 빈 리스트의 합은 0
        case Cons(x, xs) => x + sum(xs) // x 로 시작하는 리스트의 합은 x 더하기 리스트의 나머지 부분의 합이다.
    }

    def product(ds: List[Int]): Double = ds match {
        case Nil => 1.0
        case Cons(0.0, _) => 0.0
        case Cons(x, xs) => x * product(xs)
    }

    def apply[A](as: A*): List[A] =     // 가변인수 함수 구문
        if (as.isEmpty) Nil
        else Cons(as.head, apply(as.tail: _*))

}
```

trait 를 사용해 List 를 정의한다. trait 앞에 sealed 를 붙이는 것은 이 특질의 모든 구현이 반드시 이 파일 안에 선언되어 있어야 함을 의미한다. case 키워드로 시작하는 두 줄은 List 의 두 가지 구현, 즉 두 가지 자료생성자 이다. 이것들은 List 가 취할 수 있는 두 가지 형태를 나타낸다. List 는 자료생성자 Nil 로 표현되는 빈 리스트일 수도 있고 자료생성자 Cons 로 표기되는 비어있지 않은 리스트일 수도 있다. 비어있지 않은 리스트는 초기요소 head 다음에 나머지 요소들을 담은 하나의 List(tail) 로 구성된다.

함수를 다형적으로 만들 수 있는것처럼, 자료구조도 다형적으로 만들 수 있다. sealed trait List 다음에 타입 파라미터 [+A] 를 두고 Cons 자료생성자 안에서 그 매개변수 A 를 사용함으로써, 리스트에 담긴 요소들의 타입에 대해 다형성이 적용되는 List 자료형식이 만들어진다. 그러면 하나의 동일한 정의로 Int 요소들의 리스트 List[Int], Double 요소들의 리스트 List[Double], String 요소들의 리스트 List[String] 등등을 사용할 수 있게 된다(타입 파라미터 변수 A 에 있는 + 는 공변(covariant)을 뜻한다).

<br>

---

__공변과 불변에 대해__

trait List[+A] 선언에서 타입 파라미터 A 앞에 붙은 + 는 A 가 List 의 __공변__(covariant) 파라미터임을 뜻하는 가변지정자(variance annotation)이다. 이것이 뜻하는 바는, 예를 들어, 만일 Dog 가 Animal 의 하위타입이면 List[Dog] 가 List[Animal] 의 하위타입으로 간주된다는 것이다. 조금 더 일반화 하자면, X 가 Y 의 하위타입이라는 조건을 만족하는 모든 타입 X 와 Y 에 대해, List[X] 는 List[Y] 의 하위타입이다. 반면에, 만일 A 앞에 + 가 없다면 그 타입 파라미터에 대해 List 는 __무공변__(invariant)이다.

그런데 Nil 이 List[Nothing] 을 확장함을 주목하자. Nothing 은 모든 타입의 하위형식이고 A 가 공변이므로 Nil 을 List[Int] 나 List[Double] 등 그 어떤 형식의 리스트로써도 간주할 수 있다.

---


<br>

자료생성자 선언은 해당 형태의 자료형식을 구축하는 함수를 도입한다. 다음은 자료생성자의 용례의 몇 가지이다.

```scala
val ex1: List[Double] = Nil
val ex2: List[Int] = Cons(1, Nil)
val ex3: List[String] = Cons("a", Cons("b", Nil))
```

```case object Nil``` 에 의해 ```Nil``` 이라는 표기를 이용해서 빈 List 를 생성할 수 있게 되며, ```case object Cons``` 는 ```Cons(1, Nil)```, ```Cons("a", Cons("b", Nil))``` 같은 표기를 통해서 임의의 길이의 단일 연결 리스트를 생성할 수 있게 한다. List 는 타입 A 에 대해 파라미터화 되어 있으므로, 이 함수들 역시 서로 다른 타입 A 에 대해 인스턴스화 되는 다형적 함수들이다. ex1 의 예는 Nil 이 List[Double] 의 타입으로 인스턴스화 된다는 점에서 흥미롭다. 이것이 허용되는 이유는, 빈 리스트에는 아무 요소도 없으므로 그 어떤 타입의 리스트로도 간주할 수 있기 때문이다. 각 자료생성자는 sum 이나 product 같은 함수들에서처럼 패턴매칭에 사용할 수 있는 패턴도 도입한다.

<br>

## 3.2 패턴매칭

object List 에 속한 함수 sum 과 product 는 List 의 동반객체(companion object) 라고 부르기도 한다. 두 함수 모두 패턴매칭을 활용한다.
동반객체는 자료형식과 같은 이름의 object 로, 자료형식의 값들을 생성하거나 조작하는 여러 편의용 함수들을 담는 목적으로 사용된다.

```scala
def sum(ints: List[Int]): Int = ints match {  // 패턴매칭을 이용해서 리스트의 정수들을 합하는 함수.
    case Nil => 0   // 빈 리스트의 합은 0
    case Cons(x, xs) => x + sum(xs) // x 로 시작하는 리스트의 합은 x 더하기 리스트의 나머지 부분의 합이다.
}

def product(ds: List[Int]): Double = ds match {
    case Nil => 1.0
    case Cons(0.0, _) => 0.0
    case Cons(x, xs) => x * product(xs)
}
```

sum 함수는 빈 리스트의 합이 0 이라고 말한다. 한편 비어있지 않은 리스트의 합은 첫 요소 x 에 나머지 요소들의 합 xs 를 더한 것이다. 마찬가지로 product 함수의 정의는 빈 리스트의 곱이 1.0 이고 0.0 으로 시작하는 임의의 리스트의 곱은 0.0, 그렇지 않은 비어있지 않은 리스트의 곱은 첫 요소에 나머지 요소들의 곱을 곱한 것이다. 이들이 제귀적인 정의임을 주목하자. List 같은 재귀적인 자료형식(Cons 생성자에서 자신을 재귀적으로 참조하고 있다)을 다루는 함수들을 작성할 때에는 이처럼 재귀적인 정의를 사용하는 경우가 많다.

패턴매칭은 switch 문과 비슷하게 작동한다. 패턴매칭 구문은 ```ds```(대상, target) 같은 표현식으로 시작해서 그 다음에 키워드 ```match``` 가 오고, 그 다음에 일련의 경우(case) 문들이 {} 로 감싸져있는 형태이다. target 이 case 문의 패턴과 __부합__하면 그 case 문의 결과가 전체 매칭표현식의 결과가 된다. 만일 대상과 매칭되는 패턴이 여러 개 일때는 처음으로 매칭된 case 문을 선택한다.

```scala
// 패턴매칭의 예
List(1, 2, 3) match { case _ => 42 }			// 42
List(1, 2, 3) match { case Cons(h, _) => h }	// 1
List(1, 2, 3) match { case Nil => 42 }			// MatchError
```

<br>

## 3.3 함수적 자료구조의 자료 공유

자료가 불변이라면, 리스트에 요소를 추가하거나 제거하는 함수는 어떻게 작성해야 할까? 답은 간단하다. 기존 리스트의 앞에 1 이라는 요소를 추가하려면 Cons(1, xs) 라는 새 리스트를 만들면 된다. 리스트는 불변이므로, xs 를 실제로 복사할 필요는 없다. 그냥 재사용하면 된다. 이를 __자료공유__(data sharing) 이라고 부른다. 이후의 코드가 지금 이 자료를 언제 어떻게 수정할지 걱정할 필요 없이, 항상 불변 자료구조를 돌려주면 된다. 자료가 변하거나 깨지지 않도록 방어적으로 복사본을 만들어 둘 필요가 없는 것이다. 마찬가지로, 리스트 ```myList = Cons(x, xs)``` 의 첫 요소를 제거하려면 그냥 목록의 tail 부분인 xs 를 돌려주면 된다. 실질적인 제거는 일어나지 않는다. 원래의 리스트는 여전히 사용 가능한 상태이다. 이를 두고 함수적 자료구조는 영속적이라고 말한다. 이는 자료구조에 연산이 가해져도 기존의 참조들이 결코 변하지 않음을 뜻한다.

<br>

### 3.3.1 자료 공유의 효율성

자료공유를 이용하면 연산을 좀 더 효율적으로 수행할 수 있는 경우가 많다. 자료공유의 더욱 놀라운 예로, 다음 함수는 한 리스트의 모든 요소를 다른 리스트의 끝에 추가한다.

```scala
def append[A](a1: List[A], a2: List[A]): List[A] = a1 match {
    case Nil => a2
    case Cons(x, xs) => Cons(x, append(xs, a2))
}
```

이 정의는 오직 첫 리스트가 다 소진될 때 까지만 값들을 복사한다. 따라서 이 함수의 실행시간과 메모리 사용량은 오직 a1 의 길이에만 의존한다. 리스트의 나머지는 a2 를 가리킬 뿐이다.

단일 연결 리스트의 구조 때문에, Cons 의 tail 을 치환할 때마다 반드시 이전의 모든 Cons 객체를 복사해야 한다(심지어 tail 이 리스트의 마지막 Cons 일 때에도). 서로 다른 연산들을 효율적으로 지원하는 순수 함수적 자료구조를 작성할 때의 관건은 자료공유를 현명하게 활용하는 방법을 찾아내는 것이다.

<br>

### 3.3.2 고차함수를 위한 형식 추론 개선

dropWhile 같은 고차함수에는 흔히 익명함수를 넘겨준다. dropWhile 의 시그니처는 아래와 같다.

```scala
def dropWhile[A](l: List[A], f: A => Boolean): List[A]
```

파라미터 A 에 익명함수를 지정해서 호출하기 위해서는 그 익명함수의 인수(아래의 x)의 타입을 명시해야 한다.

```scala
val xs: List[Int] = List(1, 2, 3, 4, 5)
val ex1 = dropWhile(xs, (x: Int) => x < 4)
```

ex1 의 값은 List(4, 5) 다. x 의 형식이 Int 임을 명시적으로 표기해야 한다는 것은 다소 번거롭다. dropWhile 의 첫 파라미터는 List[Int] 이므로, 두번째 파라미터의 함수는 반드시 Int 를 받아야 한다. 다음처럼 dropWhile 의 파라미터들을 두 그룹으로 묶으면 스칼라가 그러한 사실을 추론할 수 있다.

```scala
def dropWhile[A](l: List[A])(f: A => Boolean): List[A] = l match {
    case Nil => Nil
    case Cons(x, xs) if f(x) => dropWhile(xs, f)
    case _ => l
}
```

이 형태의 dropWhile 을 호출하는 구문은 dropWhile(xs)(f) 의 형태이다. 즉, dropWhile(xs) 는 하나의 함수를 돌려주며, 그 함수를 인수 f 로 호출한다(커링되었다). 파라미터들을 이런 식으로 묶는 것은 스칼라의 타입추론을 돕기 위한 것이다. 이제는 dropWhile 을 다음과 같이 타입설명 없이 호출할 수 있다.

```scala
val xs: List[Int] = List(1, 2, 3, 4, 5)
val ex1 = dropWhile(xs)(x => x < 4)
```

x 의 타입을 명시적으로 지정하지 않았음에 주목하자. 좀 더 일반화하자면, 함수 정의에 여러 개의 파라미터 그룹이 존재할 경우 타입정보는 왼쪽에서 오른쪽으로 흘러간다. 지금의 예에서 첫 번째 파라미터 그룹은 타입 파라미터 A 를 Int 로 고정시키므로, ```x => x < 4``` 에 대한 타입설명은 필요하지 않다. 이처럼, 타입추론이 최대로 일어나도록 함수 파라미터들을 적절한 순서의 여러 그룹으로 묶는 경우가 많다.

<br>

### 3.4 리스트에 대한 재귀와 고차함수로의 일반화

sum 과 product 의 구현을 다시 살펴보자

```scala
def sum(ints: List[Int]): Int = ints match {
    case Nil => 0
    case Cons(x, xs) => x + sum(xs)
}

def product(ds: List[Int]): Double = ds match {
    case Nil => 0.0
    case Cons(x, xs) => x * product(xs)
}
```

두 정의가 아주 비슷하다는 점에 주목하자. 둘은 각자 다른 타입을 다루지만 그 점만 제외한다면 두 함수의 유일한 차이점은 빈 리스트일 때의 반환값과 결과를 조합하는 데 쓰이는 연산(+, \*) 뿐이다. 이런 코드 중복을 발견했다면 부분 표현식들을 추출하여 함수인수로 대체함으로써 코드를 일반화하는 것이 항상 가능하다. 일반화된 함수는 빈 리스트일 때의 반환값과 비어있지 않은 리스트일 때의 결과에 요소를 추가하는 함수를 파라미터로 받는다.

```scala
def foldRight[A, B](as: List[A], z: B)(f: (A, B) => B): B = as match {
    case Nil => z
    case Cons(x, xs) => f(x, foldRight(xs, z)(f))
}

def sum2(ns: List[Int]) =
    foldRight(ns, 0)((x, y) => x + y)

def product2(ns: List[Double]) =
    foldRight(ns, 0.0)((x, y) => x * y)
```

foldRight 는 하나의 요소 타입에만 특화되지 않았다. 그리고 일반화 과정에서 우리는 이 함수가 돌려주는 값이 리스트의 요소와 같은 타입일 필요가 없다는 점도 알게 되었다.

<br>

### 3.4.2 단순 구성요소들로 리스트 함수를 조립할 때의 효율성 손실

List 의 한 가지 문제는, 어떤 연산이나 알고리즘을 아주 범용적인 함수들로 표현할 수 있기는 하지만 그 결과로 만들어진 구현이 항상 효율적이지는 않다는 점이다. 같은 입력을 여러 번 훑는 구현이 만들어질 수 있으며, 이른 종료를 위해서는 명시적인 재귀 루프를 작성해야 할 수도 있다.


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}