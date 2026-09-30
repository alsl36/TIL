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

## Component Scan

> Component Scan을 통해서 자동으로 Bean을 등록하고, @Autowired를 통해서 자동으로 의존관계도 주입해줄 수 있음

### 컴포넌트 스캔과 의존관계 자동 주입

> Bean을 수동으로 등록하던 기존 방식을 실무에서도 사용하기는 어려움. 등록해야 할 Spring Bean이 수십, 수백개가 되면 일일이 등록하기도 귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생. <mark>자동으로 Spring Bean을 등록하는 Component Scan 과 의존관계를 자동으로 등록하는 @Autowired라는 기능을 제공함</mark>

</br>

<새로운 AppConfig>
``` java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
) 
public class AutoAppConfig {
}
```
이렇게 기존의 AppConfig 파일과 달리 컴포넌트 스캔을 사용하는 AppConfig는 @Configuration 과 @ComponentScan 어노테이션만 붙여주면 됨

*참고: 기존의 AppConfig 파일이나, Test 파일에서 작성한 설정정보도 자동으로 등록되기 때문에 이를 제외하기 위해서 exludeFilters를 이용함. 일반적으로는 사용x*

``` java
@Component
public class MemoryMemberRepository implements MemberRepository {}
```
``` java
@Component
public class RateDiscountPolicy implements DiscountPolicy {}
```
이렇게 Spring Bean으로 등록할 class 앞에 @Component 어노테이션을 붙여주면 자동으로 스프링 빈이 등록됨

**의존관계 주입이 필요했던 Spring Bean 들은 어떻게 의존관계 주입을 할 수 있는가?**

``` java
@Component
public class MemberServiceImpl implements MemberService {
 private final MemberRepository memberRepository;
 @Autowired
 public MemberServiceImpl(MemberRepository memberRepository) {
 this.memberRepository = memberRepository;
 }
}
```
``` java
@Component
public class OrderServiceImpl implements OrderService {
 private final MemberRepository memberRepository;
 private final DiscountPolicy discountPolicy;
 @Autowired
 public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy
discountPolicy) {
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
 }
}
```
의존관계 주입이 필요한 class에는 생성자 위에 @Autowired 어노테이션을 붙이면 자동으로 등록된 bean 중에서 의존관계를 주입시켜 줌

1. @ComponentScan
<img src="../images/Spring의 사용/img5.PNG" alt="컴포넌트 스캔">

2. @Autowired 의존관계 자동 주입
<img src="../images/Spring의 사용/img6.PNG" alt="Autowired 의존관계 자동 주입">

### 탐색위치와 기본 스캔 대상

> 모든 자바 클래스를 컴포넌트 스캔하면 시간이 오래 걸리기에 꼭 필요한 위치부터 탐색하도록 시작 위치를 지정할 수 있음

``` java
@ComponentScan(
 basePackages = "hello.core",
}
```
*여러개의 시작 위치를 지정할 수도 있음*

<mark>만약 지정하지 않으면 @ComponentScan이 붙은 설정 정보 클래스의 패키지가 시작 위치가 됨</mark>

따라서 패키지 위치를 따로 지정하지 말고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것을 추천

**컴포넌트 스캔 기본 대상**
- @Component
- @Controller
- @Service
- @Repository
- @Configuration

해당 어노테이션들의 소스코드를 보면 모두 @Component를 포함하고 있기 때문

### Filter

> includeFilters 와 excludeFilters 를 사용해서 컴포넌트 스캔 대상을 추가로 지정하거나 제외할 대상을 지정할 수 있음

``` java
package hello.core.scan.filter;
import java.lang.annotation.*;
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```
excludeFilters 옵션으로 사용할 어노테이션을 하나 만들어 준 이후에

``` java
package hello.core.scan.filter;
@MyExcludeComponent
public class BeanB {
}
```
스캔에서 제외할 클래스에 만들어둔 어노테이션을 붙여준 다음
``` java
@Configuration 
    @ComponentScan(
        includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    static class ComponentFilterAppConfig {}
```
excludeFilters 에 내가 만들어둔 MyExcludeComponent.class 어노테이션이 붙은 클래스를 스캔에서 제외하겠다는 코드를 작성하면 됨

**FilterType 옵션**

FilterType으로 사용할 수 있는 옵션은 어노테이션 외에도 5가지 옵션이 있음
- ANNOTATION
- ASSIGNABLE_TYPE
- ASPECTJ
- REGEX
- CUSTOM
