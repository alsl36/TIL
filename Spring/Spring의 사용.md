# Spring의 사용

## Spring Container 와 Spring Bean

### Spring Container의 생성
``` java
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

```
ApplicationContext를 Spring Container라고 하고, 일종의 인터페이스로써 여러 구현체로 Spring Container를 만들 수 있지만, 일반적으로 애노테이션 기반의 클래스로 만듦. 즉, AnnotationConfigApplicationContext(AppConfig.class)는 Spring Container의 구현체

### Spring Container의 생성 과정
1. Spring Container 생성

<img src="../images/Spring의 사용/image1.PNG" alt="Spring Container 생성">

- Spring Container 안에 Spring Bean 저장소가 생김
- Spring Bean 저장소는 Spring Container를 생성할 때 매개값으로 전달한 구성 정보를 바탕으로 채워 넣음