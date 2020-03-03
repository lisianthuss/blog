---
title: "Observer Pattern"
date: 2020-03-03T11:26:50+09:00
draft: true
---
> 통보 대상 객체의 관리를 Subject 클래스와 Observer 인터페이스로 일반화하여  
> 데이터 변경을 통보하는 클래스(ConcreteSubject)는 통보 대상 클래스나  
> 객체(ConcreteObserver)에 대한 의존성을 없앨 수 있다.  
> 결과적으로 통보 대상 클래스나 대상 객체의 변경에도 ConcreteSubject 클래스의 수정 없이 그대로 사용할 수 있도록 한다.

### 언제 사용하지?
데이터의 변경이 발생했을 때, 상대 클래스나 객체에 의존하지 않으면서 데이터 변경을 통보하고자 할 때 유용하다.

![observer](/img/observer.svg)

* Observer  
-- 데이터의 변경을 통보받는 인터페이스  
-- Subject는 Observer 인터페이스의 update를 호출하여, ConcreteSubject의 데이터 변경을 ConcreteObserver에게 통보한다.
* Subject  
-- ConcreteObserver 객체를 관리  
-- Observer 인터페이스를 참조하여 Concrete Observer를 관리하므로 ConcreteObserver의 변화에 독립적일 수 있다.
* ConcreteSubject  
-- 변경 관리 대상이 되는 데이터가 있는 클래스  
-- 데이터를 변경을 위한 setState method에서 자신의 데이터인 subjectState를 변경하고  
-- Subject의 notifyObservers method를 호출해서 ConcreteObserver 객체에 변경을 통보한다.
* ConcreteObserver  
-- ConcreteSubject의 변경을 통보받는 클래스  
-- Observer 인터페이스의 update를 구현해서 변경을 통보받는다  
-- ConcreteSubject의 getState를 호출하여 변경된 데이터를 조회한다.


참고

- Java 객체지향 디자인 패턴
