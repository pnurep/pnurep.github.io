---
layout: single
categories: Java
title: Java Super Type Token 에 대하여
author: Gold
author-email: pnurep@gmail.com
description: Super Type Token 에 대해 알아봅니다
tags: Java, Class_Literal, Type_Token, Super_Type_Token, Gson
last_modified_at: 2019-03-10T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
---

<br>

# Java Super Type Token 에 대하여

## 문제인식

최근 Gson 을 사용해 파싱을 하던 도중 타입토큰을 전해주는 코드를 작성하는 부분에서 의문이 들었다.

```kotlin
fun parseData(str: String): List<User> {
    return Gson().fromJson(str, Array<User>::class.java).toList()
}
```

Api 를 호출해 받아온 데이터를 Gson 으로 파싱하는 간단한 코드인데, 문득 지금까지 타입토큰을 전해주는 것에 대해 별로 신경을 쓰지 않았다는 사실 및 어째서 나는 ```List<User>``` 로 리턴할 것인데 왜 굳이 ```Array<User>``` 로 전해준뒤 그걸 ```toList()``` 를 하고 있는가에 대해 ;; 생각해보았다.

<br>

그래서 우선 기존의 코드에 배열을 전해주지 않고, 바로 List 를 전해주는 방식으로 바꾸어보았다.
그리고 당연하게도 컴파일에러가 발생한다.

```kotlin
return Gson().fromJson(str, List<User>::class.java).toList()
// Compile Error
```

자바에서 타입토큰으로 클래스 리터럴이 사용되는데 클래스 리터럴은 ```Integer.class```, ```String.class``` 등을 지칭하는데, ```List<String>``` 은 클래스 리터럴이 아니기 때문에 타입토큰으로 사용할 수가 없기 때문이다.

음... 그럼 어떻게 해야 타입토큰을 전달할 수 있을까?



<br>



## Super Type Token

방법은 Super Type Token 을 사용하는 것이다. Super Type Token 은 <b>Super 타입을 타입토큰으로 사용</b>하는 것을 의미한다.
Super Type Token 은 익명클래스를 사용하여 자바의 Type Erasure 를 교묘히 회피한다.

자바에서는 런타임시에 제네릭 타입정보가 지워진다. 하지만 어떤 제네릭 타입 파라미터를 갖는 클래스를 정의하고, 그 클래스를 <b>상속</b>받으면 런타임시에도 Super Class 의 타입정보를 알 수 있다.

말이 어려울 수 있으니 코드로 확인해보자.

```kotlin
open class SuperClass<T>

abstract class TypeToken1<T>: SuperClass<T>()

println(object : TypeToken1<List<String>>() {}.javaClass.genericSuperclass)
//TypeToken1<java.util.List<? extends java.lang.String>>
```

위 처럼 두 개의 클래스를 정의한다. 수퍼클래스와 그것을 상속받는 TypeToken 클래스다. 위에서 말했던 것처럼, println() 으로 확인해보니 타입정보를 잘 가져올 수 있었다.

코드를 좀 더 살펴보면 println() 메서드 내에서 익명클래스를 사용한 것이 보일것이다. 어떤 클래스를 익명클래스로 생성하게 되면 그 클래스를 확장하는 어떤 이름없는 클래스가 생성된다. 여기서 주의해야할 점은 생성되는 인스턴스는 원래 클래스를 상속하는 그 하위 클래스라는 점이다. 그래서 생성된 익명클래스에 getGenericSuperClass() 메서드를 호출하게 되면 수퍼클래스의 타입을 가져올 수 있는 것이다.

TypeToken 클래스는 익명클래스로의 사용을 강제하기 위해 abstract 키워드를 붙였다. 왜냐하면 Super Type Token 의 원리를 사용해서 타입정보를 가져오려면 항상 어떤 클래스의 SuperClass 가 되어야 하기 때문이다.

getGenericSuperClass() 메서드는 타입정보를 가져오는데 가장 중요한 역할을 한다. [문서](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getGenericSuperclass--)를 보면
* 이 클래스가 나타내는 Entity (클래스, 인터페이스, 프리미티브 유형 또는 void) 의 직접적인 수퍼 클래스를 나타내는 ```Type``` 을 리턴합니다.
* 만약, 수퍼클래스가 parameterized 타입이라면, 반환 된 Type 객체는 소스코드에서 사용된 실제 매개변수를 정확하게 반영 해야한다.

라고 설명이 되어있다. 풀어보자면 어떤 클래스의 수퍼클래스가 타입파라미터를 받는 클래스라면 getGenericSuperClass() 메서드는 그 수퍼클래스의 타입파라미터 정보를 담고있는 ```Type``` 을 리턴해야 한다는 것이다.



중요한 것들은 거의 다 나온것 같다. 위의 정보를 기반으로 타입파라미터 정보를 넘겨줄 수 있는 클래스를 작성해보자.

```kotlin
abstract class TypeToken<T> {

    private val type: Type

    init {
        type = getSuperclassTypeParameter(javaClass)
    }

    private fun getSuperclassTypeParameter(subclass: Class<*>): Type {
        val superClass = subclass.genericSuperclass

        if (superClass is Class<*>)
            throw RuntimeException("Missing type parameter")

        val parameterized = superClass as ParameterizedType
        return parameterized.actualTypeArguments[0]
    }

    fun getType(): Type {
        return this.type
    }

}
```

코드를 살펴보면, getSuperclassTypeParameter() 메서드 내부에서 대부분의 일이 일어난다. 먼저 getGenericSuperClass() 로 수퍼클래스의 타입정보를 가져온다. 수퍼클래스가 타입파라미터 정보를 갖지 않는 경우는 예외를 발생시킨다. 수퍼클래스의 정보를 무사히 가져왔다면, ParameterizedType 로 캐스팅 한 후, getActualTypeArguments() 로 Actual 한 타입파라미터 정보를 얻을 수 있다.

수퍼클래스가 타입파라미터를 갖는다면 ParameterizedType 으로 캐스팅이 가능하다. getActualTypeArguments() 는 [문서](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/ParameterizedType.html#getActualTypeArguments--)를 보면
* 이 타입에 대한 실제 타입아규먼트를 나타내는 Type 객체의 배열을 리턴한다.

라고 되어있다. 즉 이 메서드로 수퍼클래스가 갖는 실제의 타입정보들을 알 수 있는것이다.


코드를 실행해보면...

```kotlin
println( object : TypeToken<List<String>>() {}.getType() )
//java.util.List< ? extends java.lang.String >
```

잘 동작한다.




<br>




## Summary

* List< User >.class 와 같은 형식의 방법은 클래스 리터럴로서 사용할 수 없으므로 타입토큰으로 사용이 불가하다.
* Super Type Token 의 방법을 사용하면 List< User >.class 의 타입정보도 알 수 있다.


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























