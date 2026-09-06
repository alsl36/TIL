# Spring이란 무엇인가
> Spring은 자바 *엔터프라이즈 서비스* 애플리케이션 개발을 순수한 자바 객체(POJO)로 작성할 수 있도록 만들어진 프레임워크이다. 

*엔터프라이즈 서비스* : 트랜잭션, 보안, 스레드 관리 등과 같이 개인보다는 기업의 크고 복잡한 업무 시스템

### Spring의 탄생배경
과거의 자바표준 EJB는 복잡한 설정 강제, 테스트 코드 불가능, 매우 느린 속도의 문제가 있었음. 이를 해결하고자 나온 것이 Spring

### Spring 생태계
Spring은 Spring 프레임워크, Spring 부트, Spring 클라우드, Spring AI 등 다양한 생태계가 존재하는데, 가장 메인이 되는 것은 'Spring 프레임워크'이다. Spring 부트는 Spring 생태계의 툴들을 사용하기 쉽도록 모아놓은 역할.

## Spring 프레임워크
> 쉽게 말하면, 자바코드로 몇 백줄에 걸쳐서 작성하던 트랜잭션, DB 접근과 같은 엔터프라이즈 서비스 코드를 한 줄로 처리 가능하도록 해주는 것이 Spring 프레임워크

<details>
<summary>라이브러리 vs 프레임워크</summary>

|라이브러리|프레임워크|
|:--:|:--:|
|개발자가 주도권 가짐|프레임워크가 주도권 가짐|
|내 코드가 필요로 할 때 <br>라이브러리 함수를 직접 호출|내가 코드를 작성해서 넣어주면 <br> 적절한 시점에 내 코드를 호출해줌
</details>
</br>

> Spring 프레임워크가 없을 때는 서블릿과 서블릿 컨테이너를 직접 다루거나 EJB를 이용했음

 ### 서블릿과 서블릿 컨테이너

서블릿이란 각각의 요청에 따라 어떤 코드가 실행되는 지를 정의해놓은 함수(클래스) 느낌. NodeJs를 예로 들면 서블릿은 라우트 핸들러함수/모듈에 해당함
</br>
서블릿 컨테이너 안에는 여러 서블릿들이 담겨있음. 포트를 열고 listen 하고 있는 서블릿 컨테이너로 HTTP 요청이 들어오면(특정 URL에 해당하는 요청) 이 요청 텍스트를 읽고 파싱하여 요청 URL에 맞는 서블릿을 찾아 해당 서블릿을 실행함. 이 때, 서블릿을 실행하는 스레드를 스레드풀에서 꺼내오는데 서블릿 컨테이너가 스레드 할당 역할도 수행. 서블릿 코드가 실행되어 응답 텍스트가 만들어지면 서블릿 컨테이너가 네트워크로 해당 텍스트 전송하는 등의 역할 수행.

<mark> 과거에는 네트워크 바이트 스트림 열기/닫기, 데이터 파싱, 데이터 검증, 인증 등 모든 기능을 각각의 서블릿 안에서 모두 수행해야 했지만 Spring에서는 이 과정들을 쪼개서(모듈화) 각각의 전문 클래스가 일을 수행하는 구조 </mark>

### Spring 부트
최근에는 Spring 프레임워크를 더 편리하게 사용할 수 있는 Spring 부트에서 Spring 프레임워크를 사용함.

## 왜 스프링인가?

### 스프링의 진짜 핵심
- 스프링은 자바 언어 기반의 프레임워크
- 자바 언어의 가장 큰 특징은 **객체 지향 언어** 
- 스프링은 객체 지향 언어가 가진 강력한 특징을 살려내는 프레임워크
- <mark>스프링은 좋은 객체 지향 애플리케이션을 개발할 수 있게 도와주는 프레임워크</mark>

## 좋은 객체 지향 프로그래밍이란?

### 객체 지향 프로그래밍
프로그램을 여러 개의 독립된 단위, 즉 **객체들의 모임**으로 파악하여 각각의 객체들간의 메세지를 주고받고, 데이터를 처리하여 협력하는 관계로 보는 프로그래밍
> 프로그램의 각각의 단위(객체)를 변경함으로써 유연하고 용이한 프로그램 수정이 가능함. ex) 레고블럭 조립, 부품 갈아끼우기 등

