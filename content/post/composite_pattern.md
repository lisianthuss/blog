---
title: "Composite Pattern"
date: 2020-03-03T13:22:14+09:00
draft: false
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

## 예제 ( 컴퓨터에 추가 장치 지원하기 )
컴퓨터의 구성 장치를 Keyboard, Body, Monitor 클래스로 정의할 수 있다.

![computer](/img/composite_computer.svg)
```c++
class Computer {
    Body body;
    Keyboard keyboard;
    Monitor monitor
};
```
Body, Keyboard, Monitor 객체를 생성할 때 가격과 소비 전력을 생성자를 통해 입력받고, Computer 객체에서 합계를 구한다.

### 1. 문제점
* 다른 부품으로 Speaker, Mouse 클래스를 추가한다면?
Speaker, Mouse 클래스를 정의하고Speaker, Mouse 클래스를 생성하고 Computer 클래스의 부분으로 표현한다.

```c++
class Computer {
    Body body;
    Keyboard keyboard;
    Monitor monitor
    Speaker speaker;
    Mouse mouse;
};
```

이 방법은 새 부품이 추가될 때마다 Computer 클래스의 수정이 필요하기 때문에 좋은 설계가 아니다.

### 2. 해결책
Computer 클래스가 Monitor, Body, Keyboard  등의 객체를 직접 가리키면, 이러한 부품의 변화에 따라 Computer 클래스의 코드도 변할 수 밖에 없다. 좋은 설계가 아니다.  
=> __구체적인 부품들을 일반화한 클래스를 정의하고 이를 Computer 클래스가 가리키게 하는 것__ 이 올바른 설계다.

![computer](/img/composite_computer2.svg)

* Computer 가 가질 수 있는 부품들을 일반화해 ComputerDevice 클래스 정의.
* Computer 클래스는 복수개의 ComputerDevice 객체를 갖는다. 
* Computer 클래스도 ComputerDevice 의 하위 클래스로 정의했다. 즉, Computer 클래스도 ComputerDevice 클래스의 일종으로 볼 수 있다. 그러므로 ComputerDevice 클래스를 이용하면 Body, Keyboard 등과 동일한 방법으로 Computer 클래스를 사용할 수 있다.

```c++
class ComputerDevice {
    virtual int getPrice() = 0;
    virtual int getPower() = 0;
};

class Keyboard : public ComputerDevice {
public:
    Keyboard(int price, int power): price(price), power(power) {}

    int getPrice() { return price; }
    int getPower() { return power; }
private:
    int price;
    int power;
}
```

Computer 클래스는 ComputerDevice의 하위 클래스이며 복수 개의 ComputerDevice 클래스를 가질 수 있다.

```c++ 
class Computer : public ComputerDevice {
public:
    void addComponents(ComputerDevice* component) { 
        componenets.push_back(component);
    }
    void removeComponent(ComputerDevice* componenet) {
        componenets.remove(componenet);
    }
    int getPrice() {
        int price = 0;
        for (int i = 0; i < components.size(); i++) {
            price += components[i].getPrice();
        }

        return price;
    }
    int getPower() { ... }
private:
    std::vector<ComputerDevice> componenets;
}

int main() {
    Body* body = new Body(100, 70);
    Keyboard* keyboard = new Keyboard(5, 2);
    Monitor* monitor = new Monitor(20, 30);

    Computer* computer = new Computer();
    computer.addComponents(body);
    computer.addComponents(keyboard);
    computer.addComponents(monitor);

    int computerPrice = computer.getPrice();
    int computerPower = computer.getPower();
}
```



출처
* Java 객체지향 디자인 패턴
