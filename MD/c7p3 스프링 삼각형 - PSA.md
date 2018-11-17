# Chapter 7 스프링 삼각형

## 스프링 삼각형

### 3. PSA (Portable Service Abstraction) : 일관성 있는 서비스 추상화

> jdbc와 interface를 보라

먼저 표준을 정하는 것이 아니라.

스프링은 하나의 기능을 처리하는 다양한 라이브러리가 존재한다. 그것을 `adapter` 패턴을 사용해서 하나의 인터페이스로 묶어 공통되게 사용할 수 있게한다.



- DI : 전략패턴 => [DIP / OCP]
- AOP : 프록시 패턴
- PSA : 어댑터 패턴







```
웹의 독수리 오형제?
Request
Response
Cookie
Session
Application
```

