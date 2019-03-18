---
layout: single
categories:
  - Functional Programming
title: Functor, Applicative, Monad
author: Gold
author-email: pnurep@gmail.com
description: Functor, Applicative, Monad 에 대해 알아봅니다.
tags:
  - Functional Programming
  - Functor
  - Applicative
  - Monad
  - Kotlin
last_modified_at: 2019-03-18T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---



<br><br>

본 문서는 Functional Kotlin(packt) 의 번역입니다

<br>

# Functor, Applicative, Monad

펑터, 어플리케이티브, 모나드는 함수형 프로그래밍과 관련된 단어중에서 가장 많이 검색되는 단어들 중 하나인데, 이것들의 의미를 아무도 모른다고 생각해 본다면 꽤나 납득이 됩니다(사실은 아니죠, 이 단어들이 의미하는 바가 무엇인지 정확히 아는 똑똑한 사람들은 존재합니다).

특히, 모나드에 대한 혼란은 프로그래밍 커뮤니티에서 조크 혹은 밈이 되었습니다.

> "모나드는 엔도펑터 카테고리 안의 모노이드야. 뭐가 문제지?"


<br>


## 펑터

이미 당신이 코틀린에서 펑터를 사용했다고 한다면 어떨까요? 놀랍나요? 아래의 코드를 봅시다.
```kotlin
fun main(args: Array<String>) {
	listOf(1, 2, 3)
    	.map{ i -> i * 2 }
        .map(Int::toString)
        .forEach(::println)
}
```


```List<T>``` 클래스는 ```map(transform: (T) -> R): List<R>``` 함수를 갖고 있습니다. ```map``` 이라는 이름은 어디에서 온걸까요? 그 이름은 바로 카테고리 이론에서 왔습니다. 우리가 ```Int``` 를 ```String``` 으로 변환할때 우리가 하는 것은 바로, ```Int``` 카테고리를 ```String``` 카테고리로 맵핑하는 것입니다. 예제 코드 안에서 같은 맥락으로 보자면, 우리는 ```List<Int>``` 를 ```List<Int>``` 로 변환하고(그리 유쾌하지는 않지만), 다시 ```List<Int>``` 를 ```List<String>``` 으로 변환합니다. 우리는 외부의 타입을 바꾸지 않았습니다. 단지 내부의 값만 바꾸었죠.

그리고 이게 펑터입니다. <b>펑터</b>는 자신의 내용물을 변환시키거나 맵핑할수 있는 방법을 정의하는 타입입니다. 아마 당신은 펑터에 대한 다른, 좀 더 학술적인 정의를 찾을 수 있을겁니다. 하지만 원칙적으로, 다 같은 방향을 가리키고 있습니다.

펑터타입에 대한 제네릭 인터페이스를 정의해 봅시다.
```kotlin
interface Functor<C<_>> {  // 코틀린 코드에서는 불가함
	fun <A, B> map(ca: C<A>, transform: (A) -> B): C<B>
}
```

위의 코드는 컴파일이 되지 않습니다. 코틀린은 Higher-kinded Type 을 지원하지 않기 때문이죠.


__스칼라__ 나 __하스켈__ 같은 Higher-Kinded Type 을 지원하는 언어에서는 ```Functor``` 타입을 정의하는 것이 가능합니다.

에를 들어, 스칼라 Cats 라이브러리의 펑터:
```scala
trait Functor[F[_]] extends Invariant[F] { self =>
	def map[A, B](fa: F[A])(f: A => B): F[B]
    // 생략...
}
```

코틀린에선 그런 기능이 없습니다. 하지만 컨벤션에 따라서 시뮬레이트 해볼수는 있습니다. 만약 타입이 함수나 확장함수를 갖는다면, ```map``` 이 펑터입니다(이것은 <b>구조적 타이핑(Structural Typing)</b>이라 불리며, 계층구조 보다는 구조에 의해 정의되는 타입입니다).


간단한 ```Option``` 타입을 만들어 봅시다:
```kotlin
sealed class Option<out T> {
	object None : Option<Nothing>() {
    	override fun toString() = "None"
    }

    data class Some<out T>(val value: T) : Option<T>()

    companion object

}
```

그러면, 이것을 위한 ```map``` 함수를 정의할 수 있습니다:
```kotlin
fun <T, R> Option<T>.map(transform: (T) -> R): Option<R> = when(this) {
	Option.None -> Option.None
    is Option.Some -> Option.Some(transform(value))
}
```

