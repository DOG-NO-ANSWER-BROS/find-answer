# 정리

# 객체지향 5원칙 SOLID. 
2000년대 초 로버트 마틴이 주창한 [객체지향 5원칙](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod)을 두문법칙 기억법으로 정리해놓은 것이다.

|원칙|내용|
|--|--|
|SRP	| The Single Responsibility Principle.	A class should have one, and only one, reason to change.
|OCP	| The Open Closed Principle	You should be able to extend a classes behavior, without modifying it.
|LSP	| The Liskov Substitution Principle	Derived classes must be substitutable for their base classes.
|ISP	| The Interface Segregation Principle	Make fine grained interfaces that are client specific.
|DIP	| The Dependency Inversion Principle	Depend on abstractions, not on concretions.

# SRP
> 동일한 이유로 변경되는 것들은 함께 모으고, 서로 다른 이유로 변경되는 것들은 분리시킨다.
> 

로버트 마틴은 책임을 변경하려는 이유로 정의하고, 어떤 클래스나 모듈은 변경하려는 단 하나 이유만을 가져야 한다고 결론 짓는다. 

예를 들어서 보고서를 편집하고 출력하는 모듈을 생각해 보자. 이 모듈은 두 가지 이유로 변경될 수 있다. 첫 번째로 보고서의 내용 때문에 변경될 수 있다. 

두 번째로 보고서의 형식 때문에 변경될 수 있다. 이 두 가지 변경은 하나는 실질적이고 다른 하나는 꾸미기 위한 매우 다른 원인에 기인한다. 단일 책임 원칙에 의하면 이 문제의 두 측면이 실제로 분리된 두 책임 때문이며, 따라서 분리된 클래스나 모듈로 나누어야 한다. 

다른 시기에 다른 이유로 변경되어야 하는 두 가지를 묶는 것은 나쁜 설계일 수 있다.

한 클래스를 한 관심사에 집중하도록 유지하는 것이 중요한 이유는, 이것이 클래스를 더욱 튼튼하게 만들기 때문이다. 

앞서 든 예를 계속 살펴보면 편집 과정에 변경이 일어나면 같은 클래스의 일부로 있는 출력 코드가 망가질 위험이 대단히 높다.

다른 예시로 Employee라는 클래스를 정의해보자.

Employee의 예시 : 

```java
public class Employee
{
	public Money calculatePay () …
	public String reportHours () …
	public void save() …
}
```

어떤 프로그래머들은 한 클래스 안에 이 세 가지 기능이 함께 정의된 것이 가장 적절하다고 생각할지도 모른다. 결국 클래스라는 것은 공통의 변수들을 이용하여 연산을 수행하는 함수들의 집합체라고 생각하기 때문이다. 그러나 문제는, 이 세 가지 함수는 전혀 다른 이유로 변경될 수 있다는 것.

- calculatePay 함수는 급료를 계산하는 비즈니스 룰이 바뀔 때 매번 변경되어야 한다.
- reportHours 함수는 누군가가 기존과 다른 보고서 포맷을 원할 때, 그리고
- save 함수는 데이터베이스 관리자가 데이터베이스 스키마를 바꿀 때마다 변경되어야 한다.

 다양한 변경원인이 있는건 Employee 클래스를 불안정하게 만든다. 더 중요한 것은, Employee를 의존하고 있는 클래스들 역시 이 변경으로 영향을 받을 수 있다는 것이다.
 
> 따라서 책임은 하나의 특정 액터를 위한 기능 집합이다. (로버트 C. 마틴)

> 어떤 책임에 대해 액터는 해당 책임에 대한 유일한 변경의 원천이다. (로버트 C. 마틴)

'급료를 계산하는 비즈니스 룰' 을 필요로 하는 '변호사, 회계사' 객체가 액터에 해당한다.

'기존과 다른 보고서 포맷' 을 필요로 하는 '사용자' 객체가 액터에 해당한다.

'데이터베이스 스키마를 바꾸는' 역할을 하는 '데이터베이스 관리자' 객체가 액터에 해당한다.

각 액터가 필요로 하는 책임을 분리하는 방향으로 리팩토링하면 아래와 같을 것이다. 

```java
public class Employee
{
public Money calculatePay() ...
}
 
public class EmployeeReporter
{
public String reportHours(Employee e) ...
}
 
public class EmployeeRepository
{
public void save(Employee e) ...
}
```

1차 분리를 통해 결과 보고 컴포넌트들은 보고 컴포넌트로, 데이터 베이스와 관련된 모든 클래스는 저장소 컴포넌트로, 비즈니스 룰에 대한 클래스는 비즈니스 룰 컴포넌트로 들어갈 수 있게 된다.

하지만 여전히 의존관계는 존재한다. Employee가 수정되면 다른 클래스들도 재 컴파일되고 재 배포되어야 한다. 여전히 Employee를 수정하여 독립적으로 재배포 할 수는 없다. 그러나 그외 다른 클래스들은 수정 및 독립적인 재배포가 가능해진다. 여기에서 DI를 잘 활용하면, Employee 역시 독립적으로 재배포가 가능해질 것이다. 
