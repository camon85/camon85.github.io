---
title: guava example - Joiner
categories: [dev]
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### collection에 null이 포함되어 있으면 NullPointerException이 발생한다.

```java
    @Test(expected = NullPointerException.class)
    public void joinerNullTest() throws Exception {
        List<Integer> integers = Arrays.asList(1, 2, 3, null, 5);
        String joined = Joiner.on(", ")
                .join(integers);
        System.out.println(joined);
    }
```

### null을 무시하는 방법.

```java
    @Test
    public void joinerSkipNullTest() throws Exception {
        List<Integer> integers = Arrays.asList(1, 2, 3, null, 5);
        String joined = Joiner.on(", ")
                .skipNulls()
                .join(integers);
        System.out.println(joined); // 1, 2, 3, 5
    }
```

### null 인 경우 특정 값으로 세팅도 가능

```java
    @Test
    public void joinerUseForNullTest() throws Exception {
        List<Integer> integers = Arrays.asList(1, 2, 3, null, 5);
        String joined = Joiner.on(", ")
                .useForNull("empty")
                .join(integers);
        System.out.println(joined); // 1, 2, 3, empty, 5
    }
```

### Map을 간단하게 String으로 변경하기

```java
    @Test
    public void mapJoinerTest() throws Exception {
        // map to String
        Map<Integer, String> integerStringHashMap = new HashMap<>();
        integerStringHashMap.put(1, "one");
        integerStringHashMap.put(2, "two");
        integerStringHashMap.put(3, "three");
        String mapJoiner = Joiner.on("; ")
                .withKeyValueSeparator("->")
                .join(integerStringHashMap);
        System.out.println(mapJoiner); // 1->one; 2->two; 3->three
    }
```
