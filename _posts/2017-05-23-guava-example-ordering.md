---
layout: post
title: guava example - Ordering
category: dev
tags: [guava]
---

### null이 포함된 collection의 경우
처리해주지 않으면 NullPointerException이 발생하기 때문에 null 값을 
앞으로 보낼지 뒤로 보낼지를 결정해주어야 한다. nullsFirst(), nullsLast() 둘중 하나를 사용해야 한다.   
```java
  @Test
  public void withNullTest() throws Exception {
    List<Integer> numbers = Arrays.asList(1, 3, 5, null, 2, 4);
    Collections.sort(numbers, Ordering.natural().nullsFirst());
    System.out.println(numbers); // [null, 1, 2, 3, 4, 5]

    assertThat(numbers.get(0), nullValue());
  }
```


### custom object의 정렬
```java
  @Test
  public void customOrderTest() throws Exception {
    List<Person> devTeam = new ArrayList<>();
    devTeam.add(new Person(1, "jooyong", "migum"));
    devTeam.add(new Person(2, "devhak", "imae"));
    devTeam.add(new Person(3, "cjr", "bokjeong"));

    Ordering<Person> byName = new Ordering<Person>() {
      @Override
      public int compare(Person left, Person right) {
        return Ordering.natural().compare(left.getName(), right.getName());
      }
    };

    Collections.sort(devTeam, byName);
    System.out.println(devTeam); // [Person{id=3, name='cjr', address='bokjeong'}, Person{id=2, name='devhak', address='imae'}, Person{id=1, name='jooyong', address='migum'}]

    Collections.sort(devTeam, byName.reverse());
    System.out.println(devTeam); // [Person{id=1, name='jooyong', address='migum'}, Person{id=2, name='devhak', address='imae'}, Person{id=3, name='cjr', address='bokjeong'}]
  }
```

### function 처리 후의 결과값을 정렬
```java
  @Test
  public void onResultOfTest() throws Exception {
    List<Person> devTeam = new ArrayList<>();
    devTeam.add(new Person(1, "jooyong", "migum"));
    devTeam.add(new Person(2, "devhak", "imae"));
    devTeam.add(new Person(3, "cjr", "bokjeong"));

    Function<Person, Integer> lengthFunction = new Function<Person, Integer>() {
      @Override
      public Integer apply(Person input) {
        return input.getName().length();
      }
    };

    Ordering<Person> byLength = Ordering.natural().onResultOf(lengthFunction);
    Collections.sort(devTeam, byLength);
    System.out.println(devTeam); // [Person{id=3, name='cjr', address='bokjeong'}, Person{id=2, name='devhak', address='imae'}, Person{id=1, name='jooyong', address='migum'}]
  }
```

### 기존 collection을 변경하지 않고 정렬된 새로운 collection 생성
```java
  @Test
  public void sortedCopyTest() throws Exception {
    List<Person> devTeam = new ArrayList<>();
    devTeam.add(new Person(1, "jooyong", "migum"));
    devTeam.add(new Person(2, "devhak", "imae"));
    devTeam.add(new Person(3, "cjr", "bokjeong"));

    Ordering<Person> byName = new Ordering<Person>() {
      @Override
      public int compare(Person left, Person right) {
        return Ordering.natural().compare(left.getName(), right.getName());
      }
    };

    List<Person> sortedTeam = byName.sortedCopy(devTeam);

    System.out.println(devTeam); // [Person{id=1, name='jooyong', address='migum'}, Person{id=2, name='devhak', address='imae'}, Person{id=3, name='cjr', address='bokjeong'}]
    System.out.println(sortedTeam); // [Person{id=3, name='cjr', address='bokjeong'}, Person{id=2, name='devhak', address='imae'}, Person{id=1, name='jooyong', address='migum'}]
  }
```

### 정렬 후 원하는 개수만큼 리턴 (java8 stream의 limit 느낌)
```java
  @Test
  public void leastOfTest() throws Exception {
    List<Person> devTeam = new ArrayList<>();
    devTeam.add(new Person(1, "jooyong", "migum"));
    devTeam.add(new Person(2, "devhak", "imae"));
    devTeam.add(new Person(3, "cjr", "bokjeong"));

    Ordering<Person> byName = new Ordering<Person>() {
      @Override
      public int compare(Person left, Person right) {
        return Ordering.natural().compare(left.getName(), right.getName());
      }
    };

    List<Person> leastOfTeam = byName.leastOf(devTeam, 2);
    System.out.println(leastOfTeam); // [Person{id=3, name='cjr', address='bokjeong'}, Person{id=2, name='devhak', address='imae'}]
    assertEquals(2, leastOfTeam.size());
  }
```


[view source](https://github.com/camon85/guava-example)