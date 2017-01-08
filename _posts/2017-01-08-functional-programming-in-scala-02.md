---
layout: post
title: 2. 스칼라로 함수형 프로그래밍 시작하기
category: functional programming in scala
tags: [scala, fp, study]
---

### 간단한 예제
{% highlight scala %}
object MyModule {
  def abs(n: Int): Int = 
    if (n < 0) - n
    else n

    private def formatAbs(x: Int) = {
      val msg = "The absolute value of %d is %d"
      msg.format(x, abs(x))
    }

    def main(args: Array[String]): Unit =
      println(formatAbs(-42))
}
{% endhighlight %}

이 프로그램은 MyModule이라는 이름의 객체를 선언한다.
스칼라 코드는 반드시 object로 선언되는 객체나 class로 선언되는 클래스 안에 들어가야 한다.

> `object 키워드`는 singleton을 만든다. 스칼라에는 java의 static 키워드에 해당하는 것이 없다.

프로그램을 실행할 때 main이라는 이름의 메서드를 찾는다.
main메서드는 반드시 String의 Array를 인수로 받아야 하며, 반환 형식은 반드시 Unit이어야 한다.
일반적으로 Unit이라는 것은 그 메서드에 부수 효과가 존재함을 암시한다.

### 프로그램의 실행
> $ scalac MyModule.scala

이렇게 하면 확장자가 .class인 파일들이 생긴다.
그 코드를 scala를 이용해서 실행할 수 있다.

> $ scala MyModule

결과 : The absolute value of -42 is 42.

간단한 프로그램이라면 scala interpreter로 실행도 가능하다.

> $ scala MyModule.scala

결과 : The absolute value of -42 is 42.

REPL(read-evaluate-print loop) 모드에서 소스파일을 불러서 실행해 볼 수도 있다.

> $ scala

> scala> :load MyModule.scala

> scala> MyModule.abs(-42)

> res0: Int = 42


### 고차 함수(higher-order function)
함수를 함수에 전달. 함수도 변수에 담거나 함수에 인수로 넘겨줄 수 있다.


### 꼬리 재귀
재귀 호출이 꼬리 위치에서 일어나는 것을 말한다.
재귀 호출의 결과값 반환 이후에 다른 일을 하지 않는다면 최적화(tail call elimination)가 적용된다.
매 반복마다 스택을 소모하지 않도록 루프 형태로 컴파일 되게 한다.

{% highlight scala %}
def factorial(n: Int): Int = {
  def go(n: Int, acc: Int): Int =
    if (n <= 0) acc
    else go(n - 1, n * acc)

  go(n, 1)
}
{% endhighlight %}

위 예제에서 재귀 호출 go(n - 1, n * acc)는 꼬리 위치에서 일어난다.
1 + go(n - 1, n * acc) 같은 재귀 호출은 꼬리 호출이 아니다. 
go의 결과에 대해 1을 더하는 일을 수행해야 하기 때문이다.

<div class="message">
<strong>연습문제 2.1</strong><br/>
n번째 <a href="https://ko.wikipedia.org/wiki/%ED%94%BC%EB%B3%B4%EB%82%98%EC%B9%98_%EC%88%98" target="_blank">피보나치 수(Fibonacci number)</a>를 돌려주는 재귀 함수를 작성하라. 처음 두 피보나치 수는 0과 1이다. n번째 피보나치 수는 항상 이전 두 수의 합이다. 즉, 피보나치 수열은 0, 1, 2, 3, 5로 시작한다. 반드시 지역 꼬리 재귀 함수를 사용해서 작성할 것.
</div>

### 고차 함수 작성
{% highlight scala %}
object MyModule {

  ...

  def formatResult(n: Int, f: Int => Int): Int = {
    val msg = "The factorial of %d is %d."
    msg.format(n, f(n))
  }
}
{% endhighlight %}

> scala> formatResult(7, factorial)

> res0: String = "The factorial of 7 is 5040."

factorial을 f의 인수로서 formatResult에 넘겨줄 수 있다.

### 단형적 함수(monomorphic function)
{% highlight scala %}
// String 형식에만 특화되어 있다.
def findFirst(ss: Array[String], key: String): Int = {
  @annotation.tailrec
  def loop(n: Int): Int = 
    if (n >= ss.length) -1
    else if (ss(n) == key) n
    else loop(n + 1)

  loop(0) // 배열의 첫 요소에서부터 루프를 시작한다.
}
{% endhighlight %}

### 다형적 함수(parametric polymorphism)
{% highlight scala %}
// 임의의 형식 A에 대해 특정 A 값을 점검하는 함수를 인수로 받음
def findFirst[A](as: Array[A], p: A => Boolean): Int = {
  @annotation.tailrec
  def loop(n: Int): Int = 
    if (n >= as.length) -1
    else if (p(as(n))) n
    else loop(n + 1)

  loop(0)
}
{% endhighlight %}


### 익명 함수로 고차함수 호출


### 부분 적용(partial application)


### 커링(curring)



..작성중