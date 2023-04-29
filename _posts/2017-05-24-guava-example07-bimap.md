---
title: guava example - BiMap
categories: [dev]
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### plain java의 map의 사용법

```java
  @Test
  public void mapPutTest() throws Exception {
    // plain java의 map
    Map<String, String> maps = new HashMap<>();
    maps.put("jooyong", "migum");
    maps.put("devhak", "imae");
    maps.put("cjr", "bokjeong");
    maps.put("jr", "bokjeong");

    System.out.println(maps); // {devhak=imae, jr=bokjeong, jooyong=migum, cjr=bokjeong}
    assertTrue(maps.containsKey("cjr"));
    assertTrue(maps.containsKey("jr"));
  }
```

### BiMap에서는 중복되는 값을 put하면 IllegalArgumentException 발생한다.

```java
  @Test(expected = IllegalArgumentException.class)
  public void biMapAlreadyPresentTest() throws Exception {
    BiMap<String, String> biMap = HashBiMap.create();
    biMap.put("jooyong", "migum");
    biMap.put("devhak", "imae");
    biMap.put("cjr", "bokjeong");

    // 중복되는 값을 put하면 IllegalArgumentException 발생
    biMap.put("cc", "bokjeong");
  }
```

### forcePut으로 중복되는 값을 put 할 수 있다. 하지만 중복되는 value는 사라진다.

```java
  @Test
  public void biMapForcePutTest() throws Exception {
    BiMap<String, String> biMap = HashBiMap.create();
    biMap.put("jooyong", "migum");
    biMap.put("devhak", "imae");
    biMap.put("cjr", "bokjeong");

    // 중복되는 value는 사라진다.
    biMap.forcePut("jr", "bokjeong");

    System.out.println(biMap ); // {jooyong=migum, devhak=imae, jr=bokjeong}
    assertFalse(biMap.containsKey("cjr"));
    assertTrue(biMap.containsValue("bokjeong"));
  }
```

### key, value가 뒤바뀐 BiMap을 생성

```java
  @Test
  public void inverseTest() throws Exception {
    BiMap<String, String> biMap = HashBiMap.create();
    biMap.put("jooyong", "migum");
    biMap.put("devhak", "imae");
    biMap.put("cjr", "bokjeong");
    System.out.println(biMap); // {jooyong=migum, devhak=imae, cjr=bokjeong}

    // key, value를 뒤바꾸기
    BiMap<String, String> inversedBiMap = biMap.inverse();
    System.out.println(inversedBiMap); // {migum=jooyong, imae=devhak, bokjeong=cjr}

    assertNotNull(inversedBiMap.get("migum"));
  }
```
