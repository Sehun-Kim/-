---
typora-root-url: ..\IMG
---

# Chapter 7 스프링 삼각형

## 스프링 삼각형

### 1. IoC : 제어의 역전 / DI : 의존성 주입

> 스프링은 IoC를 DI를 통해 구현

스프링은 DI를 **템플릿 콜백 패턴**을 사용하여 구현하고 있다.



#### 프로그래밍에서 의존성이란?

[의사코드]

```
1. 운전자가 자동차를 생산한다.
2. 자동차는 내부적으로 타이어를 생산한다.
```

[Java]

```java
1. new Car();

2. class Car {
    Tire tire;
    
    Car() {
        tire = new Tire();
    }
}
```

> **단순하게 말하면 의존관계는 `new` 키워드라고 볼 수 있다.**
>
> 클래스가 속성(Type)으로 가질 수 있는 모든 것이 의존성이다.

**[class diagram]**

![1542193871614](/1542193871614.png)

1. `Driver`는 `Car`를 의존하고
2. `Car`는 인터페이스 `Tire`를 의존한다.
3. `Tire`는 각 구현체에 생산을 위임한다.

**[Sequence Diagram]**

![1542194033023](/1542194033023.png)



#### 생성자를 통한 의존성 주입

[의사코드]

```
1. 운전자가 타이어를 생산한다.
2. 운전자가 자동차를 생산하며 타이어를 장착한다.
```

[Java]

```java
1. Tire tire = new KoreaTire();
2. Car car = new Car(tire);
```

> 주입이란? : 외부에서라는 뜻을 가짐
>
> 외부에서 필요한 것을 만들어서 주입시킨다면 내부는 변경할 필요가 없다.
>
> 즉, 확장성이 좋아진다.

**[Sequence Diagram]**

![1542194818931](/1542194818931.png)



**[Class Diagram]**

![1542194929631](/1542194929631.png)



#### 속성을 통한 의존성 주입

[의사코드]

```
1. 운전자가 타이어를 생산한다.
2. 운전자가 자동차를 생산한다.
3. 운전자가 자동차에 타이어를 장착한다.
```

[Java]

```java
1. Tire tire = new KoreaTire();
2. Car car = new Car();
3. car.setTire(tire); // 속성을 통해 의존성 주입
```

**[Sequence Diagram]**

![20659C3450B2111E07](/20659C3450B2111E07.png)



**[Class Diagram]**

![163BA33950B211940B](/163BA33950B211940B.png)



#### XML 파일을 사용하여 의존성 주입

실제 java 코드는 변경하지 않고 xml파일로 의존성을 변경시킬 수 있다.

[의사코드]

```
1. 운전자가 종합 쇼핑몰에서 타이어를 구매한다.
2. 운전자가 종합 쇼핑몰에서 자동차를 구매한다.
3. 운전자가 자동차에 타이어를 장착한다.
```

[Java]

```java
ApplicationContext context = new ClassPathXmlApplicationContext("로컬에 있는 xml 파일의 경로"); // 종합쇼핑몰 == context(즉 스프링)
1. Tire tire = (Tire)context.getBean("tire");
2. Car car = context.getBean("car", Car.class);
3. car.setTire(tire);
```

`ApplicationContext`의 역할을 할 것을 xml 파일로 불러온다.

[xml]

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
  http://www.springframework.org/schema/beans/spring-beans.xsd">

 <bean id="tire" class="domain.KoreaTire"></bean>

 <bean id="americaTire" class="domain.AmericaTire"></bean>

 <bean id="car" class="domain.Car"></bean>
 
</beans>
```



**[Class Diagram]**

![1542341581641](/1542341581641.png)

클래스 다이어그램은 전혀 변화가 없다.



**[Sequence Diagram]**

![1542341667726](/1542341667726.png)

타이어 생산과 자동차 생산을 종합쇼핑몰에 위임



![1542342941933](/1542342941933.png)





#### 스프링 설정 파일(xml)에서 속성 주입

[의사코드]

```
1. 운전자가 종합 쇼핑몰에서 자동차를 구매 요청한다.
2. 종합 쇼핑몰은 자동차를 생산한다.
3. 종합 쇼핑몰은 타이어를 생산한다.
4. 종합 쇼핑몰은 자동차에 타이어를 장착한다.
5. 종합 쇼핑몰은 운전자에게 자동차를 전달한다.
```

[Java]

```java
ApplicationContext context = new ClassPathApplicationContext("로컬에 있는 xml 파일의 경로");
1, 2, 3, 4, 5. Car car = (Car)context.getBean("car");
```

운전자(`Driver`)가 `Car`를 조립하는 것이 아니라 종합쇼핑몰(`context`)가 조립하여 넘겨준다.

[xml]

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
  http://www.springframework.org/schema/beans/spring-beans.xsd">

 <bean id="koreaTire" class="domain.KoreaTire"></bean>

 <bean id="americaTire" class="domain.AmericaTire"></bean>

 <bean id="car" class="domain.Car">
      <property name="tire" ref="koreaTire"></property>
 </bean>

</beans>
```

