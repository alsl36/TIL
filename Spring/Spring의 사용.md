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

### 중복 등록과 충돌
> Component Scan에서 같은 빈 이름을 등록하면 어떻게 될까? 두가지 상황이 발생할 수 있는데, 자동 빈 등록과 자동 빈 등록이 충돌한 경우, 수동 빈 등록과 자동 빈 등록이 충돌한 경우임

1. **자동 빈 등록 vs 자동 빈 등록** 상황에서는 ConflictingBeanDefinitionException 예외 발생
2. **수동 빈 등록 vs 자동 빈 등록** 상황에서는 수동으로 등록한 빈이 우선권을 가짐. 하지만 예외없이 수동 빈이 등록이 되어버리면 실무에서는 **잡기 어려운 버그가 발생함**. 그래서 이 상황에서도 예외가 발생하도록 기본 값을 

### Component Scan 내부 흐름
> @ComponentScan은 @Component가 붙은 클래스들을 찾아 각각의 메타정보를 BeanDefinition으로 만들어 등록함. 이후 Spring 컨테이너가 초기화되면서 이 BeanDefinition을 기반으로 실제 Bean을 생성함. Bean을 생성하는 과정에서 생성자를 분석하고, 필요한 의존성 타입에 맞는 Bean 후보를 컨테이너에서 찾아 주입함. 필요한 의존 Bean이 아직 생성되지 않았다면 먼저 생성한 뒤 주입하고, 최종적으로 생성된 Bean을 singleton 저장소에 보관함

## 의존관계 자동 주입
### 다양한 의존관계 주입 방법
> 의존관계 주입은 크게 4가지 방법이 존재함. **생성자 주입**, **수정자 주입(seter 주입)**, **필드 주입**, **일반 메서드 주입** 이렇게 4가지 방법임

1. 생성자 주입

> 이름 그대로 생성자를 통해서 의존관계를 주입 받는 방법으로, 생성자 호출시점에 딱 1번만 호출되는 것을 보장함. **불변, 필수** 의존관계에 사용됨(1번만 호출되는 것이 보장되므로)

<mark>생성자가 딱 1개만 있으면(오버라이딩된 생성자가 없으면) @Autowired를 생략해도 의존관계가 자동으로 주입 됨</mark>

2. 수정자 주입(setter 주입)
> setter라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법으로, **선택, 변경** 가능성이 있는 의존관계에 사용함

``` java
@Autowired
 public void setDiscountPolicy(DiscountPolicy discountPolicy) {
 this.discountPolicy = discountPolicy;
 }
 ```
 setter를 호출하지 않아도, @Autowired 어노테이션이 붙어있으면, Container가 초기화 될 때 자동으로 호출을 하면서 의존관계 주입(DI)가 일어남. (기존과 똑같이 빈 중에서 의존관계가 될 수 있는 빈을 찾아서 주입해줌)

 <mark>@Autowired(required = false) 로 지정하면, 빈 중에서 의존관계로 주입할 빈이 없어도 오류가 발생하지 않음. (선택적 주입 가능)</mark>

3. 필드 주입
> 이름 그대로 필드에 바로 주입하는 방법. 코드가 매우 간결하지만 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있음(spring을 사용하지 않고 java로만 테스트가 불가능함) 
<mark>사용하지 말자</mark>

``` java
@Component
public class OrderServiceImpl implements OrderService {
 @Autowired
 private MemberRepository memberRepository;
 @Autowired
 private DiscountPolicy discountPolicy;
}
```
이렇게 필드 바로 앞에 @Autowired 어노테이션을 작성해서 간결하게 의존관계 주입 가능

4. 일반 메서드 주입
> 일반 메서드를 통해서 주입 받을 수 있음. 한번에 여러 필드를 주입 받을 수 있지만, <mark>일반적으로 잘 사용하지 않음</mark>

``` java
@Component
public class OrderServiceImpl implements OrderService {
 private MemberRepository memberRepository;
 private DiscountPolicy discountPolicy;
 @Autowired
 public void init(MemberRepository memberRepository, DiscountPolicy
discountPolicy) {
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
 }
}
```

### 옵션 처리
> 주입할 스프링 빈이 없어도(등록된 빈 중에서 후보를 찾지 못한 경우) 동작해야 될 때가 존재함. 자동 주입 대상이 없어도 예외 없이 동작되도록 하는 방법이 3가지가 있음

