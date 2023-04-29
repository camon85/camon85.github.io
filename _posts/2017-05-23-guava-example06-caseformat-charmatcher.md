---
title: guava example - CaseFormat, CharMatcher
categories: [dev]
tags: [guava]
---

[view source](https://github.com/camon85/guava-example)

### 카멜, 언더스코어, 하이픈끼리 서로 변환할 수 있다.

```java
    @Test
    public void caseFormatTest() throws Exception {
        String camelString = "myFirstGuava";
        System.out.println(CaseFormat.LOWER_CAMEL.to(CaseFormat.LOWER_HYPHEN, camelString)); // my-first-guava
        System.out.println(CaseFormat.LOWER_CAMEL.to(CaseFormat.LOWER_UNDERSCORE, camelString)); // my_first_guava
        System.out.println(CaseFormat.LOWER_CAMEL.to(CaseFormat.UPPER_UNDERSCORE, camelString)); // MY_FIRST_GUAVA
        System.out.println(CaseFormat.LOWER_CAMEL.to(CaseFormat.UPPER_CAMEL, camelString)); // MyFirstGuava

        String underscoreString = "my_first_guava";
        System.out.println(CaseFormat.LOWER_UNDERSCORE.to(CaseFormat.LOWER_HYPHEN, underscoreString)); // my-first-guava
        System.out.println(CaseFormat.LOWER_UNDERSCORE.to(CaseFormat.UPPER_UNDERSCORE, underscoreString)); // MY_FIRST_GUAVA
        System.out.println(CaseFormat.LOWER_UNDERSCORE.to(CaseFormat.UPPER_CAMEL, underscoreString)); // MyFirstGuava
        System.out.println(CaseFormat.LOWER_UNDERSCORE.to(CaseFormat.LOWER_CAMEL, underscoreString)); // myFirstGuava
    }
```

### 문자에 대한 여러가지 변환 기능을 제공한다.

```java
    @Test
    public void charMatcherTest() throws Exception {
        // 숫자만 남긴다.
        System.out.println(CharMatcher.digit().retainFrom("gua1va")); // 1

        // 숫자 또는 소문자만 남긴다.
        System.out.println(CharMatcher.digit().or(CharMatcher.javaLowerCase()).retainFrom("HELLO777guava")); // 777guava

        // whitespace를 trim하고 띄어쓰기를 언더스코어로 변경한다.
        System.out.println(CharMatcher.whitespace().trimAndCollapseFrom("     my   first  guava ", '_')); // my_first_guava

        // 숫자를 *로 replace한다.
        System.out.println(CharMatcher.digit().replaceFrom("guava999", "*")); // guava***
    }
```
