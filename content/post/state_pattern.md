---
title: "State Pattern"
date: 2020-03-03T09:53:50+09:00
draft: true
---
> 상황에 따라 동일한 작업이 다른 방식으로 실행될 때, 해당 상태가 작업을 수행하도록 위임하는 디자인 패턴이다.  

### 일을 수행할 때 상태 하나하나가 어떤 상태인지 확인해서 일을 다르게 수행한다면?  
1. 복잡한 조건식이 있는 코드가 만들어진다.  
2. 코드를 이해하거나 수정하기 어렵게 된다.  

### State Pattern을 사용하려면?  
*어떤 행위를 해야할 때, 상태에 행위를 수행하도록 위임한다*   
1. 시스템의 각 상태를 **클래스로 분리**해 표현
2. 각 클래서에서 수행하는 행위들을 **method로 구현**
3. 외부로부터 캡슐화하기 위해 **인터페이스 생성**
4. 시스템의 각 상태를 나타내는 클래스가 **실체화**하게 한다.

![state](/img/state.svg)

* State  
-- 시스템의 모든 상태에 공통적인 인터페이스를 제공
* State1, State2, State3   
-- Context 객체가 요청한 작업을 자신의 방식으로 실행
* Context  
-- State를 이용하여 원하는 작업을 수행. 생태를 변경하는 setState method가 있고 실제 행위를 실행하는 대신 해당 상태 객체에 행위 실행을 요청하는 request method가 있다

## 예제 ( 형광등 만들기 )
### 1. 형광등 동작
형광등 상태와 누른 버튼에 따라 다음과 같이 동작한다.  

|형광등 상태|On 버튼|Off 버튼|
|:---:|:---:|:---:|
|On|반응 없음|형광등 Off|
|Off|형광등 On|반응 없음|

```c++
class Light {
    const int ON = 0; // 형광등이 켜진 상태
    const int OFF = 1; // 형광등이 꺼진 상태
    int state = OFF;
    
    void on_btn_pushed() {
        if (state == ON)
            // nothing
        else
            print("light on")
            state = ON;
    }
    void off_btn_pushed() {
        if (state == OFF)
            // nothing
        else
            print("light off")
            state = OFF;
    }
}
```

### 2. 문제점
형광등에 새로운 상태를 추가한다면?

|형광등 상태|On 버튼|Off 버튼|
|:---:|:---:|:---:|
|On|취침등|형광등 Off|
|Off|형광등 On|반응 없음|
|취침등|형광등 On|반응 없음|  

  
추가되는 조건에 따라 if-else 를 추가하면 아름답지 않은 코드가 만들어진다.
```c++
class Light {
private:
    const int ON = 0; // 형광등이 켜진 상태
    const int OFF = 1; // 형광등이 꺼진 상태
    const int SLEEPING = 2;
    int state = OFF;
    
public:
    void on_btn_pushed() {
        if (state == ON)
            // nothing
        else if (state == SLEEPING)
            print("light on")
            state = ON;
        else
            print("light on")
            state = ON;
    }
    void off_btn_pushed() {
        if (state == OFF)
            // nothing
        else
            print("light off")
            state = OFF;
    }
}
```

### 3. 해결책

변하는 부분을 찾아서 __캡슐화__ 해보자.   
목표는 현재 시스템이 어떤 상태에 있는지와 상관없게 구성하고, 상태 변화에도 독립적이도록 수정하는 것이다.

![light](/img/state_light.svg)

Strategy 패턴과 구조가 동일하다. Light 클래스에서 구체적인 상태 클래스가 아닌 State 인터페이스만 참조하므로, 현재 상태와 무관하게 코드를 작성할 수 있다.  
또한 매번 On, Off 인스턴스를 생성하지 않고 singleton 기반으로 변경하면 다음과 같다.

```java
interface State {
    public void on_button_pushed(Light light);
    public void off_button_pushed(Light light);
}

public class On implements State {
    private static On on = new On();
    public void on_button_pushed(Light light) {}
    public void off_button_pushed(Light light)
    {
        print("light off");
        light.setState(Off.getInstance());
    }
}

public class Off implements State {
    private static Off off = new Off();
    public void on_button_pushed(Light light) {
        print("light on");
        light.setState(On.getInstance());
    }
    public void off_button_pushed(Light light){}
}

public class Light {
    private State state;
    public Light() {
        state = new Off();
    }
    public void setState(State state) {
        this->state = state;
    }
    public void on_button_pushed() {
        this->state.on_button_pushed(this);
    }
    public void off_button_pushed(Light light) {
        this->state.off_button_pushed(this);
    }
}
```

### 4. Strategy Pattern vs. State Pattern
같은점
* 행위를 별도의 클래스로 캡슐화한다.  
-- Strategy Pattern: Strategy class  
-- State Pattern: State class
* 실제 작업 수행을 위임한다
* 실행 중에 행위를 변경할 수 있다.

차이점
* State Pattern 에서 클라이언트는 상태 객체와 관련된 어떤 지식도 없다.
* 행위를 변경하는 주체가 다르다.  
-- Strategy Pattern: 행위 시작과 변경이 Client 클래스에서 이루어지며 통제된다.  
-- State Pattern: 상태 변경을 State 요소를 구현한 클래서 자신이 수행.   

참고

- Java 객체지향 디자인 패턴