그리고 다음과 같이 사용합니다:
```kotlin
fun main(args: Array<String>) {
	println(Option.Some("Kotlin")
    		.map(String::toUpperCase)) // Some(value=KOTLIN)
}
```

이제, ```Option``` 값은 ```Some``` 과 ```None``` 에 대해 다르게 동작할 것입니다:
```kotlin
fun main(args: Array<String>) {
	println(Option.Some("Kotlin").map(String::toUpperCase)) //Some(value=KOTLIN)
    println(Option.None.map(String::toUpperCase)) //None
}
```

확장함수는 굉장히 유연해서, 함수타입 ```(A) -> B``` 에 대한 ```map``` 함수를 작성할 수 있으므로 함수를 펑터로 변환시킬 수 있습니다.
```kotlin
fun <A, B, C> ((A) -> B).map(transform: (B) -> C): (A) -> C = { t -> transform(this(t)) }
```

여기서 우리가 변화시키는 것은 함수 ```(A) -> B``` 의 결과에 파라미터 함수 ```transform: (B) -> C``` 를 적용한 B 에서 C 로의 리턴타입 입니다.


만약 다른 함수형 프로그래밍 언어에서 경험이 있는 경우, 이 동작을 정방향 함수 합성(Forward Function Composition)으로 생각하면 됩니다.



<br><br>



## 모나드

<b>모나드</b>는 동일한 타입을 반환하는 람다를 받는 ```flatMap```( 또는 ```bind```, 다른 언어들에서) 함수를 정의하는 펑터 타입입니다.

예제와 함께 설명하겠습니다. 운이 좋게도, ```List<T>``` 는 ```flatMap``` 함수를 정의하고 있습니다.
```kotlin
fun main(args: Array<String>) {
	val result = listOf(1, 2, 3)
    				.flatMap{ i -> listOf(i * 2, i + 3) }
                    .joinToString()

    println(result) //2, 4, 4, 5, 6, 6
}
```

```map``` 함수에서, 우리는 ```List``` 값의 내용을 변환시켰습니다. 하지만 ```flatMap``` 에서는 새로운 ```List``` 타입을 더 많거나 더 적은 아이템 갯수로 반환할 수 있으므로, ```map``` 함수보다 더 강력합니다.


일반적인 모나드는 이렇게 생겼습니다(코틀린에서는 Higher-Kinded Type 을 쓸 수 없다는 것을 기억하세요)
```kotlin
interface Monad<C<_>>: Functor<C> {  // Invalid
	fun <A, B> flatMap(ca: C<A>, fm:(A) -> C<B>): C<B>
}
```


이제, 우리는 ```flatMap``` 함수를 우리가 정의한 ```Option``` 타입에 사용할 수 있습니다:
```kotlin
fun <T, R> Option<T>.flatMap(fm: (T) -> Option<R>): Option<R> = when(this) {
	Option.None -> Option.None
    is Option.Some -> fm(value)
}
```


조금더 자세히 본다면, ```flatMap``` 함수와 ```map``` 함수가 굉장히 비슷하다는 것을 알 수 있을겁니다. 굉장히 비슷해서 ```map``` 함수를 ```flatMap``` 함수를 사용해 다시 작성해 볼 수도 있습니다.
```kotlin
fun <T, R> Option<T>.map(transform: (T) -> R): Option<R> = flatMap { t -> Option.Some(transform(t)) }
```


그리고 이제 우리는 ```map``` 함수로는 불가능했던 것을 ```flatMap``` 함수의 힘을 사용해 멋지게 해결해볼 것입니다.

```kotlin
fun calculateDiscount(price: Option<Double>): Option<Double> {
	return price.flatMap { p ->
    	if (p > 50.0)
        	Option.Some(5.0)
        else
        	Option.None
    }
}

fun main(args: Array<String>) {
	println(calculateDiscount(Option.Some(80.0))) //Some(value=5.0)    			println(calculateDiscount(Option.Some(30.0))) //None   						println(calculateDiscount(Option.None)) //None
}
```

우리가 작성한 calculateDiscount 함수를 보면, ```Option<Double>``` 을 받고 또 반환하고 있습니다. price 가 50.0 보다 높으면 할인금액으로 ```Some``` 에 래핑한 5.0 을, 그보다 낮다면 ```None``` 을 리턴합니다.


