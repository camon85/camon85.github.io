---
layout: post
title: guava example - Optional
category: dev
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### null 체크 대신 isPresent() 사용
```java
    @Test
    public void nameTest() throws Exception {
        Map<Integer, String> members = new HashMap<>();
        members.put(1, "jooyong");
        members.put(2, "devhak");
        members.put(3, "cjr");

        Optional<String> stringOptional = Optional.fromNullable(members.get(4));

        if (stringOptional.isPresent()) {
            System.out.println("member is " + stringOptional);
        } else {
            System.out.println("member is empty"); // member is empty
        }
    }
```

### null인 경우 기본값을 세팅하면 더 편리하다.
```java
    @Test
    public void orTest() throws Exception {
        Map<Integer, String> members = new HashMap<>();
        members.put(1, "jooyong");
        members.put(2, "devhak");
        members.put(3, "cjr");

        Optional<String> stringOptional = Optional.fromNullable(members.get(4));
        System.out.println("member is " + stringOptional.or("empty"));
    }
```
