---
title: "Abstract Factory Pattern"
date: 2020-03-03T13:03:21+09:00
draft: true
---
> 관련성 있는 여러 종류의 객체를 일관성 있는 방석으로 생성할 때 유용  


![abstract](/img/abstract_factory.svg)

* AbstractFactory  
-- factory 클래스의 공통 인터페이스  
-- 각 제품의 부품을 생성하는 기능을 추상 method로 정의한다.
* ConcreteFactory  
-- AbstractFactory 클래스의 abstrace method를 override 해서 prodct를 만든다.
* AbstractProduct  
-- product 의 공통 인터페이스
* ConcreteProduct  
-- factory 에서 생성되는 객체
