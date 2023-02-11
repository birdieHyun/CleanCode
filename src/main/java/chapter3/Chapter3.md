# 3장 함수  
이 장은 함수를 잘 만드는 법을 소개한다.  
함수에 어떤 속성을 부여해야 처음 읽는 사람이 프로그램 내부를 직관적으로 파악할 수 있을까?   
  
### 작게 만들어라!  
함수를 만드는 첫째 규칙은 '작게!'다.  
함수를 만드는 둘째 규칙은 '더 작게!'다.  
  
이 규칙은 근거를 대기가 곤란하다.  
함수가 작을수록 더 좋다는 증거나 자료를 제시하기도 어렵다.  
그럼에도 불구하고 함수의 크기는 작은 것이 좋다.  
  
80년대에는 함수가 한 화면을 넘어가면 안된다고 했다.  
  
그렇다면 얼마나 짧아야 좋을까?  
  
각 함수가 이야기 하나만 표현하도록 해야한다.  
  
다시말해, if문, else문, while문 등에 들어가는 블록은 한줄이어야 한다.  
블록 안의 내용은 함수로 만드는 것이 바람직하다.  
그러면 바깥을 감싸는 함수가 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다.  
  
  
### 한 가지만 해라!  
**함수는 한 가지를 해야 한다. 그 한가지를 잘해야 한다. 그 한가지만을 해야 한다.**  
  
지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한가지 작업만 한다.    
우리가 함수를 만드는 이유는 큰 개념을 다음 추상화 수준에서 여러 단계로 수행하기 위해서이다.  
  
  
### 함수 당 추상화 수준은 하나로  
함수가 확실히 '한 가지' 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.  
  
한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다.  
  
**위에어 아래로 코드 읽기 : 내려가기 규칙** 
코드는 위에서 아래로 이야기처럼 읽혀야 좋다.   
한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.  
즉 위에서 아래로 프로그램을 읽으면, 함수 추상화 수준이 한단계씩 낮아진다.  
이것을 **내려가기** 규칙이라고 부른다. 

### Switch 쓰지마!!! 제발!!!!!!!  
switch 문은 작게 만들기 어렵다.  
case 분기가 단 두 개인 switch 문도 cleancode 저자 취향에는 너무 길며, 단일 블록이나 함수를 선호한다.  
또한 '한 가지' 작업만 하는 switch 문도 만들기 어렵다.   
본질적으로 switch 문은 N가지를 처리한다.  
불행하게도 switch 문을 완전히 피할 방법은 없다.  
하지만 각 switch 문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법은 있다.  
물론 다형성을 이용한다.  
```java
public class Switch {
    public Money calculatePa(Employee employee) throws InvalidEmployeeType{
        switch (employee.type) {
            case COMIISIONED :
                return calculateCommissionedPay(employee);
            case HOURLY :
                return calculateHourlyPay(employee);
            case SALARIED :
                return calculateSalariedPay(employee);
            default:
                throw new InvalidEmployeeType(employee.type);
        }
    }
}
```
  
위 함수에는 몇가지 문제가 있다.  
1. 함수가 길다. 새 직원 유형을 추가하면 더 길어진다. 
2. '한 가지' 작업만 수행하지 않는다.  
3. SRP를 위반한다. 코드를 변경할 이유가 여럿이기 때문이다. 
4. OCP를 위반한다. 새 직원 유형을 추가할 때마다 코드를 변경하기 때문이다. 

이 문제를 해결한 코드는 다음과 같다.  
다음 코드는 switch 문을 추상 팩토리에 꽁꽁 숨긴다. 아무에게도 보여주지 않는다.  
팩토리는 switch 문을 사용해 적잘한 Employee 파생 클래스의 인스턴스를 생성한다.  
calculatePat, isPayday, deliverPay 등과 같은 함수는 Employee 인터페이스를 거쳐 호출된다. 
그러면 다형성으로 인해 실제 파생 클래스의 함수가 실행된다. 

```java
public class Switch {
    public abstract class Employee {
        public abstract bollean isPayday();
        public abstract Money calculatePay();
        public abstract void deliverPay(Money pay);
    }
}

public interface EmployeeFactory {
    public Emplyee makeEmployee(EmployeeRecord r);
}

public class EmployeeImpl implements EmployeeFactory {
    public Emplyee makeEmployee(EmployeeRecord r) {
        switch (r.type) {
            case COMMISSIONED :
                return new ComiisionedEmployee(r);
            case  HOURLY :
                return new HourlyEmployee(r);
            case SALAREIED:
                return new SalariedEmployee(r);
        }
    }
}
```
다형적 객체를 생성하는 코드 한번만 switch 를 참는다. 
이렇게 상속 관계로 숨긴 후에는 절대로 다른 코드에 노출하지 않는다.  
물론 불가피한 상황이 생기기도 하고, 저자도 이 규칙을 위반한 경험이 여러번 있다.  
  
### 서술적인 이름을 사용하라 
함수명을 서술적으로 지으면, 함수가 하는 일을 좀 더 잘 표현하므로 훨씬 좋은 이름이다.  
그리고 private 함수도 좋은 이름을 짓도록 해야한다.  
좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않다.  
코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다.  
  
이름이 길어도 괜찮다.   
길고 서술적인 이름이 짧고 어려운 이름보다 좋다.  
길고 서술적인 이름이 길고 서술적인 주석보다 좋다.
함수 이름을 정할때는 여러 단어가 쉽게 읽히는 명명법을 사용한다.  
  
서술적인 이름을 사용하면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.  





