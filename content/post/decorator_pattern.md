---
title: "Decorator Pattern"
date: 2020-03-03T11:59:45+09:00
draft: true
---
> 기본 기능에 추가할 수 있는 많은 종류의 부가 기능에서 파생되는 다양한 조합을 동적으로 구현할 수 있는 패턴  
### 언제 사용하지?
* 기본 기능에 추가할 수 있는 기능의 종류가 많은 경우, 각 추가 기능을 Decorator 클래스로 정의한 후 필요한 Decorator 객체를 조합하여 추가 기능의 조합을 설계하는 방식

![decorator](/img/decorator.svg)

* Component  
-- 기본 기능을 뜻하는 ComcreteComponent와 추가 기능을 뜻하는 Decorator의 공통 기능을 정의  
-- 즉 클라이언트는 Component를 통해 실제 객체를 사용한다.
* ConcreteComponent  
-- 기본 기능을 구현하는 클래스  
* Decorator  
-- 공통 기능을 제공한다.
* ConcreteDecorator
-- Decorator의 하위 클래스로 기본 기능에 추가되는 개별적인 기능을 뜻한다.  


## 예제 ( 도로 표시 방법 조회하기 )
네비게이션 SW 에서 도로를 표시하는 기능을 구현한다.  
* 도로를 선으로 표시  
-- RoadDisplay 클래스  
* 도로의 차선을 표시(추가 기능)  
-- RoadDisplayWithLane 클래스

하위 클래스로 설계하여 다음과 같이 표현할 수 있다.
![display](/img/decorator_road1.svg)
```c++
class RoadDisplay {
public:
    void draw() { std::cout << "기본 도로 표시" << std::endl; }
}; 

class RoadDisplayWithLane : public RoadDisplay {
public:
    void draw() {
        RoadDisplay::draw();
        drawLane();
    }
    void drawLane() { std::cout << "차선 표시" << std::endl; }
};

int main() {
    RoadDisplay* road = new RoadDisplay();
    road->draw();

    RoadDisplayWithLane* roadWithLane = new RoadDisplayWithLane();
    roadWithLane->draw();

    delete roadWithLane;
    delete road;

    return 0;
}
```
### 1. 문제점
* 또다른 도로 표시 기능을 구현하고 싶다면?  
* 여러 가지 추가 기능을 조합해서 사용하고 싶다면?

#### 1.1  또다른 도로 표시 기능을 구현하는 경우
위 설계와 동일하게 RoadDisplayWithTraffic 클래스를 RoadDisplay의 하위 클래스로 설계할 수 있다.  

### 1.2 여러 가지 추가 기능을 조합하는 경우
여러 개의 기능 조합을 고려해야 하는 경우, 상속을 통한 기능 확장은 각 기능별로 클래스를 추가해야 하는 단점이 있다.
* RoadDisplay
* RoadDisplayWithLane
* RoadDisplayWithTraffic
* RoadDisplayWithLaneTraffic
* RoadDisplayWithLaneCrossing
* RoadDisplayWithTrafficCrossing
* RoadDisplayWithLaneTrafficCrossing

### 2. 해결책
조합 수가 늘어나는 문제를 해결할 수 있는 설계를 하려면, 각 추가 기능별로 개별적인 클래스를 설계하고, 기능을 추가할 때 각 클래스의 객체 조합을 이용하면 된다.


![display](/img/decorator_display.svg)
기본 기능은 RoadDisplay 객체를 사용한다. 차선을 표시하는 기능이 필요하다면, LaneDecorator 에서 차선 표시 기능만 구현하고 도로 표시 기능은 RoadDisplay 클래스의 draw method 를 호출하면 된다.

```c++
class Display {
public:
    Display() {}
    virtual ~Display() {}
    virtual void draw() = 0;
};

class RoadDisplay : public Display {
public:
    void draw() { std::cout << "기본 도로 표시" << std::endl; }
};

class DisplayDecorator : public Display {
public:
    DisplayDecorator() {}
    virtual ~DisplayDecorator() { delete decoratedDisplay; }

    DisplayDecorator(Display* display) { decoratedDisplay = display; }
    void draw() { decoratedDisplay->draw(); }
private:
    Display* decoratedDisplay;
};

class LaneDecorator : public DisplayDecorator {
public:
    LaneDecorator() {}
    virtual ~LaneDecorator() {}
    LaneDecorator(Display* decoratedDisplay): DisplayDecorator(decoratedDisplay) {}

    void draw() {
        DisplayDecorator::draw();
        drawLane();
    }
    void drawLane() { std::cout << "\t차선 표시" << std::endl; }
};

class TrafficDecorator : public DisplayDecorator {
public:
    TrafficDecorator() {}
    virtual ~TrafficDecorator() {}
    TrafficDecorator(Display* decoratedDisplay):DisplayDecorator(decoratedDisplay) {}

    void draw() {
        DisplayDecorator::draw();
        drawLane();
    }
    void drawLane() { std::cout << "\t교통량 표시" << std::endl; }
};
int main() {
    Display* road = new RoadDisplay();
    road->draw();

    Display* roadWithLane = new LaneDecorator(new RoadDisplay());
    roadWithLane->draw();

    Display* roadWithTraffic = new TrafficDecorator(new RoadDisplay());
    roadWithTraffic->draw();

    Display* roadWithLaneAndTraffic = new TrafficDecorator(new LaneDecorator(new RoadDisplay()));
    roadWithLaneAndTraffic->draw();

    delete roadWithLaneAndTraffic;

    delete roadWithTraffic;
    delete roadWithLane;
    delete road;

    return 0;
}
```
DisplayDecorator는 Display 클래스를 상속받은 RoadDisplay 객체를 가지고 draw method를 호출하는 역할을 한다. LaneDecorator, TrafficDecorator는 생성자로 Display 객체(RoadDisplay)를 받고 draw method를 호출하면, 상위 클래스인 DisplayDecorator에서 RoadDisplay의 draw를 호출하고, 이후 자신의 draw method를 호출한다.  
road, roadWithLane, roadWithTraffic 객체의 접근 이 모두 Display 클래스를 통해 이루어진다.  즉, 각 객체에 관계없이 동일한 Road 클래스를 통해 도로 정보를 표시할 수 있다.



참고

- Java 객체지향 디자인 패턴
