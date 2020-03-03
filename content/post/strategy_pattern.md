---
title: "Strategy Pattern"
date: 2020-03-03T08:50:15+09:00
draft: true
---
> 같은 문제를 해결하는 여러 알고리즘이 클래스별로 캡슐화되어 있고, 필요할 때 교체할 수 있도록 함으로써 동일한 문제를 다른 알고리즘으로 해결할 수 있게 하는 디자인 패턴이다.  


![strategy_pattern](/img/strategy.svg)

* Strategy  
-- 인터페이스나 추상 클래스, 외부에도 동일한 방식으로 호출하는 방식을 명시
* ConcreteStrategy  
-- 알고리즘을 실제로 구현
* Context  
-- strategy 패턴을 실제로 이용. 동적으로 전략을 바꾸도록 setter method 제공


참고

- Java 객체지향 디자인 패턴

