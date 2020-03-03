---
title: "Command Pattern"
date: 2020-03-03T11:06:56+09:00
draft: true
---
> 실행될 기능을 캡슐화하여, 기능 실행을 요구하는 호출자(Invoker) 클래스와, 실제 기능을 수행하는 수신자(Receiver) 클래스 사이의 의존성을 제거한다.  
> 따라서 실행될 기능의 변경에도 호출자 클래스를 수정 없이 그대로 사용할 수 있다.  
  
  
### 언제 사용하지?
이벤트가 발생했을 때 실행될 기능이 다양하면서도 변경이 필요한 경우, 이벤트를 발생시키는 클래스를 변경하지 않고 재사용하고자 할 때 유용하다.

![command](/img/command.svg)

* Command  
-- 실행될 기능에 대한 인터페이스. 실행될 기능을 execute method로 선언함.
* ConcreteCommand  
-- 실제로 실행되는 기능을 구현.
* Invoker  
-- 기능의 실행을 요청하는 호출자 클래스
* Receiver  
-- ConcreteCommand에서 execute method를 구현할 때 필요한 클래스. ConcreteCommand의 기능을 실행하기 위해 사용하는 수신자 클래스


참고

- Java 객체지향 디자인 패턴
