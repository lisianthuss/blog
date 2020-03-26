---
title: "Factory Method Pattern"
date: 2020-03-03T12:47:00+09:00
draft: false
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

## 예제 ( 여러가지 방식의 엘리베이터 스케줄링 방법 지원하기 )
여러 대의 엘리베이터가 있는 경우, 버튼을 눌렀을 때 하나의 엘리베이터를 선택하여 이동시킨다.  
* 어떤 엘리베이터를 이동시킬지 결정하는 여러 스케줄링 방법을 적용한다.

![factory](/img/factory_elevator.svg)

ElevatorManager는 스케줄링을 위해 하나의 ThroughputScheduler 객체와 엘리베이터별로 ElevatorController 객체를 갖는다.  
ElevatorManager는 reqeustElevator method로 요청을 받았을 때, ThroughputScheduler 클래스의 selectElavator method를 호출하여 적절한 엘리베이터를 선택하고, 선택된 엘리베이터에 해당하는 ElavatorController 객체의 gotoFloor method를 호출한다.

```c++
class ElevatorManager {
public:
    ElevatorManager(int controllerCount) {
        // controllerCount 수 만큼 ElevatorController 객체를 만들어 controllers에 추가
        for (int i = 0; i < controllerCount; i++) {
            controllers.append(new ElevatorController(i));
        }

        scheduler = new ThroughputScheduler();
    }

    void requestElevator(int destination, Direction direction) {
        int selectedElevator = scheduler->selectElevator(this, destination, direction);

        controllers.at(selectElavator)->gotoFloor(destination);
    }
private:
    std::vector<ElevatorController> controllers;
    ThroughputScheduler* scheduler;
};
class ElevatorController {
public:
    ElevatorController(int id) { 
        this->id = id; 
        curFloor = 1;
    }
    void gotoFloor(int destination) {
        std::cout << "Elevator[" << id << "]:" + curFloor << std::endl;

        curFloor = destination;
    }
private:
    int id;
    int curFloor;
}
```

### 1. 문제점
* 다른 스케줄링 전략을 사용한다면?
* 동적으로 스케줄링 전략을 변경한다면?

Strategy 패턴을 사용하여 동적으로 Scheduler를 변경할 수 있다.  
하지만 ElevatorManager 클래스는 스케줄링 전략이 변경될 때마다 requestElevator method도 수정이 필요하다.

![elevator2](/img/factory_elevator2.svg)

requestElevator method는 엘리베이터 선택과 이동이 역할이기 때문에, 엘리베이터 선택 전략에 따라 코드가 변경되는 것은 적절하지 않다.

### 1.1 해결책
주어진 기능을 제공하는 적절한 클래스 생성 작업을 별도의 클래스/method로 분리한다.  
=> 적절한 스케줄링 클래스를 생성하는 코드를requestElevator 에서 분리한다.

ElevatorManager 클래스가 ThroughputScheduler, ResponseTimeScheduler 객체를 생성하는 대신 SchedulerFactory 가 객체를 생성한다.

![elevator3](/img/factory_elevator3.svg)
```c++
enum SchedulingStrategyID { RESPONSE_TIME, THROUGHPUT, DYNAMIC }

class SchedulerFactory {
    static ElevatorScheduler getScheduler(SchedulingStrategyID strategyID) {
        ElevatorScheduler* scheduler = NULL;
        switch (strategyID) {
            case RESPONSE_TIME:
                scheduler = new ResponseTimeScheduler();
                berak;
            case THROUGHPUT:
                scheduler = new ThroughputScheduler();
                break;
            case DYNAMIC:
                int hour = 현재시간;
                if (hour < 12) // 오전
                    scheduler = new ResponseTimeScheduler();
                else
                    scheduler = new ThroughputScheduler();
                break;

        }

        return scheduler;
    }
}

class ElevatorManager {
public:
    void requestElevator(int destination, Direction direction) {
        // int selectedElevator = scheduler->selectElevator(this, destination, direction);
        // controllers.at(selectElavator)->gotoFloor(destination);

        ElevatorScheduler* scheduler = SchedulerFactory::getScheduler(strategyID);
        int selectedElevator = scheduler->selectElavator(this, destination, direction);
        controllers.at(selectElavator)->gotoFloor(destination);
    }

private:
    SchedulingStrategyID strategyID;
}

int main() {
    ElevatorManager emWithResponseTimeScheduler = new ElevatorManager(2, RESPONSE_TIME);
    emWithResponseTimeScheduler->requestElevator(10, UP);

    ElevatorManager emWithThroughputScheduler = new ElevatorManager(2, THROUGHPUT);
    emWithThroughputScheduler->requestElevator(10, UP);

    ElevatorManager emWithDynamicScheduler = new ElevatorManager(2, DYNAMIC);
    emWithDynamicScheduler->requestElevator(10, UP);
}
```
이제 ElevatorManager 의 reqeustElevator method에서는 직접 스케줄링 클래스를 생성하는 대신 SchedulerFactory 클래스의 getScheduler 를 호출하면 된다.


참고
* Java 객체지향 디자인 패턴