### 역할과 구현의 분리
객체지향 프로그래밍에서는 **역할**과 **구현**을 분리해놓음. 역할은 인터페이스가, 구현은 구현객체가 함으로써 구현을 하는 객체가 바뀌어도 사용자에게 아무런 영향을 주지 않음. 역할은 고정되어 있기 때문에 구현의 변경이 유연해지고 단순해짐.
> 중요한 것은 서버의 구현 대상이 변경되어도 **클라이언트**는 구현 대상에 대해 몰라도 됨. 서버의 역할은 동일하기 때문에 클라이언트는 하던대로 동일한 메서드를 호출하거나 요청을 날리면 됨.

### 객체 지향 프로그래밍의 한계
역할(인터페이스) 자체가 변하면, 클라이언트, 서버 모두에 큰 변경 발생

<mark>인터페이스를 안정적으로 잘 설계하는 것이 중요!!</mark>

## SOLID
> 좋은 객체 지향 설계를 위한 5가지 원칙
- SRP
- OCP
- LSP
- ISP
- DIP

### 1. SRP (Single responsibility principle, 단일 책임 원칙)
- 한 클래스는 하나의 책임만 가져야 한다.
- 하나의 책임이라는 것은 모호하여 클 수도 있고, 작을 수도 있으며 문맥과 상황에 따라 다름.
- **중요한 기준은 변경**이다. 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것 ex) UI변경 등

### 2. OCP (Open/closed principle, 개방-폐쇄 원칙)
- 소프트웨어 요소는 **확장에는 열려** 있으나 **변경에는 닫혀** 있어야 한다.(기존 코드의 변경 없이 확장을 해야 함)
- 다형성을 활용해보자
- 인터페이스를 구현한 새로운 클래스를 하나 만들어서 새로운 기능을 구현
- **<문제점>**
- MemberService 클라이언트가 구현 클래스를 직접 선택
    - MemberRepository m = new MemoryMemberRepository(); //기존코드
    - MemberRepository m = new JdbcMemberRepository(); //변경코드
- **구현 객체를 변경하려면 클라이언트 코드를 변경해야 함**
- **분명 다형성을 사용했지만 OCP원칙을 지킬 수 없음**
- 이 문제를 해결하려면 객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요함 (Spring Container)

### 3. LSP (Liskov substitution principle, 리스코프 치환 원칙)
- 다형성에서 구현 객체는 인터페이스 규약을 다 지켜야 한다. 
- 단순히 컴파일에 성공하는 것이 중요x 기능적인 이야기
- ex) 자동차 인터페이스의 엑셀은 앞으로 가라는 기능, 뒤로 가게 구현객체를 만들면 LSP 위반

### 4. ISP (Interface segregation principle, 인터페이스 분리 원칙)
- 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다
- 자동차 인터페이스 -> 운전 인터페이스, 정비 인터페이스로 분리
- 사용자 클라이언트 -> 운전자 클라이언트, 정비사 클라이언트로 분리
- 분리하면 정비 인터페이스 자체가 변해도 운전자 클라이언트에 영향을 주지 않음
- 인터페이스가 명확해지고, 대체 가능성이 높아짐

### 5. DIP (Dependency inversion principle, 의존관계 역전 원칙)
- 구현 클래스에 의존하지 말고, 인터페이스에 의존해야 함
- 역할(Role)에 의존하게 해야 함 클라이언트가 인터페이스에 의존해야 유연하게 구현체글 변경할 수 있음
- 의존한다 = 그 코드에 대해 알고있다. 그 코드를 사용하고 있다.
- MemberService 클라이언트가 구현 클래스를 직접 선택하는 코드
    - MemberRepository m = new MemoryMemberRepository();
    - MemberService는 인터페이스에 의존하지만, 구현 클래스도 동시에 의존하고 있음
    - **DIP 위반** 

### 정리
- 객체 지향의 핵심은 다형성
- 다형성 만으로는 쉽게 부품을 갈아 끼우듯이 개발할 수 없음
- 다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경 됨
- **다형성 만으로는 OCP, DIP를 지킬 수 없음**
- 뭔가 더 필요하다.

### Spring과 객체 지향
- **스프링은 다음 기술로 다형성 + OCP, DIP를 가능하게 지원**
    - DI(Dependency Injection): 의존관계, 의존성 주입
    - DI 컨테이너 제공
- **클라이언트 코드의 변경 없이 기능 확장**
- 쉽게 부품을 교체하듯이 개발

