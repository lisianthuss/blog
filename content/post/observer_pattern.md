---
title: "Observer Pattern"
date: 2020-03-03T11:26:50+09:00
draft: true
---
> 통보 대상 객체의 관리를 Subject 클래스와 Observer 인터페이스로 일반화하여  
> 데이터 변경을 통보하는 클래스(ConcreteSubject)는 통보 대상 클래스나  
> 객체(ConcreteObserver)에 대한 의존성을 없앨 수 있다.  
> 결과적으로 통보 대상 클래스나 대상 객체의 변경에도 ConcreteSubject 클래스의 수정 없이 그대로 사용할 수 있도록 한다.

### 언제 사용하지?
데이터의 변경이 발생했을 때, 상대 클래스나 객체에 의존하지 않으면서 데이터 변경을 통보하고자 할 때 유용하다.

![observer](/img/observer.svg)

* Observer  
-- 데이터의 변경을 통보받는 인터페이스  
-- Subject는 Observer 인터페이스의 update를 호출하여, ConcreteSubject의 데이터 변경을 ConcreteObserver에게 통보한다.
* Subject  
-- ConcreteObserver 객체를 관리  
-- Observer 인터페이스를 참조하여 Concrete Observer를 관리하므로 ConcreteObserver의 변화에 독립적일 수 있다.
* ConcreteSubject  
-- 변경 관리 대상이 되는 데이터가 있는 클래스  
-- 데이터를 변경을 위한 setState method에서 자신의 데이터인 subjectState를 변경하고  
-- Subject의 notifyObservers method를 호출해서 ConcreteObserver 객체에 변경을 통보한다.
* ConcreteObserver  
-- ConcreteSubject의 변경을 통보받는 클래스  
-- Observer 인터페이스의 update를 구현해서 변경을 통보받는다  
-- ConcreteSubject의 getState를 호출하여 변경된 데이터를 조회한다.

### 예제 ( 성적 출력 )
#### 1. 성적을 출력하는 프로그램을 만들어보자.  
먼저 점수를 입력하는 ScoreRecord 클래스, 점수를 출력하는 DataSheetView 클래스가 필요하다. 점수를 입력(ScoreRecord::addScore)할 때, ScoreRecord는 DataSheetView 객체를 참조해야 한다.

![record](/img/observer_record.svg)

ScoreRecord 객체에 점수가 추가(addScore)되면, DataSheetView 객체의 update 를 호출해서 성적을 출력해야 한다. 

```java
public class ScoreRecord {
    private List<Integer> scores = new ArrayList<Integer>();
    private DataSheetView dataSheetView;

    public void addScore(int score) {
        scores.add(score);
        dataSheetView.update();
    }
    ...
}

public class DataSheetView {
    public void update() {
        List<Integer> record = scoreRecord.getScoreRecord();
        displaySocres(record, viewCount); // 조회된 점수를 viewCount만큼 출력.
    }
}

public static main() {
    ScoreRecord scoreRecord = new ScoreRecord();

    // 3개의 점수 출력
    DataSheetView dataSheetView = new DataSheetView(scoreRecord, 3);

    scoreRecord.setDataSheetView(dataSheetView);

    for (int i = 0; i <= 5; i++) {
        int score = i * 10;
        System.out.println("Adding " + score);

        // 10, 20, 30, 40, 50을 추가. 추가할 때마다 최대 3개의 점수만 출력
        scoreRecord.addScore(score);
    }
}
```

#### 2. 문제점

* 성적을 다른 형태로 출력하려면?
* 동시 혹은 순차적으로 출력하려면?

#### 2.1 성적을 다른 형태로 출력하려면?
점수가 입력되면 점수 목록을 출력하는 대신, 최소/최대 값만 출력하라면?  
DataSheeetView 클래스 대신 MinMaxView 클래스를 추가할 필요가 있다. 그리고 ScoreRecord 클래는 데이터 변경을 DataSheetView 클래스가 아니라 MinMaxView 클래스에 전달한다.

```java
public class ScoreRecord {
    private List<Integer> scores = new ArrayList<Integer>();
    private MinMaxView minMaxView;
    
    public void addScore(int score) {
        scores.add(score);
        minMaxView.update();
    }
}
public class MinMaxView {
    private ScoreRecord scoreRecord;
    
    public void update() {
        List<Integer> record = scoreRecord.getScoreRecord();
        displayMinMax(record);
    }
    public void displayMinMax(List<Integer> record) {
        int min = Collections.min(record, null);
        int max = Collections.max(record, null);
        System.out.println("Min:" + min + "Max:" + max);
    }
}

public void main() {
    ScoreRecord scoreRecord = new ScoreRecord();
    MinMaxView minMaxView = new MinMaxView(scoreRecord);

    scoreRecord.setMinMaxView(minMaxView);

    for (int i = 0; i <= 5; i++) {
        int score = i * 10;
        System.out.println("Adding " + score);

        // 10, 20, 30, 40, 50을 추가. 추가할 때마다 최소,최대 값 출력
        scoreRecord.addScore(score);
    }
}
```
#### 2.2 동시 혹은 순차적으로 성적을 출력하는 경우
* 성적이 입력되었을 때, 최대 3개, 최대 5개, 최소/최대 값을 동시에 출력한다면?  
* 처음에는 목록으로 출력하고, 나중에는 최소/최대 값을 출력하려면?  

