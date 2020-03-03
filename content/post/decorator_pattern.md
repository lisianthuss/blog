---
title: "Decorator Pattern"
date: 2020-03-03T11:59:45+09:00
draft: true
---
> 기본 기능에 추가할 수 있는 많은 종류의 부가 기능에서 파생되는 다양한 조합을 동적으로 구현할 수 있는 패턴  
### 언제 사용하지?
* 기본 기능에 추가할 수 있는 기능의 종류가 많은 경우, 각 추가 기능을 Decorator 클래스로 정의한 후 필요한 Decorator 객체를 조합하여 추가 기능의 조합을 설계하는 방식

![decorator](/img/decorator.svg)

* Component  
-- 기본 기능을 뜻하는 ComcreteComponent와 추가 기능을 뜻하는 Decorator의 공통 기능을 정의  
-- 즉 클라이언트는 Component를 통해 실제 객체를 사용한다.
* ConcreteComponent  
-- 기본 기능을 구현하는 클래스  
* Decorator  
-- 공통 기능을 제공한다.
* ConcreteDecorator
-- Decorator의 하위 클래스로 기본 기능에 추가되는 개별적인 기능을 뜻한다.

참고

- Java 객체지향 디자인 패턴