```flatMap``` 을 사용하는 멋진 트릭 하나는 중첩될 수 있다는 것 입니다.

```kotlin
fun main(args: Array<String>) {
	val maybeFive = Option.Some(5)
    val maybeTwo = Option.Some(2)

    println(maybeFive.flatMap { f ->
    	maybeTwo.flatMap { t ->
        	Option.Some(f + t)
        }
    })	// Some(value=7)
}
```

안쪽의 ```flatMap``` 함수에서, 우리는 두 값에 대해 넘나들며 접근하고 연산할 수 있습니다.


```flatMap``` 함수와 ```map``` 함수를 결합하는 것으로 이 예제를 조금 더 짧게 만들수 있습니다.
```kotlin
fun main(args: Array<String>) {
	val maybeFive = Option.Some(5)
    val maybeTwo = Option.Some(2)

    println(maybeFive.flatMap{ f ->
    	maybeFive.map { f ->
        	f + t
        }
    })  //Some(value=7)
}
```


이와 같이, 우리가 작성한 첫번째 ```flatMap``` 함수의 예제를 두 개의 리스트의 결합으로 재작성 해볼 수 있습니다.
```kotlin
fun main(args: Array<String>) {
	val numbers = listOf(1, 2, 3)
    val functions = listOf<(Int) -> Int>({ i -> i * 2 }, { i -> i + 3 })
	val result = numbers.flatMap { number ->
    	functions.map { f -> f(number) }
    }.joinToString()

    println(result) //2, 4, 4, 5, 6, 6
}
```


여러개의 중첩 ```flatMap``` 혹은 ```flatMap``` 과 ```map``` 함수의 결합은 매우 강력하며 모나드 내포(Monadic Comprehension) 라는 모나딕한 연산들을 결합할 수 있게 해주는 또 다른 개념의 기본 아이디어 입니다.



<br><br>



## 어플리케이티브

동일한 종류의 래퍼 안에서, 파라미터를 갖는 래퍼의 내부에서 람다를 호출하는 것은 어플리케이티브를 소개하는데에 완벽한 방법일 것입니다.

<b>어플리케이티브</b>는 어플리케이티브 타입으로 래핑된 T 를 반환하는 함수 ```pure(t: T)``` 와 어플리케이티 타입으로 래핑된 람다를 받는 ```ap``` 함수(```apply```, 다른 언어들에서) 두 가지의 함수로 정의된다.

이전 섹션에서, 모나드에 대해 설명할때, 우리는 펑터로 부터 직접 상속을 받게 했습니다. 하지만 실제로는, 모나드는 어플리케이티브를 확장하고, 어플리케이티브는 펑터를 확장합니다. 그러므로, 일반적인 어플리케이티브를 위한 우리의 의사코드와 전체적인 계층구조는 아래와 같습니다.
```kotlin
interface Functor<C<_>> { //Invalid
	fun <A, B> map(ca: C<A>, transform: (A) -> B): C<B>
}

interface Applicative<C<_>>: Functor<C> { //Invalid
	fun <A> pure(a: A): C<A>

    fun <A, B> ap(ca: C<A>, fab: C<(A) -> B>): C<B>
}

interface Monad<C<_>>: Applicative<C> {  //Invalid
	fun <A, B> flatMap(ca: C<A>, fm: (A) -> C<B>): C<B>
}
```


간단히 말해서, 어플리케이티브는 더 강력한 펑터이고, 모나드는 더 강력한 어플리케이티브 입니다.


이제, ```List<T>``` 에 대한 확장함수 ```ap``` 를 작성해 봅시다.
```kotlin
fun <T, R> List<T>.ap(fab: List<(T) -> R>): List<R> = fab.flatMap { f -> this.map(f) }
```

Monads 섹션에서 마지막 예제를 다시 살펴 보겠습니다.
```kotlin
fun main(args: Array<String>) {
	val numbers = listOf(1, 2, 3)
    val functions = listOf<(Int) -> Int>({ i -> i * 2 }, { i -> i + 3 })
    val result = numbers.flatMap { number ->
    	functions.map { f -> f(number) }
    }.joinToString()

    println(result) //2, 4, 4, 5, 6, 6
}
```

