---
title: guava example - RangeSet, RangeMap
categories: [dev]
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

은근히 써먹을데가 있을 것 같다.

### RangeSet으로 범위 계산을 간단하게 할 수 있다.

```java
  @Test
  public void openClosedTest() throws Exception {
    RangeSet<Integer> rangeSet = TreeRangeSet.create();

    // 0 <= x <= 2
    rangeSet.add(Range.closed(0, 2));
    System.out.println(rangeSet); // [[0..2]]

    assertTrue(rangeSet.contains(0));
    assertTrue(rangeSet.contains(1));
    assertTrue(rangeSet.contains(2));
    assertFalse(rangeSet.contains(3));

    // 13 < x 15
    rangeSet.add(Range.open(13, 15));
    System.out.println(rangeSet); // [[0..2], (3..5)]
    assertFalse(rangeSet.contains(12));
    assertFalse(rangeSet.contains(13));
    assertTrue(rangeSet.contains(14));
    assertFalse(rangeSet.contains(15));
    assertFalse(rangeSet.contains(16));

    // 23 < x <= 25
    rangeSet.add(Range.openClosed(23, 25));
    System.out.println(rangeSet); // [[0..2], (13..15), (23..25]]
    assertFalse(rangeSet.contains(22));
    assertFalse(rangeSet.contains(23));
    assertTrue(rangeSet.contains(24));
    assertTrue(rangeSet.contains(25));
    assertFalse(rangeSet.contains(26));

    // 33 <= x < 35
    rangeSet.add(Range.closedOpen(33, 35));
    System.out.println(rangeSet); // [[0..2], (13..15), (23..25], [33..35)]
    assertFalse(rangeSet.contains(32));
    assertTrue(rangeSet.contains(33));
    assertTrue(rangeSet.contains(34));
    assertFalse(rangeSet.contains(35));
    assertFalse(rangeSet.contains(36));
  }
```

### complement로 rangeSet의 여집합을 구한다.

```java
  @Test
  public void complementTest() throws Exception {
    RangeSet<Integer> rangeSet = TreeRangeSet.create();

    // 0 <= x < 2
    rangeSet.add(Range.closedOpen(0, 2));
    System.out.println(rangeSet); // [[0..2)]
    assertTrue(rangeSet.contains(0));
    assertTrue(rangeSet.contains(1));
    assertFalse(rangeSet.contains(2));

    // x < 0 , 2 <= x
    RangeSet<Integer> complementRangeSet = rangeSet.complement();
    System.out.println(complementRangeSet); // [(-∞..0), [2..+∞)]
    assertTrue(complementRangeSet.contains(-1));
    assertFalse(complementRangeSet.contains(0));
    assertFalse(complementRangeSet.contains(1));
    assertTrue(complementRangeSet.contains(2));
    assertTrue(complementRangeSet.contains(3));
  }
```

### RangeMap

```java
  @Test
  public void rangeMap() {
    RangeMap<Integer, String> rangeMap = TreeRangeMap.create();
    rangeMap.put(Range.atLeast(1000), "gold");
    rangeMap.put(Range.closedOpen(500, 1000), "silver");
    rangeMap.put(Range.open(0, 500), "bronze");
    rangeMap.put(Range.atMost(0), "none");
    System.out.println(rangeMap); // [(-∞..0]=none, (0..500)=bronze, [500..1000)=silver, [1000..+∞)=gold]

    assertEquals("none", rangeMap.get(-1));
    assertEquals("none", rangeMap.get(0));
    assertEquals("bronze", rangeMap.get(333));
    assertEquals("silver", rangeMap.get(500));
    assertEquals("gold", rangeMap.get(1000));
    assertEquals("gold", rangeMap.get(1500));
  }
```