1. @Autowired(required = false)
- 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안됨
2. org.springframework.lang.@Nullable
- 자동 주입할 대상이 없으면 null이 입력됨
3. Optional<>
- 자동 주입할 대상이 없으면 Optional.empty가 입력됨

### 생성자 주입을 선택해라!
> 과거에는 수정자 주입과 필드 주입을 많이 사용했지만, 최근에는 대부분이 생성자 주입을 권장함

1. 불변
</br>
대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 변경할 일이 없음. 수정자 주입을 사용하면 누군가 실수로 변경될 수도 있고, public으로 열어두기에 좋은 설계 방법이 아님. 반면 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 불변하게 설계할 수 있음

2. 누락
</br>
프레임워크 없이 순수한 자바 코드를 단위 테스트하는 경우가 많은데 수정자 주입의 경우 의존관계를 누락한 경우 이를 알아채기 어렵지만, 생성자 주입의 경우 의존관계가 없으면 바로 컴파일 오류가 발생함. 추가로 final 키워드를 사용하면 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아줌

<mark>항상 생성자 주입을 선택해라! 그리고 가끔 옵션이 필요하면 수정자 주입을 선택해라</mark>

### 롬복과 최신 트렌드
> Lombok 라이브러리를 사용하면 생성자 주입을 사용하고도 깔끔하게 의존관계 주입을 할 수 있음

``` java
@Component
@RequiredArgsConstructor 
public class OrderServiceImpl implements OrderService {
    
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
```
@RequiredArgsConstructor 어노테이션을 붙여주면 final이 붙은 필드에 대해서 생성자를 자동으로 만들어줌. 생성자 주입 방식으로 직접 타이핑하던 코드가 자동으로 생성됨. (생성자가 하나일 때는 @Autowired 어노테이션을 생략해도 되므로 의존관계 주입에서도 문제 발생 x)

<mark>최신 트렌드는 생성자를 하나만 두고 @Autowired를 생략하거나 Lombok 라이브러리의 @RequiredArgsConstructor 어노테이션을 사용함</mark>

### 조회 빈이 2개 이상인 경우
> 의존관계 주입을 할 때 class 타입으로 조회를 하기 때문에 여러 개의 하위 클래스가 빈으로 등록된 경우 의존관계 주입 시점에서 여러 개의 빈이 조회되면서 오류가 발생함

``` java
public class OrderServiceImpl implements OrderService {
    
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
```
이런 생성자에서 만약 DiscountPolicy 타입의 빈을 조회할 때 FixDiscountPolicy 와 RateDiscountPolicy 두 개 모두 빈으로 등록되어 있는 경우 예외 발생.

*처음부터 DiscountPolicy 타입이 아닌 구체 타입(FixDiscountPolicy)으로 필드를 선언하면 안되는가?*

**하위 타입으로 지정하면 DIP를 위배하고 유연성이 떨어짐. 그리고 이름만 다르고 똑같은 타입의 빈이 2개 있을 때 해결 불가능**

1. @Autowired 필드 명 매칭
> @Autowired는 기본적으로 타입 매칭을 시도하지만, 만약 빈이 2개 이상 찾아진다면 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭함

``` java
@Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy rateDiscountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = rateDiscountPolicy;
    }
```
이렇게 파라미터 이름을 rateDiscountPolicy로 바꾸면 파라미터 이름으로 추가 매칭하여 여러 빈 중에서도 정확히 RateDiscountPolicy가 의존관계로 주입이 됨

*코드에서 파라미터 이름을 바꿨어도 Java가 컴파일한 파일인 bytecode에서는 해당 이름이 보존되지 않고 다른 이름으로 변경이 되는 경우가 있기에 이를 확인해야 됨*

2. @Qualifier 사용
> @Qualifier 라는 추가 구분자를 붙여줄 수 있음. 추가적인 구분자를 넣어주는 것이지 빈 이름을 변경하는 것이 아님을 알아야 함

``` java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
```
빈 등록 시 @Qualifier를 붙여 줌

``` java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
 @Qualifier("mainDiscountPolicy") DiscountPolicy
discountPolicy) {
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
}
```
의존관계 주입 과정에서 @Qualifier를 사용하여 추가 구분자로 활용 가능