위의 예제를 ```ap``` 함수로 다시 작성해봅시다.
```kotlin
fun main(args: Array<String>) {
	val numbers = listOf(1, 2, 3)
    val functions = listOf<(Int) -> Int>({ i -> i * 2 }, { i -> i + 3 })
	val result = numbers.ap(functions)
    					.joinToString()

    println(result) //2, 4, 6, 4, 5, 6
}
```

읽기 쉬워졌으나, 주의할 점이 있습니다. - 결과값의 정렬이 달라졌습니다. 우리는 우리의 특정 경우에 적합한 옵션을 알고 선택해야 합니다.

우리는 ```pure``` 와 ```ap``` 를 ```Option``` 클래스에 추가할 수 있습니다.
```kotlin
fun <T> Option.Companion.pure(t: T): Option<T> = Option.Some(t)
```

```Option.pure``` 는 ```Option.Some``` 생성자에 대한 간단한 alias 입니다.


우리의 ```Option.ap``` 함수는 매력적입니다.
```kotlin
//Option
fun <T, R> Option<T>.ap(fab: Option<(T) -> R>): Option<R> = fab.flatMap { f -> map(f) }

//List
fun <T, R> List<T>.ap(fab: List<(T) -> R>): List<R> = fab.flatMap{ f -> this.map(f) }
```

```Option.ap```와 ```List.ap``` 는 ```flatMap``` 과 ```map``` 의 결합을 사용한 같은 바디를 가지며, 그것은 정확하게 모나드 연산을 결합하는 방식입니다.


모나드로, ```flatMap``` 과 ```map``` 을 사용해 두개의 ```Option<Int>``` 를 더했습니다.
```kotlin
fun main(args: Array<String>) {
	val maybeFive = Option.Some(5)
    val maybeTwo = Option.Some(2)

    println(maybeFive.flatMap { f ->
    	maybeTwo.map { t -> f + t }
    }) //Some(value=7)
}
```

이제, 어플리케이티브를 사용해서:
```kotlin
fun main(args: Array<String>) {
	val maybeFive = Option.pure(5)
    val maybeTwo = Option.pure(2)

    println(maybeTwo.ap(maybeFive.map{ f -> { t: Int -> f + t } })) //Some(value=7)
}
```

읽기가 쉽지 않습니다. 먼저, ```maybeFive``` 를 람다 ```(Int) -> (Int) -> Int``` 로 맵핑한 후, ```maybeTwo.ap``` 의 파라미터로 전달할 수 있는 ```Option<(Int) -> Int>``` 를 반환합니다.


우리는 약간의 트릭을 사용해 가독성을 높일 수 있습니다(하스켈에서 차용한).
```kotlin
infix fun <T, R> Option<(T) -> R>.`(*)`(o: Option<T>): Option<R> = flatMap { f: (T) -> R -> o.map(f) }
```

```infix``` 확장함수 ``` Option<(T) -> R>.`(*)` ``` 을 통해 ```sum``` 연산을 왼쪽에서 오른쪽으로 읽을 수 있습니다. 멋지지 않나요? 이제 아래의 코드를 봅시다. 어플리케이티브를 사용한 두개의 ```Option<Int>``` 의 합입니다.
```kotlin
fun main(args: Array<String>) {
	val maybeFive = Option.pure(5)
    val maybeTwo = Option.pure(2)

    println(Option.pure { f: Int -> { t: Int -> f + t } } `(*)` maybeFive `(*)` maybeTwo) //Some(value=7)
}
```

```pure``` 함수로 ```(Int) -> (Int) -> Int``` 람다를 감싸고 ```Option<Int>``` 를 하나씩 적용합니다. 하스켈의 ```<*>``` 연산자에 대한 경의의 의미로 ``` `(*)` ``` 라는 이름을  사용합니다.


지금까지, 어플리케이티브를 통해 멋진 트릭을 사용할 수 있는것을 보았습니다. 하지만 모나드는 더욱 강력하고 유연합니다. 언제 둘 중 하나를 사용할까요? 그건 당신의 어떤 특정한 문제에 달려있습니다만, 일반적인 조언을 드리자면, 가능한 최소한의 노력으로 추상화를 사용하라는 것입니다.

