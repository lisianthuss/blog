---
title: "Abstract Factory Pattern"
date: 2020-03-03T13:03:21+09:00
draft: false
---
> 관련성 있는 여러 종류의 객체를 일관성 있는 방식으로 생성할 때 유용  


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

## 예제 ( 엘리베이터 부품 업체 변경하기 )
엘리베이터를 구성하는 부품 중 모터와 문을 예를 들어보자.  
각 제조업체별로 모터와 문을 생산한다. 제조업체가 다르더라도 각 부품은 같은 동작을 하기 때문에 다른 부품을 사용하더라도 코드의 변경은 최소화 되어야 한다.

![elevator](/img/abstract_elevator.svg)

Motor 와 Door 클래스에서는 template method 패턴을 사용하여, 공통되는 동작을 상위 클래스에 구현하고 각각의 개별 동작은 하위 클래스에 구현할 수 있다.


엘리베이터 입장에서 특정 제조업체의 모터와 문을 제어하려는 경우, MotorFactory 클래스를 정의하여 LGMotor와 HyundaiMotor 중에 선택하여 Motor, Door 객제를 생성할 수 있다.

![elevator](/img/abstract_factory2.svg)

```c++
enum VenderID { LG, HYUNDAI }

class MotorFactory {
    static Motor* createMotor(VenderID venderID) {
        Motor* motor = NULL;
        switch (venderID) {
            case LG:
                motor = new LGMotor();
                break;
            case HYUNDAI:
                motor = new HyundaiMotor();
                break;
        }
        return motor;
    }
}

int main() {
    Door* lgDoor = new DoorFactory::createDoor(LG);
    Motor* lgMotor = new MotorFactory::createMotor(LG);
    lgMotor->setDoor(lgDoor);

    lgDoor.open();
    lgMotor.move(UP);
}
```

### 1. 문제점
* 다른 제조업체의 부품을 사용해야 한다면?(HyundaiMotor, HyundaiDoor)
* 새로운 제조업제 부품을 지원해야 한다면?(SamsungMotor, SamsungDoor)

#### 1.1 다른 제조업체의 부품을 사용하는 경우
LG 대신 Hyundai의 부품을 사용하도록 고쳐보자.

```c++
int main() {
    Door* hyundaiDoor = new DoorFactory::createDoor(hyundai);
    Motor* hyundaiMotor = new MotorFactory::createMotor(hyundai);
    hyundaiMotor->setDoor(hyundaiDoor);

    hyundaiDoor.open();
    hyundaiMotor.move(UP);
}
```
부품이 Motor와 Door 외에 다수가 있을 경우, 필요한 Factory 개수도 많아지며 그에 따라 수정해야 할 코드량은 비례하게 된다.

#### 1.2 새로운 제조업제 부품을 지원해야 한다면
새로운 제조업체 Samsung 이 추가된다면, SamsungMotor, SamsungDoor 클래스가 필요하며, Factory 클래스의 수정이 요구된다.
```c++
class DoorFactory {
    static Door* createDoor(VenderID venderID) {
        Door* door = NULL;
        switch (venderID) {
            case LG:
                door = new LGDoor();
                break;
            case HYUNDAI:
                door = new HyundaiDoor;
                break;
            case SAMSUNG:
                door = new SamsungDoor;
                break;
        }
        return door;
    }
}
```

factory method 를 사용한 객체 생성은 관련 있는 여러 개의 객체를 일관성 있는 방식으로 생성하는 경우에 많은 코드 수정이 필요하다.

### 2. 해결책
여러 종류의 객체를 생성할 때, 객체들 사이의 관련성이 있는 경우( LG 모터를 사용하면 LG 문을 사용 ) 각 종류별로 Factory 클래스를 사용하는 대신, 관련 객체를 일관성 있게 생성하는 Factory 클래스를 사용해보자
* 업체별로 Factory 클래스를 만든다.  

![factory3](/img/abstract_factory3.svg)
ElevatorFactory 클래스를 LGElevatorFactory, HyundaiElevatorFactory 의 상위 클래스로 설계가 가능하다.

```c++
class ElevatorFactory {
    virtual Motor* createMotor() = 0;
    virtual Door* createDoor() = 0;
};
class LGElevatorFactory : public ElevatorFactory {
    Motor* createMotor() { return new LGMotor(); }
    Door* createDoor() { return new LGDoor(); }
};
class HyundaiElevatorFactory : public ElevatorFactory {
    Motor* createMotor() { return new HyundaiMotor(); }
    Door* createDoor() { return new HyundaiDoor(); }
};

int main() {
    ElevatorFactory* factory = NULL;
    if (LG == vender) {
        factory = new LGElevatorFactory();
    }
    else (HYUNDAI == vender) {
        factory = new HyundaiElevatorFactory();
    }

    Door* door = factory->createDoor();
    Motor* motor = factory->createMotor();

    motor->setDoor(door);

    door->open();
    motor->move(UP);

    delete factory;
}
```
어느 제조업체의 부품을 사용하던지 코드의 변경이 필요 없다.




참고
* Java 객체지향 디자인 패턴