`property`의 `name`은  Class가 갖고있는 속성의 이름이다. `setter()`메소드를 사용하여 주입한다. `ref`는 참조할 객체의 주소. 즉, 참조변수의 이름이다. 

**ex) Car.java**

```java
class Car {
    Tire tire;
    
    public void setTire(Tire tire) {
        this.tire = tire;
    }
    
    public Tire getTire() {
        return this.tire;
    }
}
```



**[Class Diagram]**

![1542341581641](/1542341581641.png)

이전과 동일



**[Sequence Diagram]**

![1542343101665](/1542343101665.png)



![1542343155450](/1542343155450.png)



#### @Autowired를 통한 속성 주입

[의사코드]

```
1. 운전자가 종합 쇼핑몰에서 자동차를 구매 요청한다.
2. 종합 쇼핑몰은 자동차를 생산한다.
3. 종합 쇼핑몰은 타이어를 생산한다.
4. 종합 쇼핑몰은 자동차에 타이어를 장착한다.
5. 종합 쇼핑몰은 운전자에게 자동차를 전달한다.
```

[Java]

```java
ApplicationContext context = new ClassPathApplicationContext("로컬에 있는 xml 파일의 경로");
1, 2, 3, 4, 5. Car car = (Car)context.getBean("car");
```

[xml]

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
  http://www.springframework.org/schema/beans/spring-beans.xsd
  http://www.springframework.org/schema/context 
  http://www.springframework.org/schema/context/spring-context-3.1.xsd">
  <!--- context라는 네임스페이스를 사용하겠다는 설정 추가 />
  
  <context:annotation-config />
  <!--- 어노테이션을 통한 설정을 하겠다. />
    
 <bean id="koreaTire" class="domain.KoreaTire"></bean>

 <bean id="americaTire" class="domain.AmericaTire"></bean>

 <bean id="car" class="domain.Car"></bean>

</beans>
```

 `@Autowird`는 Spring에서 제공하는 어노테이션이다.

 Spring 설정 파일을 보고 자동으로 속성의 set 메서드에 해당하는 역할을 해주겠다는 의미



**ex) Car.java**

```java
class Car {
    @Autowired
    Tire tire;
}
```



만약 `xml`파일에서 `id`값을 지운다면 어떻게 될까? (대신 하나만 남겨야 한다.)

그래도 정상적으로 `tire`가 주입된다.

```xml
 <bean class="domain.AmericaTire"></bean>
```

`interface`가 구현되어 있고, 같은 Type으로 구현한 여러개의 클래스(AmericaTire, KoreaTire)가 있다면 그 때는 `id`로 구분해서 매핑한다.

![1542345940237](/1542345940237.png)





![1542345273036](/1542345273036.png)



#### @Resource를 통한 속성 주입

[의사코드]

```
1. 운전자가 종합 쇼핑몰에서 자동차를 구매 요청한다.
2. 종합 쇼핑몰은 자동차를 생산한다.
3. 종합 쇼핑몰은 타이어를 생산한다.
4. 종합 쇼핑몰은 자동차에 타이어를 장착한다.
5. 종합 쇼핑몰은 운전자에게 자동차를 전달한다.
```

[Java]

```java
ApplicationContext context = new ClassPathApplicationContext("로컬에 있는 xml 파일의 경로");
1, 2, 3, 4, 5. Car car = (Car)context.getBean("car");
```

[xml]

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
  http://www.springframework.org/schema/beans/spring-beans.xsd
  http://www.springframework.org/schema/context 
  http://www.springframework.org/schema/context/spring-context-3.1.xsd">
  <!--- context라는 네임스페이스를 사용하겠다는 설정 추가 />
  
  <context:annotation-config />
  <!--- 어노테이션을 통한 설정을 하겠다. />
    
 <bean id="koreaTire" class="domain.KoreaTire"></bean>

 <bean id="americaTire" class="domain.AmericaTire"></bean>

 <bean id="car" class="domain.Car"></bean>

</beans>
```

`@Resource`는 **java에서 제공하는 어노테이션**이다. 

`@Autowired`의 경우`interface`를 먼저 확인하고 그 다음으로 `id`를 확인한다. `@Resource`는 `id`를 먼저 확인한 후에 `interface`를 확인한다.



**ex) Car.java**

```java
class Car {
    @Resource
    Tire tire;
}
```



---

### 설정 정보

1. XML
2. @ (어노테이션)
3. Java Config
4. Groovy Config