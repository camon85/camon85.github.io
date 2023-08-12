---
title: Virtual Thread 살펴보기
categories: [dev]
tags: [java]
---
# Virtual Thread
- [JEP 444: Virtual Threads `JAVA 21`](https://openjdk.org/jeps/444)
- 가상 쓰레드는 높은 처리량의 동시성 애플리케이션을 쉽게 작성할 수 있는 경량 쓰레드이다.
- Java 플랫폼에 가상 쓰레드가 도입된다.

## Thread per request 모델
![thread-per-request](/assets/img/post/virtual_thread/thread-per-request.png)
- 웹 애플리케이션의 일반적이고 단순한 패턴
- 쓰레드 생성은 매우 느리고 자원을 많이 소모하기 때문에, 미리 생성하여 Thread Pool 을 통해 관리 한다.

## Java thread 와 OS thread
![java-is-made-of-threads](/assets/img/post/virtual_thread/java-is-made-of-threads.png)
- Java의 thread는 OS의 thread와 1:1로 연결되어 있다. OS thread를 사용하기 쉽도록 추상화 되어있다.
- Java thread를 `Platform thread` 라고 부른다.
- 쓰레드가 데이터베이스 연결 또는 네트워크 호출과 같은 Blocking 작업을 수행하면 작업이 완료될 때까지 해당 쓰레드는 차단된다.
- 처리량을 늘리기 위해 쓰레드를 많이 생성하게 되면 메모리 부족, 잦은 컨텍스트 스위칭 등 여러가지 문제가 발생할 수 있다.
- Blocking 되는 동안 낭비되는 자원을 해결하기 위해 Non-Blocking I/O, Reactive Programming 등의 기술이 발전해왔다.

### Non-Blocking I/O, Reactive Programming 단점도 있음
- 러닝 커브
- 코드의 컨텍스트가 달라짐
- 디버깅 어려움 등
- [Why Continuations are Coming to Java](https://www.youtube.com/watch?v=9vupFNsND6o) 



---
#### Why Continuations are Coming to Java 내용 발췌
- 간단한 예제 코드가 있다.

```java
double calcImportantFinance(double x) {
  try {
    double result
    result = compute("USD->euro", 100.0);
    result = compute("subtractTax", result * 1.3);
    result = compute("addInterest", result);
    return result; 
  } catch (Exception ex) {
    log(ex);
    throw ex;
  }
}

double compute(String op, double x);
```

- 위 코드를 비동기 코드로 작성해본다.

```java
CompletableFuture<Double> calcImportantFinance(double x) {
  return
    compute("USD-euro, 100.0")
    .thenCompose(result -> compute("subtractTax", result * 1.3))
    .thenCompose(result -> compute("addInterest", result))
    .handle((result, ex) -> {
      if (ex != null) {
        log(ex);
        throw new RuntimeException(ex);
      } else
        return result;
    });
}
```

##### 문제점
1. Lost control flow
코드 분기나 반복문을 사용하고 싶을 때, CompletableFuture를 사용하는 경우 기존 코드를 재사용 할 수가 없다.
```java
double result
result = compute("USD->euro", 100.0);
if (result > 1000)
  result = compute("subtractTax", result * 1.3);
while (!sufficient(result))
  result = compute("addInterest", result);
return result;
```

2. Lost context
CompletableFuture나 Reactive 프레임워크를 사용할 때, 각 단계는 완전히 다른 스레드에서 실행될 수 있다.
예외에 도달하게 될 스택은 실제로 실행 중인 컨텍스트를 알려주지 않기 때문에 코드를 디버깅하기가 매우 어렵다. 
```java
CompletableFuture<Double> calcImportantFinance(double x) {
  return
    compute("USD-euro, 100.0")
    .thenCompose(result -> compute("subtractTax", result * 1.3))
    .thenCompose(result -> compute("addInterest", result))
    .handle((result, ex) -> {
      if (ex != null) {
        log(ex);
        throw new RuntimeException(ex);
      } else
        return result;
    });
}
```

 3. Viral
가장 큰 문제는 반환 타입. 
비동기 메서드를 호출하는 모든 caller는 CompletableFuture를 처리할 수 있도록 비동기 스타일로 작업해야 한다.
이는 전체 호출 스택이 동기식이거나 비동기식이어야 함을 의미한다. 
두 스타일을 상호 운용하는 것은 거의 불가능하다.

다른 언어에서는 async-await 기능으로 이를 해결하기도 한다.
하지만 여전히 다른 스레드에서 실행되기 때문에 stacktrace가 크게 도움이 되지 않을것임
---



## Virtual Thread 구조
![virtual-threads](/assets/img/post/virtual_thread/virtual-threads.png)
- 가상 쓰레드는 OS 쓰레드에 연결되지 않음
- OS 쓰레드 제한에 대해 걱정하지 않고 원하는 만큼 가상 스레드를 생성할 수 있다.
- Platform thread 는 `Carrier thread` 라고 부른다.
- Platform thread 의 수는 기본적으로 사용 가능한 프로세서의 수 이하로 설정된다.

## Virtual thread는 무엇이 다른가?
- 실행중인 가상 쓰레드가 I/O 작업 등으로 Blocking 되면 캐리어 쓰레드에서 `unmount` 되고 스택 프레임은 heap 에 저장된다. 
- 다시 실행할 준비가 되면 heap에 저장된 스택 프레임 정보를 꺼내고 가상 쓰레드를 캐리어 쓰레드에 `mount` 하여 처리한다.
	- Blocking 방식으로 개발하지만 JVM이 Non-Blocking 처리를 해주는 것 같은 동작
- 비동기 방식이나 Reactive 방식과 다르게 스택 프레임을 잃어버리지 않는다.
- 코드 작성 방식이 달라지지 않고, 디버깅, JVM 프로파일링 도구도 기존과 같은 것을 사용할 수 있다.

## 주의할 부분
### Thread Pool 을 사용하지 마라
- 풀링은 안티패턴
- 가상 쓰레드를 생성하는 것은 작고 가볍기 때문에, 쓰레드 재사용을 위해 풀링하는 것은 좋지 않은 아이디어다.
- 데이터베이스 연결과 같이 동시성 제한을 위해서는 세마포어를 사용해라 
- https://www.infoq.com/articles/java-virtual-threads/ 내용 중 이 부분은 잘 이해가 안된다.
>Virtual threads are so lightweight that it is perfectly OK to create a virtual thread even for short-lived tasks, and counterproductive to try to reuse or recycle them. Indeed, virtual threads were designed with such short-lived tasks in mind, such as an HTTP fetch or a JDBC query.

### ThreadLocal 남용
- 가상 쓰레드의 수가 수백만개까지 많아질 수 있기 때문에 ThreadLocal을 잘못 사용하면 메모리 사용량이 매우 높아질 수 있다.

### Avoid pinning
- Synchronized 를 사용하면 Carrier Thread 전체가 블로킹되어 가상 쓰레드를 unmount 할 수 없게 된다. 이것을 `Pinning` 이라고 한다.
- 캐리어 쓰레드가 차단되어, 다른 가상 쓰레드에서도 사용할 수 없다.
	- https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/
	- 이후 버전에서 해결될 듯하다

## Spring 에서 Virtual thread사용 방법
서블릿 요청이 가상 쓰레드에서 처리되도록 설정만 하면 된다.
```java
@Bean(TaskExecutionAutoConfiguration.APPLICATION_TASK_EXECUTOR_BEAN_NAME)
public AsyncTaskExecutor asyncTaskExecutor() {
  return new TaskExecutorAdapter(Executors.newVirtualThreadPerTaskExecutor());
}

@Bean
public TomcatProtocolHandlerCustomizer<?> protocolHandlerVirtualThreadExecutorCustomizer() {
  return protocolHandler -> {
    protocolHandler.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
  };
}
```


## 이후 할일
샘플 코드로 동작 테스트를 해봐야겠다.

---

참고:
- [Spring into the Future: Embracing Virtual Threads with Java's Project Loom](https://www.youtube.com/watch?v=Is5HXJhC3jE)
- [Embracing Virtual Threads](https://spring.io/blog/2022/10/11/embracing-virtual-threads)
- [Web applications and Project Loom](https://spring.io/blog/2023/02/27/web-applications-and-project-loom)
- [Virtual Threads: New Foundations for High-Scale Java Applications](https://www.infoq.com/articles/java-virtual-threads/)
- [Virtual Threads and Structured Concurrency in Java 21 With Loom](https://www.youtube.com/watch?v=QxxG66eQoTc)
- [Why Continuations are Coming to Java](https://www.youtube.com/watch?v=9vupFNsND6o)
- [The Ultimate Guide to Java Virtual Threads](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/)
- [Carrier Kernel Thread Pinning of Virtual Threads](https://paluch.biz/blog/183-carrier-kernel-thread-pinning-of-virtual-threads-project-loom.html)
