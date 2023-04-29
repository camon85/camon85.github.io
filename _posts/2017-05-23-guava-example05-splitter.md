---
title: guava example - Splitter
categories: [dev]
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### string을 구분자를 통해 Iterable 객체로 변경

```java
    @Test
    public void splitTest() throws Exception {
        String sequence = "java ,scala, ,python       ,      go, , javascript";
        Iterable<String> iterable = Splitter.on(',')
                .split(sequence);
        System.out.println(iterable); // [java , scala,  , python       ,       go,  ,  javascript]
    }
```

### list로 만들수도 있다.

```java
    @Test
    public void splitToListTest() throws Exception {
        String sequence = "java ,scala, ,python       ,      go, , javascript";
        List<String> strings = Splitter.on(',')
                .splitToList(sequence);
        System.out.println(strings); // [java , scala,  , python       ,       go,  ,  javascript]
    }
```

### 공백문자 제거

```java
    @Test
    public void splitTrimResultTest() throws Exception {
        String sequence = "java ,scala, ,python       ,      go, , javascript";
        Iterable<String> iterable = Splitter.on(',')
                .trimResults()
                .split(sequence);
        System.out.println(iterable); // [java, scala, , python, go, , javascript]
    }
```

### null 오브젝트 제거

```java
    @Test
    public void splitOmitEmptyTest() throws Exception {
        String sequence = "java ,scala, ,python       ,      go, , javascript";
        Iterable<String> iterable = Splitter.on(',')
                .trimResults()
                .omitEmptyStrings()
                .split(sequence);
        System.out.println(iterable); // [java, scala, python, go, javascript]
    }
```
