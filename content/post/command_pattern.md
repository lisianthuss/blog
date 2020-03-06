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

## 예제 ( 만능 버튼 만들기 ) 

* 버튼을 누르면 램프 켜짐: Button class
* 불 키는 기능: Lamp class

Button 클래스에 Lamp 인스턴스를 전달해서 버튼을 누르는 구현이 가능하다.
```java
public static void main() {
    Lamp lamp = new Lamp();
    BUtton lampButton = new Button(lamp);
    lampButton.pressed();
}
```

### 1. 문제점
* 버튼을 눌렀을 때 불이 켜지는 대신, 다른 기능을 하려면?
* 버튼을 처음 누르면 불이 켜지고, 다시 한 번 누르면 알람을 동작하게 하려면?

### 1.1 버튼을 눌렀을 때 불이 켜지는 대신, 다른 기능을 실행하는 경우
기능을 변경하려고 Button 클래스의 코드를 수정하는 것은 OCP 위배.
```java
public void pressed() {
    // theLamp.turnOn();

    theAlarm.start();
}
```

### 1.2 버튼을 처음 누르면 불이 켜지고, 다시 한 번 누르면 알람을 동작하게 하려면?
기능 변경을 위해 Button 클래스를 수정해야 한다.  
기능을 추가할 때마다 코드를 수정해야 하기 때문에 Button 클래스를 재사용하기 어렵다.
```java
public class Button {
    public void setMode(Mode mode) {
        this.theMode = mode;
    }
    public void pressed() {
        switch (theMode) {
            case LAMP:
                theLamp.turnOn;
                break;
            case ALARM:
                theAlarm.start()
                break;
        }
    }
}
```

### 1.3 해결책
Button 클래스의 pressed() 에서 기능을 구현하는 대신 외부에서 제공받아 캡슐화해 pressed()에서 호출하자.  
Button 클래스에서는 pressed() 에서 Command 인터페이스의 execute() 를 호출하여 알람이나 램프를 켤 수 있다.

![button](/img/command_button.svg)

```c++
class Command {
    virtual void execute();
}

class Lamp {
    void turnOn() { printf("lamp on"); }
}
class LampOnCommand : public Command {
    LampOnCommand(Lamp* lamp) { this->lamp = lamp; }
    void execute() { lamp->turnOn(); }

    Lamp* lamp;
}

class Alarm {
    void start() { printf("alarming"); }
}
class AlarmOnCommand : public Command {
    AlarmOnCommand(Alarm* alarm) { this->alarm = alarm; }
    void execute() { alarm->start(); }
    
    Alarm* alarm;
}

class Button {
    Button(Command* cmd) { setCommand(cmd); }
    void setCommand(Command newCommand) { command = newCommand; }
    void pressed() { command.execute(); }
    
    Command* command;
}

void main() {
    Lamp* lamp = new Lamp();
    Command* lampOnCommand = new LampOnCommand(lamp);

    Button button1 = Button(LampOnCommand);
    button1.pressed();

    Alarm* alarm = new Alarm();
    Command* alarmOnCommand = new AlarmOnCommand(alarm);

    Button button2 = Button(AlarmOnCommand);
    button2.pressed();

    button2.setCommand(lampOnCommand);
    button2.pressed();
}
```
버튼을 끄는 기능도 Button 클래스를 변경하지 않고 구현할 수 있다.  


버튼을 처음 눌렀을 때에는 램프를 켜고 두  번 누르면 끄는 기능을 구현해보자.

![button2](/img/command_button2.svg)

```c++
class Command {
    virtual void execute();
}
class Button {
    Button(Command* cmd) { setCommand(cmd); }
    void setCommand(Command newCommand) { command = newCommand; }
    void pressed() { command.execute(); }
    
    Command* command;
}

class Lamp {
    void turnOn() {
        printf("Lamp on");
    }
    void turnOff() {
        printf("Lamp off");
    }
}
class LampOnCommand : public Command {
    LampOnCommand(Lamp* theLamp) { this->theLamp = theLamp; }
    void execute() { theLamp->turnOn(); }
    Lamp* theLamp;
}
class LampOffCommand : public Command {
    LampOffCommand(Lamp* theLamp) { this->theLamp = theLamp; }
    void execute() { theLamp->turnOff(); }
    Lamp* theLamp;
}

void main() {
    Lamp* lamp = new Lamp();
    Command* lampOnCommand = new LampOnCommand(lamp);
    Command* lampOffCommand = new LampOffCommand(lamp);

    Button* btn = new Button(lampOnCommand);
    btn->pressed();

    btn.setCommand(lampOffCommand);
    btn->pressed();
}
```

## 예제 ( Elevator )

### Requirements
* Controller 는 목적지를 입력받아 하나의 엘리베이터를 이동시킨다. 
* Manager는 여러개의 Controller를 관리한다. 목적지가 정해지면 적절한 Controller 를 배정한다.
* Button의 역할은 두 가지다.  
-- 엘리베이터 안에서 가고자 하는 목적지를 설정.
-- 자신이 위치한 층으로 엘리베이터를 호출

![elevator](/img/command_elevator.svg)

```c++
void main() {
    ElevatorController* controller1 = new ElevatorController(1);
    ElevatorController* controller2 = new ElevatorController(2);

    ElevatorManager* em = new ElevatorManager(2);
    em.addController(controller1);
    em.addController(controller2);

    // 1층에서 1번 엘리베이터 버튼 클릭
    Command* destinationCommand1stController = new DestinationSelectionCommand(1, controller1);
    ElevatorButton* destinationButton1stFloor = new ElevatorButton(destinationCommand1stController);
    destinationButton1stFloor.pressed();

    // 2층에서 2번 엘리베이터 버튼 클릭
    Command* destinationCommand2ndController = new DestinationSelectionCommand(2, controller2);
    ElevatorButton* destinationButton2ndFloor = new ElevatorButton(destinationCommand2ndController);
    destinationButton2ndFloor.pressed();

    // 1번 엘리베이터를 2층에서 아래로 이동시키려는 버튼
    Command* requestDownCommand = new ElevatorRequestCommand(2, Direction.DOWN, em);
    ElevatorButton* requestDownFloorButton2ndFloor = new ElevatorButton(requestDownCommand);
    requestDownFloorButton2ndFloor.pressed();
}
```

참고

- Java 객체지향 디자인 패턴
