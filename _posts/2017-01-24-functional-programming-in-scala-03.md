---
title: 3. 함수적 자료구조
categories: [functional programming in scala]
tags: [scala, fp, study]
---

### 함수적 자료구조의 정의

순수 함수만으로 조작되는 자료구조.
list a와 b 를 연결하면 두 list에는 변경이 일어나지 않고 새로운 list가 생성된다.
객체 복사가 많이 일어나는 것을 걱정하지 않아도 된다.
List자료형은 singly linked list와 개념적으로 동일하다.

- trait는 추상 인터페이스로, 메서드의 구현을 담을 수도 있다.
- trait앞에 sealed를 붙이는 것은 이 trait의 구현이 반드시 이 파일안에 선언되어 있어야 함을 뜻한다.

{% highlight scala %}
package chap3

sealed trait List[+A]
case object Nil extends List[Nothing]
case class Cons[+A](head: A, tail: List[A]) extends List[A]

object List {
def sum(ints: List[Int]): Int = ints match {
case Nil => 0
case Cons(x, xs) => x + sum(xs)
}

def product(ds: List[Double]): Double = ds match {
case Nil => 0
case Cons(0.0, \_) => 0.0
case Cons(x, xs) => x \* product(xs)
}

def apply[A](as: A*): List[A] =
if (as.isEmpty) Nil
else Cons(as.head, apply(as.tail: \_*))
}
{% endhighlight %}

### 패턴 매칭

object List에 속한 함수 sum과 product는 List의 동반 객체이다.
sum함수의 정의는 빈 목록의 합이 0이다. 비지 않은 목록의 합은 첫 요소 x에 나머지 요소들의 합 xs를 더한 것이다.

<div class="message">
<strong>동반객체</strong><br/>
동반객체는 자료 형식과 같은 이름의 object.
</div>

패턴 매칭은 표현식의 구조를 따라 내려가면서 그 구조의 부분 표현식을 추출한다.
대상이 패턴과 부합하면 그 문의 결과가 전체 부합 표현식의 결과가 된다. 만일 대상과 부합하는 패턴이 여러 개이면 스칼라는 처음으로 부합한 경우 문을 선택한다.

- List(1,2,3) match { case \_ => 42 } 는 42가 된다.
- List(1,2,3) match { case Cons(h, \_) => h } 의 결과는 1이다.
- List(1,2,3) match { case Cons(\_, t) => t } 의 결과는 List(2,3)이다.
- List(1,2,3) match { case Nil => 42 } 의 결과는 실행시점 MatchError이다.

<div class="message">
<strong>연습문제 3.1</strong><br/>
다음 패턴 매칭 표현식의 결과는 무엇인가?

{% highlight scala %}
val x = List(1,2,3,4,5) match {
case Cons(x, Cons(2, Cons(4, _))) => x
case Nil => 42
case Cons(x, Cons(y, Cons(3, Cons(4, _)))) => x + y
case Cons(h, t) => h + sum(t)
case \_ => 101
}
{% endhighlight %}

</div>
<script src="https://gist.github.com/camon85/8b8e553703879a4915eb9e9c9baa182a.js"></script>

<div class="message">
<strong>스칼라의 가변 인수 함수</strong><br/>
object List의 apply 함수는 가변 인수 함수이다. 이 함수가 A형식의 인수를 0개 이상 받을 수 있음을 뜻한다.

{% highlight scala %}
def apply[A](as: A*): List[A] =
if (as.isEmpty) Nil
else Cons(as.head, apply(as.tail: \_*))
{% endhighlight %}

</div>

### 함수적 자료구조의 자료 공유
