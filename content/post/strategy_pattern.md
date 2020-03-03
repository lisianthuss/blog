---
title: "Strategy Pattern"
date: 2020-03-03T08:50:15+09:00
draft: false
---
> 같은 문제를 해결하는 여러 알고리즘이 클래스별로 캡슐화되어 있고, 필요할 때 교체할 수 있도록 함으로써 동일한 문제를 다른 알고리즘으로 해결할 수 있게 하는 디자인 패턴이다.  


![strategy_pattern](/img/strategy.svg)

* Strategy  
-- 인터페이스나 추상 클래스, 외부에도 동일한 방식으로 호출하는 방식을 명시
* ConcreteStrategy  
-- 알고리즘을 실제로 구현
* Context  
-- strategy 패턴을 실제로 이용. 동적으로 전략을 바꾸도록 setter method 제공

## 예제 ( 로봇 만들기 )

### 1. 기본 설계
![robot1](/img/strategy_robot1.svg)

공격과이동이 가능한 태권v와 아톰을 위와 같이 설계할 수 있다.

### 2. 문제점
* 기존 공격과 이동 방법을 수정하려면 어떤 변경을 해야하나? 아톰이 날 수 없고 걷게만 만들고 싶으면? 태권v를 날게 하려면?
* 새로운 로봇을 만들어 기존 공격 또는 이동 방법을 추가, 수정하려면? 새로운 로봇을 만들어 태권v의 미사일 공격을 추가한다면?

### 2.1 기존 공격과 이동 방법을 수정하는 경우
- 기존 코드의 내용을 수정해기 때문에 OCP에 위배된다.
- 두 클래스의 move가 동일한 기능을 수행하므로 중복이 발생된다.
```java
public class TaekwonV extends Robot {
    ...
    public void move() {
        System.out.println("I can only walk.");
}
public class Atom extends Robot {
    ...
    public void move() {
        System.out.println("I can only walk.");
}
```

### 2.2 새로운 로봇에 공격 이동 방법을 추가/수정하는 경우
* 새 로봇 클래스를 추가하고 로봇의 sub 클래스로 둔다  
* 하지만 다른 로봇의 공격/이동 방법을 사용하려고 할 때, 코드 중복이 발생하게 된다.

![robot1](/img/strategy_robot2.svg)


### 3. 해결책
> 무엇이 변화되었는지를 찾은 후에 이를 클래스로 캡슐화한다.  

* 문제를 발생시키는 요인은 공격과 이동방법의 변화다.  
* 기존의 로봇이나 새로운 로봇이 이러한 기능을 별다른 코드 변경 없이 제공받거나,
* 기존의 공격이나 이동방법을 다른 공격이나 이동방법으로 쉽게 변경할 수 있어야 한다.

공격과 이동을 위한 인터페이스를 각각 만들고 이들을 구현한 클래스를 만들어야 한다.

![robot1](/img/strategy_robot3.svg)  

* Robot 클래스 입장에서 보면 공격 / 이동 방법이 AttackStrategy, MovingStrategy 인터페이스에 의해 캡슐화 되어 있다.
* 새로운 공격 방법이 추가도더라도 AttackStrategy 인터페이스가 Robot 클래스의 변경을 차단해준다.
* 즉 새로운 기능의 추가가 기존의 코드에 영향을 미치지 못한다.

```java
public abstract class Robot {
    private MovinStrategy movingStrategy
    private AttackStrategy attackStrategy
}
...
public void setMovingStrategy(MovingStrategy movingStrategy) {
    this.movingStrategy = movingStrategy;
}
public void setAttackStrategy(AttackStrategy attackStrategy) {
    this.attackStrategy = attackStrategy;
}
...
public static void main() {
    Robot taekwonV = new TaekwonV("TaekwonV");
    Robot atom = new Atom("Atom");

    taekwonV.setMovingStrategy(new WalkingStrategy());
    taekwonV.setAttackStrategy(new MissileStrategy());

    atom.setMovingStrategy(new FlyStrategy());
    atom.setAttackStrategy(new FunchStrategy());

    taekwonV.move();
    taekwonV.attack();

    atom.move();
    atom.attack();
}
```
---
참고
- Java 객체지향 디자인 패턴

