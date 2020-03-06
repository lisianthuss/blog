---
title: "OOP 커피 전문점"
date: 2020-03-06T09:54:58+09:00
draft: false
---

### 1. 커피 전문점에서 커피를 주문하는 과정을 객체들의 협력관계로 구현하자

#### 객체들의 연관 관계
[손님 객체] --- 커피를 선택한다--- [메뉴판 객체(아메리카노, 라떼)]  
___|  
___|  바리스타에서 커피를 주문한다  
___|  
[바리스타 객체] --- 커피를 제조한다 --- [아메리카노 객체, 카푸치노 객체, 라떼 객체]

### 2. 설계하기
* 협력을 설계할 때에는 객체가 메시지를 선택하는 것이 아니라, 메시지가 객체를 선택하게 해야 한다.  
-- 메시지를 먼저 선택하고, 그 후에 메시지를 수신하기에 적절한 객체를 선택해야 한다.  
* 할당된 책임을 수행하는 도중에 스스로 할 수 없는 일이 있다면, 다른 객체에 요청해야 한다.  
-- 이 요청이 외부로 전송되는 메시지를 정의한다.  

#### 구성도
커피를 주문하라(in: 메뉴이름) ==>  [손님] 메뉴 항목을 찾아라(in: 메뉴 이름, ret: 메뉴 항목) <==> [ 메뉴판 ]  
[손님] 커피를 제조하라(in: 메뉴항목, ret: 커피) <==> [바리스타] 제조하라 ==> [커피]


### 3. 인터페이스
각 객체가 수신하는 메시지
[손님] <== 커피를 주문하라(in: 메뉴이름)
[메뉴판] <== 메뉴 항목을 찾아라(in:메뉴이름, ret: 메뉴항목)
[바리스타] <== 커피를 제조하라(in: 메뉴항목, ret: 커피)
[커피] <== 제조하라

```c++
class Customer {
    void order(String menuName) {}
};
class Menu {
    MenuItem choose(String name) {}
};
class Barista {
    Coffee makeCoffee(MenuItem menuItem) {}
};
class Coffee {
    Coffee(MenuItem menuItem) {}
};
```
### 4. 구현하기
#### 4.1 Customer
Customer 는 Menu 에게 menuName 에 해당하는 MenuItem을 요청해서 이를 Barista 에게 전달해서 원하는 커피를 주문해야 한다.   

문제는  
* Customer는 어떻게 Menu 객체아 Barista 객체에 접근할 것인가?  

객체가 다른 객체에 메시지를 전송하기 위해서는 먼저 객체에 대한 참조를 얻어야 한다.  
==> Customer의 order() method의 인자로 Menu와 Customer 객체를 전달받는 방법을 사용하기로 하자.

Customer 의 인터페이스를 변경해보자.
```c++
class Customer {
    void order(String menuName, Menu menu, Barista barista) {
        MenuItem menuItem = menu.choose(menuName);
        Coffee coffee = barista.makeCoffee(menuItem);
        ...
    }
};
```

#### 4.2 Menu
Menu 는 menuName 에 해당하는 MenuItem을 찾아야 하기 때문에 내부적으로 MenuItem을 가지고 있어야 한다.
```c++
class Menu {
	Menu(std::list<MenuItem*> items) {
		this->items = items;
    }
	MenuItem* choose(std::string name) {
		for(auto each : items) {
			if (0 == each->getName().compare(name)) {
				return each;
			}
		}
		return NULL;
	}
	std::list<MenuItem*> items;
};
```

#### 4.3 Barista
barista는 menuItem을 제공받아 커피를 제조한다.
```c++
class Barista
{
public:
	Coffee* makeCoffee(MenuItem* menuItem)
	{
		Coffee* coffee = new Coffee(menuItem);
		return coffee;
	}
};
```

![coffee](/img/oop_coffee.svg)

### 완성된 코드
```c++
#include <iostream>
#include <string>
#include <list>
//#define _debug
class MenuItem
{
public:
	MenuItem(std::string name, int price)
	{
		this->name = name;
		this->price = price;
#ifdef _debug
	std::cout << "Constructor of MenuItem: " << this->name << std::endl;
#endif
	}
	int cost() { return this->price; }
	std::string getName() { return this->name; }

private:
	std::string name;
	int price;
};

class Menu
{
public:
	Menu(std::list<MenuItem*> items)
	{
		this->items = items;
#ifdef _debug
		std::cout << "Constructor of Menu: " << items.size() << std::endl;
#endif
	}

	MenuItem* choose(std::string name)
	{
		for(auto each : items)
		{
			if (0 == each->getName().compare(name))
			{
				return each;
			}
		}
		return NULL;
	}
	private:
	std::list<MenuItem*> items;
};

class Coffee
{
public:
	Coffee(MenuItem* menuItem)
	{
		this->name = menuItem->getName();
		this->price = menuItem->cost();
	}

	std::string getName() { return name; }
	int cost() { return price; }
private:
	std::string name;
	int price;
};

class Barista
{
public:
	Coffee* makeCoffee(MenuItem* menuItem)
	{
		Coffee* coffee = new Coffee(menuItem);
#ifdef _debug
		std::cout << "Barista: " << coffee->getName() << std::endl;
#endif
		return coffee;
	}
};

class Customer
{
public:
	void order(std::string menuName, Menu menu, Barista barista)
	{
		MenuItem* menuItem = menu.choose(menuName);
		this->coffee = barista.makeCoffee(menuItem);
	}

	Coffee* getCoffee() { return coffee; }
private:
	Coffee* coffee;
};

int main()
{
	std::list<MenuItem*> menu;
	std::list<MenuItem*>::iterator it;

	MenuItem menuItem1("Americano", 1500);
	MenuItem menuItem2("Latte", 2000);
	MenuItem menuItem3("Cappuccino", 2000);
	MenuItem menuItem4("Macchiato", 2500);
	MenuItem menuItem5("Espresso", 2500);

	menu.push_back(&menuItem1);
	menu.push_back(&menuItem2);
	menu.push_back(&menuItem3);
	menu.push_back(&menuItem4);
	menu.push_back(&menuItem5);

	Customer customer;
	Barista barista;

	customer.order("Americano", menu, barista);
	Coffee* coffee = customer.getCoffee();

	std::cout << "--- My Coffee ---" << std::endl;
	std::cout << coffee->getName() << std::endl;
	std::cout << coffee->cost() << std::endl;
	return 1;
}
```

**참고**  
> 객체지향의 사실과 오해

