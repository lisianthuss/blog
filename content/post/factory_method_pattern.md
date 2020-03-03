---
title: "Factory Method Pattern"
date: 2020-03-03T12:47:00+09:00
draft: true
---
> 객체를 생성하는 코드를 별도의 class/method로 분리함으로써 객체 생성 방식의 변화를 대비하는데 유용

![factory](/img/factory_method.svg)

* Product  
-- factory method로 생성될 객체의 공통 클래스
* ConcreteProduct  
-- 객체가 생성되는 클래스
* Creator  
-- factory method를 갖는 클래스
* ConcreteCreator  
-- factory method를 구현하는 클래스로 ConcreteProduct 객체를 생성한다