당신은 펑터의 ```map```, 어플리케이티브의 ```ap```, 모나드의 ```flatMap``` 을 통해 시작할 수 있습니다. 모든것을 ```flatMap``` 을 통해 해결할 수 있지만(당신도 보았듯이 ```Option```, ```map```, ```ap``` 는 ```flatMap``` 을 사용해 구현되었습니다), 대부분의 경우 ```map``` 과 ```ap``` 는 그 문제들에 대해 더 나은 접근성을 보여줍니다.


함수로 돌아와서, 우리는 어플리케이티브처럼 동작하도록 함수를 만들 수 있습니다. 먼저, ```pure``` 함수를 추가합니다.
```kotlin
object Function1 {
	fun <A, B> pure(b: B) = { _: A -> b }
}
```

첫째로, 함수타입 ```(A) -> B``` 는 우리가 ```Option``` 으로 했던것처럼 새로운 확장함수를 추가하기 위한 companion object 를 갖지 않기 때문에, object ```Function1``` 을 생성합니다.
```kotlin
fun main(args: Array<String>) {
	val f: (String) -> Int = Function1.pure(0)
    println(f("Hello,"))	//0
    println(f("World"))		//0
    println(f("!"))			//0
}
```

```Function1.pure(t: T)``` 함수는 T 값을 래핑하고 우리가 사용하는 파라미터에 상관없이 이를 반환합니다. 만약 여러분이 다른 함수형 언어를 사용한 경험이 있다면, ```pure``` 함수를 ```identity``` 함수로 인식할 겁니다.

```flatMap```, ```ap```를 함수 ```(A) -> B``` 에 추가해 보겠습니다:
```kotlin
fun <A, B, C> ((A) -> B).map(transform: (B) -> C): (A) -> C = { t -> transform(this(t)) }

fun <A, B, C> ((A) -> B).flatMap(fm: (B) -> (A) -> C): (A) -> C = { t -> fm(this(t))(t) }

fun <A, B, C> ((A) -> B).ap(fab: (A) -> (B) -> C): (A) -> C = fab.flatMap { f -> map(f) }
```


우리는 이미 ```map(transform: (B) -> C): (A) -> C``` 를 커버하고 있고, 그것이 정방향 함수 합성처럼 행동하는 것을 알고있습니다. ```flatMap``` 과 ```ap``` 를 자세히 들여다보면, 파라미터가 거꾸로 된 것을 볼 수 있습니다(해당 ```ap```는 다른 타입의 다른 모든 ```ap``` 함수로 구현됩니다).


하지만 우리가 function 의 ```ap``` 로 무엇을 할 수 있을까요? 아래의 코드를 봅시다:
```kotlin
fun main(args: Array<String>) {
	val add3AndMultiplyBy2: (Int) -> Int = { i: Int -> i + 3 }.ap { { j: Int -> j * 2 } }
    println(add3AndMultiplyBy2(0)) //6
    println(add3AndMultiplyBy2(1)) //6
    println(add3AndMultiplyBy2(2)) //6
}
```

음. 우리는 함수를 함성할 수 있지만, 이미 ```map``` 으로 해봤기 때문에 별로 흥미롭지는 않습니다. 하지만 function 의 ```ap``` 를 사용한 약간의 트릭으로 우리는 원래의 매개변수에 접근할 수 있습니다.

```kotlin
fun main(args: Array<String>) {
	val add3AndMultiplyBy2: (Int) -> Pair<Int, Int> = { i: Int -> i + 3 }.ap { original -> { j: Int -> original to (j * 2) } }
}
```

함수 합성에서 원래 매개 변수에 액세스하는 것은 디버깅과 같은 여러 시나리오에서 유용합니다.



<br><br>



## Summary

우리는 무서운 이름을 가진 많은 멋진 개념을 다루었지만 그 뒤에는 간단한 아이디어가 있습니다. Functor, Applicative 및 Monad 타입은 다음 장에서 다루는 몇 가지 추상화 및 보다 강력한 기능 개념의 문을 엽니다. 우리는 Kotlin 의 한계에 대해 배웠고, 다른 유형의 Functor, Applicative 및 Monad 를 모방하는 함수를 만들 때 Kotlin 의 한계점을 극복 할 수 있었습니다. 우리는 또한 Functor, Applicative, Monad 간의 계층구조를 탐구했다.

다음 장에서는 데이터 스트림을 효과적으로 사용하는 방법에 대해 알아봅니다.



<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}