### 정리
- 모든 설계에 **역할**과 **구현**을 분리하자
- 애플리케이션 설계 역시 공연을 설계 하듯이 배경만 만들어두고, 배우는 언제든지 **유연**하게 **변경**할 수 있도록 만드는 것이 좋은 객체 지향 설계
- 이상적으로는 모든 설계에 인터페이스를 부여하자
- <문제점>
- 인터페이스를 도입하면 추상화라는 비용이 발생함(코드를 디버깅하거나 확인할 때 한단계 더 거쳐야 함)

## IoC (Inversion of Control, 제어의 역전)
> 제어의 역전이란 프로그램의 흐름 제어권 (객체 생성, 호출, 생명주기 관리 등)을 개발자가 작성한 코드가 아닌 외부의 컨테이너(프레임워크)가 주도하는 설계 원칙을 의미

### 전통적인 방식 vs IoC

|구분|전통적인 제어 흐름(개발자가 주도)|제어의 역전(프레임워크가 주도)|
|:---:|:---:|:---:|
|객체 생성|개발자가 코드 안에서 직접 new로 생성|외부 컨테이너가 대신 생성|
|의존관계 연결|객체가 사용할 하위 객체를 스스로 결정 및 생성|컨테이너가 필요한 객체를 꽂아줌(DI)|
|실행 흐름|main() 함수에서 개발자가 호출 순서를 지정|프레임워크가 라이프사이클을 돌리며 내 코드를 호출|
|비유|내가 직접 배우를 캐스팅하고 무대를 세팅함|나는 연기 대본만 넘기고, 기획자가 배우를 배치함|

``` java
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    // 무엇이 들어올지 스스로 결정하지 않고, 외부에서 주는 대로 받음
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```
객체를 만들고 엮어주는 권한은 외부의 설정자(AppConfig 또는 스프링 컨테이너)로 넘어감 -> IoC

### AppConfig도 결국 개발자가 작성하는 것 아닌가?
제어권이 넘어갔다의 관점이 개발자 본인이 아니라 실제 일(비즈니스 로직)을 수행하는 객체(Service, Repository)들의 관점에서 봐야함
> AppConfig가 도입되어 구성 영역과 실행 영역으로 나누어진 이후에는 객체 내부에서 구현체를 직접 지정하여 의존관계를 제어하던 것이 AppcConfig라는 외부에서 제어 주도권을 행사함으로써 객체의 제어권이 외부로 넘어감(역전됨)

<mark>AppConfig를 넘어 스프링 프레임워크에서는 개발자가 단지 @Configuration, @Bean이라는 설명서만 선언해 두면 스프링 프레임워크 컨테이너가 스스로 그 설명서를 읽고 알아서 객체 생성, 의존성 주입 등의 제어를 행사함</mark>

### IoC, DI 그리고 OCP 와 DIP
결국 좋은 객체 지향의 가장 중요한 원칙인 OCP(구현체가 아닌 인터페이스에 의존하자)와 DIP(기존 코드를 건드리지 않고 기능을 확장하자) 이 두가지의 원칙을 지키기 위해 IoC라는 설계원칙을 도입한 것이고, IoC라는 설계원칙을 이루어내는 중요한 구현기법 중 하나가 DI.

### '정적인 의존관계' 와 '동적인 의존관계'
**정적인 의존관계**는 클래스가 사용하는 import 코드만 보고 파악 가능. 즉, 어떤 인터페이스에 의존하는 지는 쉽게 파악이 가능하고 이를 정적인 의존관계라고 함. 하지만 실제로 어떤 객체가 해당 코드 내의 인터페이스에 주입 될지 알 수 없음. 이렇게 애플리케이션 실행 시점에 객체가 생성되어 주입된 의존관계를 **동적인 의존관계**라고 함. 

<mark>이렇게 동적인 의존관계를 사용하여 의존관계를 주입하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있음</mark>

### IoC 컨테이너(DI 컨테이너)
AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 주입해주는 것을 **IoC 컨테이너** 혹은 **DI 컨테이너** 라고 함. 주로 DI 컨테이너 라는 용어가 많이 사용됨. Spring에서는 Spring이 DI컨테이너 역할을 해줌

## Spring Container
### AppConfig 스프링 기반으로 변경
``` java
@Configuration
public class AppConfig {
    
    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }
}
```
AppConfig에 설정을 구성한다는 뜻의 @Configuration을 붙여주고, 각 메서드에 @Bean을 붙여줌. 이렇게 하면 각각의 메서드를 Spring Container에 Spring Bean으로 등록을 해줌