성적이 입력되었은 때 복수 개의 대상 클래스를 갱신하는 구조를 생각해봐야 한다.  

두 경우에 대해 DataSheetView 클래스와 MinMaxView 클래스를 활용할 수 있다.  

```java
public class ScoreRecord {
    private List<Integer> scores = new ArrayList<Integer>();
    
    // 복수 개의 DataSheetView
    private List<DataSheetView> dataSheetViews = new ArrayList<DataSheetView>();

    // 1 개의 MinMaxView
    private MinMaxView minMaxView; 

    public void addDataSheetView(DataSheetView dataSheetView) {
        dataSheetView.add(dataSheetView);
    }

    public void setMinMaxView(MinMaxView minMaxView) {
        this.minMaxView = minMaxView;
    }

    public void addScore(int score) {
        scores.add(score);
        for (DataSheetView dataSheetView:dataSheetViews) {
            dataSheetView.update() // 각 DataSheetView에 값 변경을 통보
        }

        minMaxView.update() // MinMaxView에 값 변경을 통보
    }
}

public void main() {
    ScoreRecord scoreRecord = new ScoreRecord();
    
    // 3개 목록의 DataSheetView 생성
    DataSheetView dataSheetView3 = new DataSheetView(scoreRecord, 3);

    // 5개 목록의 DataSheetView 생성
    DataSheetView dataSheetView5 = new DataSheetView(scoreRecord, 5);

    MinMaxView minMaxView = new MinMaxView(scoreRecord);

    scoreRecord.addDataSheetView(dataSheetView3);
    scoreRecord.addDataSheetView(dataSheetView5);
    scoreRecord.setMinMaxView(minMaxView);

    for (int i = 0; i <= 5; i++) {
        int score = i * 10;
        System.out.println("Adding " + score);

        // 10, 20, 30, 40, 50을 추가.
        // 추가할 최대 3개, 최대 5개 목록, 최소,최대 값 출력
        scoreRecord.addScore(score);
    }
}
```
성적 통보 대상이 변경된 것을 반영하려고 ScoreRecord 클래스를 수정했다.  이런 상황은 성적 변경을 새로운 클래스에 통보할 때마다 반복될 것이다.

#### 해결책
성적 통보 대상이 변경되더라도 ScoreRecord 클래스를 변경 없이 사용할 수 있어야 한다.  

![record2](/img/observer_record2.svg)

성적 변경을 통보받아야 하는 객체를 관리하는 기능을 구현한 Subject 클래스를 만들고 attach, detach method로 대상을 추가, 제거한다.  
DataSheetView와 MinMaxView는 Observer 라는 동일 인터페이스를 구현한 클래스이므로 Subject는 두 클래스의 객체를 같은 인터페이스로 호출할 수 있다.

```java
public interface Observer {
    public abstract void update(); // 데이터 변경을 처리
}

public interface Subject {
    private List<Observer> observers = new ArrayList<Observer>();

    public void attach(Observer observer) {
        observers.add(observer);
    }

    public void detach(Observer observer) {
        observer.remove(observer);
    }

    public void notifyObservers() {
        for (Observer o:observers) {
            o.update();
        }
    }
}

public class ScoreRecord extends Subject {
    private List<Integer> scores = new ArrayList<Integer>();

    public void addScore(int score) {
        scores.add(score);

        // 등록된 observer에게 성적 변경을 통보한다.
        notifyObservers();
    }

    public List<Integer> getScoreRecord() {
        return scores;
    }
}

public class DataSheetView implements Observer {
}
public class MinMaxView implements Observer {
}

public void main() {
    ScoreRecord scoreRecord = new ScoreRecord();
    DataSheetView dataSheetView3 = new DataSheetView(scoreRecord, 3);
    DataSheetView dataSheetView5 = new DataSheetView(scoreRecord, 5);
    MinMaxView minMaxView = new MinMaxView(scoreRecord);

    scoreRecord.attach(dataSheetView3);
    scoreRecord.attach(dataSheetView5);
    scoreRecord.attach(minMaxView);

    for (int i = 0; i <= 5; i++) {
        int score = i * 10;
        scoreRecord.addScore(score);
    }
}
```


참고

- Java 객체지향 디자인 패턴
