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

2. Spring Bean 등록

<img src="../images/Spring의 사용/imgae2.PNG"  alt="Spring Bean 등록">

- Spring Container는 파라미터로 넘어온 설정 클래스 정보를 사용해서 Spring Bean 등록함
- @Bean(name="...")으로 Bean 이름을 직접 부여할 수도 있음
- Bean 이름은 항상 다른 이름을 부여해야 함

3. Spring Bean 의존관계 설정 - 준비 및 완료

<img src="../images/Spring의 사용/image3.PNG" alt="Spring Bean 의존관계 설정 준비">

<img src="../images/Spring의 사용/image4.PNG" alt="Spring Bean 의존관계 설정 완료">

- Spring Container는 설정 정보를 참고해서 의존관계를 주입(DI)함
- 단순히 자바 코드를 호출하는 것 같지만, 차이가 있음.(feat. 싱글톤 컨테이너)

### Container에 등록된 모든 빈 조회
> Test 파일을 만들어서 Spring Container에 등록된 모든 Bean 혹은 기본적으로 등록된 Bean을 제외한 내가 등록한 Bean 만을 찾아서 출력할 수 있음

``` java
 @Test
    @DisplayName("모든 빈 출력하기")
    public void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name = " + beanDefinitionName + " object = " + bean);
        }
    }
```

### Spring Bean 조회(가져오기)
> Spring Container 인터페이스의 getBean() 메서드를 사용해서 Bean을 조회할 수 있음. Type으로 조회할 수도 있고, 이름으로 조회할 수 있지만 Type으로 조회할 경우 같은 타입이 중복으로 있으면 Error발생. 특정 타입의 Bean을 모두 조회하는 getBeansOfType()메서드도 사용 가능

``` java
@Test
    @DisplayName("특정 타입을 모두 조회하기")
    public void findAllBeanByType() {
        Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("beansOfType = " + beansOfType);
            Assertions.assertThat(beansOfType.size()).isEqualTo(2);
        }
    }
```
getBeansOfType() 메서드를 사용해서 모든 Bean을 조회할 경우 Map 형식으로 반환인 됨

### Spring Bean 부모타입으로 조회
> 타입으로 Spring Bean 조회를 할 때 해당 타입의 자식 타입까지 모두 조회가 됨. Object 타입으로 Bean 조회하는 경우 모든 Bean이 조회됨. 이 때도 Map 형식으로 반환이 됨

``` java
    @Test
    @DisplayName("부모 타입으로 모두 조회하기")
    public void findAllBeanByParentType() {
        Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
        Assertions.assertThat(beansOfType.size()).isEqualTo(2);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }
    }
```