3. @Primary 사용
> @Primary는 우선순위를 정하는 방법으로 @Autowired 시에 여러 빈이 매칭되면 @Primary가 우선권을 가짐

``` java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}
```
우선적으로 의존관계 주입을 하고 싶은 빈에 @Primary 컴포넌트를 붙여줌

이후에는 의존관계 주입을 할 때 @Primary가 붙은 빈이 우선적으로 주입 됨. @Qualifier와 달리 의존관계를 주입할 때 부가적으로 코드가 붙지 않아도 된다는 장점이 있음

**@Primary 와 @Qualifier 활용**
</br>
2개 이상의 스프링 빈이 존재할 때, 일반적으로 많이 사용하는 빈에는 @Primary를 사용하여 부가적인 코드 없이도 간결하게 해당 빈을 주입하여 주고, 특수한 경우에 다른 빈을 주입해야 되는 경우에는 해당 빈에 @Qualifier를 설정하여 넣어주면 **Spring에서는 @Primary 보다 @Qualifier에 우선권을 부여하기에** 해당 빈을 주입해 줄 수 있음

### 조회한 빈이 모두 필요할 때, List & Map
> 2개 이상의 빈이 조회가 될 때 (의존관계 주입이 될 때) 해당 타입의 스프링 빈이 다 필요한 경우가 존재함. 예를 들면 클라이언트가 할인의 종류(rate, fix)를 선택할 수 있는 패턴... 이 경우 Map 이나 List로 스프링 빈을 주입받으면 됨

``` java
static class DiscountService {
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policies;

        @Autowired 
        public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
            this.policyMap = policyMap;
            this.policies = policies;
            System.out.println("policyMap = " + policyMap);
            System.out.println("policies = " + policies);
        }
```
의존관계를 주입 받을 때부터 Map 이나 List 로 주입을 받으면 조회한 빈이 모두 담기게 됨

``` java
public int discount(Member member, int price, String discountCode) {
 DiscountPolicy discountPolicy = policyMap.get(discountCode);
 System.out.println("discountCode = " + discountCode);
 System.out.println("discountPolicy = " + discountPolicy);
 return d
 ```
 이후에 필요할 때 Map 에서 키 값으로 조회한 다음에 골라서 빈을 사용할 수 있음

 ## 빈 생명주기 콜백
 > Spring은 빈이 생성되고 설정이 끝난 직후와 빈이 종료되기 직전에 각각 콜백을 넘겨줌. 이 콜백을 이용해서 빈의 생명주기 상에서 적절한 위치에서 빈의 초기화 작업이나 사용 등을 할 수 있음

 ### 빈 생명주기 콜백의 필요성
DB에 연결하는 빈의 예시를 살펴보자
```java
public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
        connect();
        call("초기화 연결 메세지");
    }
```
``` java
@Bean 
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
```
이 경우에 문제가 발생함. Bean을 생성할 때, 생성자가 호출되면서 networkClient 객체가 생성되는데, 그 과정에서 생성자 안에 있는 connect()가 호출됨. 아직, url이 set 되지 않은 상태에서 connect가 호출되기 때문에 DB연결에 실패함. 이후에 setUrl이 호출되면서 실패되고 난 후에 제대로 된 url이 들어가는 구조임.

**connect()를 생성자에서 호출하지 말고, bean에서 생성자 호출을 끝낸 후에 호출하면 되잖아?**
</br>
이 방식의 경우 동작에 있어서는 문제가 없음. 하지만 @Bean 메서드 안에서 객체 생성, 설정, 초기화까지 모두 책임지고 있다는 점에서 유지/보수 관점 상 좋은 코드가 아님

> Spring Bean의 라이프 사이클은 다음과 같음. </br>
스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료 </br>
Bean의 초기화가 완료 된 후에 호출되는 콜백, 소멸되기 직전에 호출되는 콜백을 이용하면 Bean의 라이프 사이클 중간중간 정확한 시점에서 추가 설정이나 초기화를 할 수 있음

스프링은 크게 3가지 방법으로 Bean 생명주기 콜백을 지원함
- 인터페이스(InitializingBean, DisposableBean)
- 설정 정보에 초기화 메서드, 종료 메서드 지정
- @PostConstruct, @PreDestroy 애노테이션 지원
