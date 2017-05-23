---
layout: post
title: guava example - Preconditions
category: dev
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### 기존의 try catch 문으로 nullpointer를 catch 하는 방법
```java
    @Test
    public void exceptionTest() throws Exception {
        Integer a = null;
        Integer b = null;
        boolean success = false;

        try {
            System.out.println(a * b);
        } catch (NullPointerException e) {
            success = true;
        }

        Assert.assertEquals(success, true);
    }
```

### 계산하기 전에 미리 null 체크를 하는 방법
```java
    @Test(expected=NullPointerException.class)
    public void checkNotNullTest() throws Exception {
        Integer a = null;
        Integer b = null;
        Preconditions.checkNotNull(a, "must not null");
        Preconditions.checkNotNull(b, "must not null");
        System.out.println(a * b);
    }
```

### expression 검사. a가 검사에 걸림
```java
    @Test(expected = IllegalArgumentException.class)
    public void checkArgumentTest() throws Exception {
        Integer a = null;
        Integer b = 0;
        Preconditions.checkArgument(a != null, "A must not be null");
        Preconditions.checkArgument(b != null || b > 0, "B must be greater than 0");
        System.out.println(a / b);
    }
```

### expression 검사. b가 검사에 걸림
```java
    @Test(expected = IllegalArgumentException.class)
    public void checkArgumentTest2() throws Exception {
        Integer a = 100;
        Integer b = 0;
        Preconditions.checkArgument(a != null, "A must not be null");
        Preconditions.checkArgument(b != null && b > 0, "B must be greater than 0");
        System.out.println(a / b);
    }
```

### expression 검사. a, b 모두 검사를 통과한 경우
```java
    @Test
    public void checkArgumentTest3() throws Exception {
        Integer a = 100;
        Integer b = 5;
        Preconditions.checkArgument(a != null, "A must not be null");
        Preconditions.checkArgument(b != null && b > 0, "B must be greater than 0");
        int x = a / b;
        System.out.println(x);
        Assert.assertTrue(x == 20);
    }
```
    