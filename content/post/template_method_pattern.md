---
title: "Template Method Pattern"
date: 2020-03-03T12:34:39+09:00
draft: false
---
> 전체적인 알고리즘은 상위 클래스에 구현하면서 다른 부분은 하위 클래스에서 구현할 수 있도록 하는 패턴

### 언제 사용하지?
전체적으로는 동일하면서 부분적으로는 다른 구문으로 구성된 method의 코드 중복을 최소화할 때 유용  


![template](/img/templateMethod.svg)  

## 예제 ( 여러 회사 모터 지원하기 )
엘리베이터 제어 시스템에서 모터를 구동시키는 기능을 구현하자.  
* 엘리베이터가 이동 중이면 모터를 구동시키지 않는다.  

현대 모터를 사용하여 문을 제어한다면 다음과 같이 설계할 수 있다.

![door](/img/template_hyundai1.svg)
```c++
enum DoorStatus { CLOSED, OPENED }
enum MotorStatus { MOVING, STOPPED }

class Door {
public:
    Door() { doorStatus = CLOSED; }
    DoorStatus getDoorStatus() { return doorStatus; }
    void close() { doorStatus = CLOSED; }
    void open() { doorStatus = OPENED; }
private:
    DoorStatus doorStatus;
};

class HyundaiMotor {
public:
    HyundaiMotor(Door* door) {
        this->door = door;
        motorStatus = STOPPED;
    }
    MotorStatus getMotorStatus() { return motorStatus; }
    void move(Direction direction) { 
        if (getMotorStatus() == MOVING) {
            return;
        }

        if (door.getDoorStatus() == OPENED) {
            door.close();
        }

        moveHyundaiMotor(direction);
        setMotorStatus(MOVING);
    }
private:
    void setMotorStatus() { this->motorStatus = motorStatus; }
    void moveHyundaiMotor(Direction direction) {
        // 모터 이동
    }
private:
    Door* door;
    MotorStatus motorStatus;
}

int main() {
    Door* door = new Door();
    HyundaiMotor* hyundaiMotor = new HyundaiMotor(door);
    hyundaiMotor.move(UP);

```

### 1. 문제점
* 다른 회사의 모터를 제어해야 한다면?  
LG 모터를 구동하는 것은 Hyundai 모터와 완전히 동일하지는 않기 때문에 HyundaiMotor 클래스를 복사한 후 일부 수정해야 한다.

```c++
class LGMotor {
public:
    void move(Direction direction) { 
        if (getMotorStatus() == MOVING) {
            return;
        }

        if (door.getDoorStatus() == OPENED) {
            door.close();
        }

        moveLGMotor(direction); // HyundaiMotor 와 다른 부분
        setMotorStatus(MOVING);
    }
}
```
대부분의 코드가 동일하며 move method의 일부 코드가 다른 것을 알 수 있다.  

*위와 같이 유사한 기능을 제공하면서 중복된 코드가 있는 경우 상속을 이용해 중복되는 코드를 상위 클래스에 모으고 다른 부분은 하위 클래스에서 구현할 수 있다.

```c++
class Motor {
public: 
    Motor(Door door) {
        this->door = door;
        motorStatus = STOPPED;
    }
    MotorStatus getMotorStatus() { return motorStatus; }
protected:
    virtual void move(Direction direction) {}
    void setMotorStatus(MotorStatus motorStatus) { this->motorStatus = motorStatus }
protected:
    Door* door;
    MotorStatus motorStatus;
};

class HyundaiMotor : public Motor {
public:
    HyundaiMotor(Door door): Motor(door) {}
private:
    void moveHyundaiMotor(Direction direction) {
        // hyundai 모터 구동
    }

    void move(Direction direction) {
        if (getMotorStatus() == MOVING) {
            return;
        }

        if (getDoorStatus() == OPENED) {
            door.close();
        }

        moveHyundaiMotor(direction);
        setMotorStatus(MOVING);  // 이 부분은 동일
    }
}
```

Motor 클래스를 HyundaiMotor, LGMotor 클래스의 상위 클래스로 정의하여, Door 클래스와의 연관 관계, motorStatus 필드, getMotorStatus, setMotorStatus 의 중복을 피할 수 있었다.  
하지만 move() 는 여전히 중복되는 코드가 존재하고 있다.

### 2. 해결책
부분적으로 코드가 중복되는 경우에는 상속을 활요하여 중복을 피할 수 있다.  
move method 를 상위 클래스로 올리고, moveHyundaiMotor, moveLGMotor 호출 부분을 하위 클래스에서 override 하는 방법이다.

```c++
public Motor {
    virtual void moveMotor(Direction direction) {}
    void move(Direction direction) {
        if (getMotorStatus() == MOVING) {
            return;
        }

        if (getDoorStatus() == OPENED) {
            door.close();
        }

        moveMotor(direction);     // 하위 클래스에서 override한 method가 호출된다.
        setMotorStatus(MOVING);
    }
}
public class HyundaiMotor : public Motor {
    void moveMotor(Direction direction) {
        // Hyundai 모터 구동
    }
}
public class LGMotor : public Motor {
    void moveMotor(Direction direction) {
        // LG 모터 구동
    }
}
```



참고  
* Java 객체지향 디자인 패턴