### Client 코드에 Spring Container 적용
``` java
public class MemberApp {
    public static void main(String[] args) {
        
         ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

```
- ApplicationContext를 Spring Container라고 함. 기존에는 개발자가 AppConfig를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제는 Spring Container를 통해서 사용함. 

- Spring Container는 @Configuration이 붙은 AppConfig를 설정(구성)정보로 사용하고, 여기서 @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 Spring Container에 등록해 놓음. 이렇게 컨테이너에 등록된 객체를 **스프링 빈**이라고 함. 스프링 빈은 기본적으로 메서드의 명을 스프링 빈의 이름으로 사용함. 

- 기존에 AppConfig를 통해서 필요한 객체를 메서드 호출을 통해 얻어냈다면 이제부터는 Spring Container에서 필요한 스프링 빈(객체)를 찾아야 하는데, 스프링 빈은 applicationContext.getBean() 메서드를 사용해서 찾을 수 있음.

### BeanFactory와 ApplicationContext
<img src="../images/Spring의 이해/img1.PNG" alt="Spring Container의 상속관계">

**BeanFactory**
- Spring Container의 최상위 인터페이스
- Spring Bean을 관리하고 조회하는 역할 담당(getBean()메서드 제공 등)

**ApplicationContext**
- BeanFactory 기능을 모두 상속받아서 제공
- BeanFactory에 더해서 여러 개의 인터페이스를 모두 상속받음
- 애플리케이션 개발할 때 Bean을 관리하고 조회하는 기능은 물론, 수 많은 부가기능 제공

<img src="../images/Spring의 이해/img2.PNG" alt="ApplicationContext의 상속관계">

> ApplicationContext는 그림과 같이 여러개의 인터페이스를 모두 상속하여 다양한 편의 기능을 제공함

**ApplicationContext의 부가기능**
- 메세지소스를 활용한 국제화 기능
    - 예를 들어서 한국 웹에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
- 환경변수
    - 로컬, 개발, 운영 등을 구분해서 처리
- 애플리케이션 이벤트
    - 이벤트를 발행하고 구독하는 모델을 편리하게 지원
- 편리한 리소스 조회
    - 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

<mark>BeanFactory 와 ApplicationContext 모두 Spring Container라고 하지만 일반적으로 ApplicationContext만을 사용함</mark>

### Spring Container 구현체
<img src="../images/Spring의 이해/img3.PNG" alt="ApplicationContext 구현체">

> ApplicationContext라는 Spring Container 인터페이스를 구현하는 구현체로 다양한 클래스를 사용할 수 있는데, 어떤 언어로 만들어진 Config 파일을 매개변수에 넣어주느냐에 따라 각기 다른 구현체를 사용함

일반적으로 class 형식의 Config파일을 매개값으로 받는 구현체인 AnnotationConfigApplicationContext 구현체를 가장 많이 사용함

<mark>중요한 것은 Spring은 이렇게 다양한 형식의 설정정보들을 지원할 수 있다는 것</mark>

### Spring Bean 설정 메타 정보 - BeanDefinition

스프링이 다양한 설정 형식을 지원할 수 있는 이유가 무엇인가?

**Definition**이라는 추상화가 있기 때문!!

BeanDefinition이라는 하나의 추상화를 만들어놓고, XML은 XML로 읽어서 구현하고, JAVA는 JAVA로 읽어서 구현하는 방식을 사용. 즉, Spring Container는 자바코드인지, XML인지 몰라도 되고, 오직 BeanDefinition만 알면, 각각의 언어에 맞는 Reader가 그에 맞게 설정정보를 읽은 후 BeanDefinition 추상화에 알맞게 띄워줌

**BeanDefinition**안에는 다양한 Bean의 메타정보들이 포함됨. ex) 빈의 클래스명, 팩토리 역할의 빈 이름, 싱글톤 등등

## SingleTon
### 웹 애플리케이션과 싱글톤

> 웹 애플리케이션에서는 보통 수많은 클라이언트가 동시에 요청을 하는 경우가 많음. 이 경우에 Spring을 사용하지 않는 순수한 DI Container의 경우 클라이언트가 요청을 보낼 때마다 새로운 객체를 생성해서 반환을 해주는 문제 발생
<img src="../images/Spring의 이해/img4.PNG" alt="순수한 DI Container의 객체 생성">

- 순수한 DI 컨테이너인 AppConfig는 요청을 할 때 마다 새로운 객체를 새로 생성함
- 메모리 낭비가 매우 심함
- <mark>해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계 -> **SingleTon패턴**</mark>