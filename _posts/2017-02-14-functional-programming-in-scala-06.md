---
layout: post
title: 6. 순수 함수적 상태
category: functional programming in scala
tags: [scala, fp, study]
---

### 난수
난수(Random Number)는 임의의 수, 예측 불가능 한 수다.
컴퓨터 과학 분야에서 말하는 난수는 보통 결정론적인 방법으로 생성된 난수이다. 
특정 입력이나 조건에 따라 무작위로 선택된 것처럼 보이는 난수 또는 난수열이 생성되며 그 생성 조건이나 입력이 같다면 그 결과값은 항상 같다. 
진정한 의미에서의 난수는 아니지만 그 결과값이 충분히 추측되기 어렵다면 어느정도 난수로서의 의미를 가질 수 있다.
**컴퓨터에 의해 생성된 모든 난수는 의사난수(Pseudo Random Number) 이다.** 

### 소프트웨어적으로 난수를 생성하는 방식
- 초기값(Seed) 에서 출발
- 특정한 공식을 통하여 수 생성
- 그 수를 다시 Seed 로 지정

위의 내용을 계속해서 하게 되면 특정한 공식을 통하여 생성되는 난수열이 만들어진다. 
즉, 연관성이 없는 듯한 수열을 만들어 내는 것이 소프트웨어의 난수 생성 방식이다. 
따라서 진정한 난수가 아니기 때문에 의사 난수(Pseudo-Random Number) 라고 한다.


* 선형 합동 발생기 (linear congruential generator)
위에서 설명한 난수 생성 방식을 선형 합동법이라고 한다. 

<script src="https://gist.github.com/camon85/85204513aa801e253601efa9a496d72f.js"></script>


참고: [wikipedia](https://ko.wikipedia.org/wiki/%EB%82%9C%EC%88%98), [wikidot](http://p7kell.wikidot.com/random-number)






### java의 Math.abs() 버그?
최소값의 절대값이 음수로 나온다.
처리하기 애매한건 알겠지만 이건 버그 아닌가?
<script src="https://gist.github.com/camon85/74c644f589f218c65a2973c3d8287334.js"></script>
