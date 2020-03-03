---
title: "Composite_pattern"
date: 2020-03-03T13:22:14+09:00
draft: true
---
> 전체-부분의 관계를 갖는 객체들 사이의 관계를 정의할 때 유용  
> 클라이언트는 전체와 부분을 구분하지 않고 동일한 인터페이스를 사용할 수 있다.

![composite](/img/composite.svg)

* Component  
-- Leaf 클래스와 전체에 해당하는 Composite 클래스에 공통 인터페이스를 정의한다.
* Leaf  
-- Composite 객체의 부품으로 설정한다
* Composite  
-- 전체 클래스로 복수 개의 Compoenet를 갖도록 정의한다.
-- 복수 개의 Leaf, Composite 객체를 부분으로 가질 수 있다.
