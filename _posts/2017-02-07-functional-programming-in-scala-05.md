---
layout: post
title: 5. 엄격성과 나태성
category: functional programming in scala
tags: [scala, fp, study]
---

### 엄격한 함수와 엄격하지 않은 함수
엄격한 함수는 자신의 인수들을 항상 평가한다. 스칼라에서 기본적으로 모든 함수 정의는 엄격한 함수이다.
```scala
def square(x: Double): Double = x * x
```

square(41.0, + 1.0)으로 호출하면, 함수 square는 엄격한 함수이므로 평가된 값인 42.0을 받게된다. 
square(sys.error("failure"))라고 호출하면 square가 실제로 작업을 수행하기도 전에 예외가 발생한다.
sys.error("failure") 라는 표현식이  square의 본문에 진입하기 전에 평가되기 때문이다.


```scala
def if2[A](cond: Boolean, onTrue: () => A, onFalse: () => A): A = 
    if (cond) onTrue() 
    else onFalse() 
    
if2 (a < 22, () => println("a"), () => println("b"))
```
위 예에서 보듯이, 평가되지 않은 채 전달될 인수에는 해당 형식 바로 앞에 () => 를 표기해 준다.
() => A는 인수를 받지 않고 A를 돌려주는 함수이다.
일반적으로 표현식의 평가되지 않은 형태를 성크(thunk)라고 부른다.
나중에 그 성크의 표현식을 평가해서 결과를 내도록 강제할 수 있다.
onTrue()나 onFalse()에서처럼 빈 인수 목록을 지정해서 함수를 호출하면 된다.


이런 표현이 상당히 흔하기 때문에 스칼라에서는 더 깔끔한 구문을 제공한다.
```scala
def if2[A](cond: Boolean, onTrue: => A, onFalse: => A): A = 
    if (cond) onTrue 
    else onFalse 
```
평가되지 않은 채로 전달할 인수에는 그 형식 바로 앞에 화살표 => 만 붙인다.
함수의 본문에서는 => 로 지정된 인수를 평가하는데 특별한 구문이 필요하지 않다.
그냥 평소대로 해당 식별자를 참조하면 된다. 스칼라가 성크안의 표현식을 알아서 감싸준다.



* 스칼라에서 비엄격 함수의 인수는 값(by value)으로 전달되는 것이 아니라 이름(by name)으로 전달된다.

* 어떤 표현식의 평가가 무한히 실행되거나 오류를 던진다면, 
그러한 표현식을 일컬어 종료되지(terminate) 않는 표현식 또는 바닥(bottom)으로 평가되는 표현식이라고 부른다.

 
 

