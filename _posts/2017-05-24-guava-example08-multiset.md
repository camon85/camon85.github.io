---
layout: post
title: guava example - Multiset
category: dev
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### plain java로 단어수를 카운트 하는 방법
```java
  @Test
  public void wordCountTest() throws Exception {
    // 단어수를 카운트 하는 방법
    List<String> words = new ArrayList<>();
    words.add("java");
    words.add("scala");
    words.add("python");
    words.add("java");
    words.add("java");
    words.add("java");

    Map<String, Integer> wordCountMap = new HashMap<>();
    for (String word : words) {
      Integer count = wordCountMap.get(word);
      if (count == null) {
        wordCountMap.put(word, 1);
      } else {
        wordCountMap.put(word, count + 1);
      }
    }

    System.out.println(wordCountMap); // {python=1, java=4, scala=1}
    assertEquals(4, wordCountMap.get("java").intValue());
  }
```

### Multiset으로 단어수를 카운트 하는 방법
```java
  @Test
  public void wordCountByMultiSetTest() throws Exception {
    // Multiset으로 단어수를 카운트 하는 방법
    List<String> words = new ArrayList<>();
    words.add("java");
    words.add("scala");
    words.add("python");
    words.add("java");
    words.add("java");
    words.add("java");

    Multiset<String> wordMultiSet = HashMultiset.create();

//    for (String word : words) {
//      wordMultiSet.add(word);
//    }

    // 하나씩 for문 돌면서 add 할 수도 있지만, 한번에 addAll하는 방법도 있다.
    wordMultiSet.addAll(words);
    System.out.println(wordMultiSet); // [python, java x 4, scala]
    assertEquals(4, wordMultiSet.count("java"));
  }
```

### 다양한 API 제공
```java
  @Test
  public void queryTest() throws Exception {
    List<String> words = new ArrayList<>();
    words.add("java");
    words.add("scala");
    words.add("python");
    words.add("java");
    words.add("java");
    words.add("java");

    Multiset<String> wordMultiSet = HashMultiset.create();
    wordMultiSet.addAll(words);

    System.out.println(wordMultiSet); // [python, java x 4, scala]
    assertEquals(4, wordMultiSet.count("java"));

    // java의 카운트를 3감소
    wordMultiSet.remove("java", 3);
    assertEquals(1, wordMultiSet.count("java"));

    // java의 카운트를 30으로 강제 설정
    wordMultiSet.setCount("java", 30);
    assertEquals(30, wordMultiSet.count("java"));

    // 기존값이 99라면 20으로 변경한다.
    wordMultiSet.setCount("java", 99, 20);

    // 기존값인 30이 그대로 유지되어 있다.
    assertEquals(30, wordMultiSet.count("java"));
  }
```