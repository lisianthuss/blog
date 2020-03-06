---
title: "Singleton Pattern"
date: 2020-03-03T09:28:31+09:00
draft: true
---
> 인스턴스가 하나만 생성되는 것을 보장하며 어디에서든 이 인스턴스에 접근할 수 있는 디자인 패턴이다

![singleton](/img/singleton.svg)

## 예제 ( 프린터 관리자 만들기 )

### 1. 단 하나의 프린터(객체)만 생성해 어디서든 참조할 수 있게 해보자.

new Printer() 가 호출되지 않도록 생성자를 private로 변경하고, 한 번만 호출되도록 getPrinter에 static 속성을 부여하면, Printer 인스턴스가 생성된 상황이라면 그 인스턴스를 반환하며, 없다면 새로 생성해서 반환한다.
```java
public class Printer {
    private static Printer printer = null;
    private Printer() {}
    public static Printer getPrinter() {
        if (printer == null)
            printer = new Printer();

        return printer;
    }

    public void print(Resource r) {
        ...
    }
}
```
### 2. 문제점
race condition 발생
> 메모리와 같은 동일한 자원을 2개 이상의 스레드가 이용하려고 하는 현상  

printer 변수가 null인 것을 확인하고 스레드 A 가 인스턴스를 생성할 때, 동시에 스레드 2도 null인 것을 확인하고 인스턴스를 생성하려고 시도할 수 있다. 결국 두 개의 인스턴스가 생성된다.


### 3. 해결책
* 정적 변수에 인스턴스를 만들어 바로 초기화하는 방법  
* 인스턴스를 만드는 method에 동기화하는 방법

#### 3.1 정적 변수에 인스턴스를 만들어 바로 초기화하는 방법  

정적 변수는 객체가 생성되기 전 클래스가 메모리에 로딩될 때 만들어져 초기화가 한 번만 실행된다.  
따라 중복 생성 없이 이미 생성된 printer를 반환할 수 있다.
```java
public class Printer {
    private static Printer printer = new Printer();
    prinvate Printer() {}

    public static Printer getPrinter() {
        return printer;
    }
}
```

#### 3.2 인스턴스를 만드는 method에 동기화하는 방법
다중 스레드 환경에서 동시에 여러 스레드가 getPrinter method를 소유하는 객체이 접근하는 것을 방지하지한다.
```java
public class Printer {
    private static Printer printer = null;
    prinvate Printer() {}

    public synchronized static Printer getPrinter() {
        return printer;
    }
}
```




참고

- Java 객체지향 디자인 패턴

