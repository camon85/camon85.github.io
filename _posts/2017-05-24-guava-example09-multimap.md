---
title: guava example - Multimap
categories: [dev]
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### 하나의 key에 여러개의 value를 put 할 수 있다.

```java
  @Test
  public void multimapTest() throws Exception {
    // 하나의 key에 여러 value 셋팅 가능
    Multimap<String, String> mpsTeam = ArrayListMultimap.create();
    mpsTeam.put("dev", "jooyong");
    mpsTeam.put("dev", "cjr");
    mpsTeam.put("dev", "devhak");
    mpsTeam.put("dev", "not yet");
    mpsTeam.put("live", "jn");
    mpsTeam.put("live", "hs");
    mpsTeam.put("live", "hb");
    mpsTeam.put("live", "yi");
    mpsTeam.put("live", "mn");
    System.out.println(mpsTeam); // {dev=[jooyong, cjr, devhak, not yet], live=[jn, hs, hb, yi, mn]}

    Collection<String> dev = mpsTeam.get("dev");
    System.out.println(dev); // [jooyong, cjr, devhak, not yet]

    // 특정 value 제거
    mpsTeam.remove("dev", "not yet");
    System.out.println(mpsTeam); // {dev=[jooyong, cjr, devhak], live=[jn, hs, hb, yi, mn]}

    //  always returns a non-null, possibly empty collection
    Collection<String> unknown = mpsTeam.get("unkownKey");
    System.out.println(unknown); // []
    assertNotNull(unknown);
  }
```

### Multimap은 Map이 아니다.

Multimap 구현체

- ArrayListMultimap

- HashMultimap

- LinkedListMultimap

- LinkedHashMultimap

- TreeMultimap

- ImmutableListMultimap

- ImmutableSetMultimap

### Multimap을 Map으로 변환하는 방법

```java
  @Test
  public void asMapTest() throws Exception {
    // 하나의 key에 여러 value 셋팅 가능
    Multimap<String, String> mpsTeam = ArrayListMultimap.create();
    mpsTeam.put("dev", "jooyong");
    mpsTeam.put("dev", "cjr");
    mpsTeam.put("dev", "devhak");
    mpsTeam.put("dev", "not yet");
    mpsTeam.put("live", "jn");
    mpsTeam.put("live", "hs");
    mpsTeam.put("live", "hb");
    mpsTeam.put("live", "yi");
    mpsTeam.put("live", "mn");
    System.out.println(mpsTeam); // {dev=[jooyong, cjr, devhak, not yet], live=[jn, hs, hb, yi, mn]}

    Map<String, Collection<String>> stringCollectionMap = mpsTeam.asMap();
    System.out.println(stringCollectionMap); // {dev=[jooyong, cjr, devhak, not yet], live=[jn, hs, hb, yi, mn]}
  }
```
