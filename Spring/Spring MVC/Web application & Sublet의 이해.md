# Web application & Sublet

## Web application의 이해

### Web Server, Web Application Server
> Web application 을 동작시키기 위해서는 Web Server 와 Web Application Server의 협력이 필요함

**HTTP**
</br>
HTTP(Hyper Text Transfer Protocol)은 인터넷 Web 상에서 거의 모든 형태의 데이터 전송을 담당하는 Protocol. 대표적으로 HTML, Text, Image, 음성, 영상, 파일, JSON, XML 등 모든 데이터가 가능함

**Web Server vs Web Application Server(WAS)**
</br>
Web Server(ex.NGINX, APACHE) 와 Web Application Server(ex.Tomcat, Undertow)를 구분할 수 있는데, 둘 다 Client의 요청을 받아 응답을 하지만 Web Server는 정적인 리소스(HTML, CSS, JS, Image, 영상 등)를 제공하는 역할인 반면 WAS는 Web Server의 기능도 포함하면서 동시에 프로그램 코드를 실행해서 Application Logic을 수행하는 역할을 하여 Client마다 개별적이고, 동적인 응답을 보낼 수 있음

> 그럼 WAS를 사용하여 Web Application을 구현하는가?
<img src="../../images/Spring MVC/Web application & Sublet/img1.PNG"> 이런 형태의 구성 시 WAS가 너무 많은 역할을 담당하여 서버 과부화 우려 존재, 가장 중요한 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있고, WAS 장애시 오류 화면도 노출 불가능
<img src="../../images/Spring MVC/Web application & Sublet/img2.PNG">
그래서 이런 형태로 정적 리소스는 Web Server가 처리하고, Application Logic 같은 동적인 처리가 필요하면 Web Sever 가 WAS에 요청을 위임함. 효율적인 리소스 관리가 가능하고, WAS 나 DB 장애시 WEB 서버가 오류 화면 제공 가능 

### Sublet(서블릿)

<img src="../../images/Spring MVC/Web application & Sublet/img3.PNG">

HTTP 방식으로 통신을 할 때, Client에게 요청이 들어오면 Server는 위 그림과 같은 다양한 일들을 처리해야 함.(개발자가 모두 코딩으로 구현해야 됨) 하지만 정작 중요한 동작은 비즈니스 로직을 실행하는 것.(나머지는 기계적인 동작일 뿐)
<mark>Sublet은 이러한 기계적인 동작들을 대신 처리하여 개발자가 비즈니스 로직에만 집중할 수 있도록 해줌</mark>

<img src="../../images/Spring MVC/Web application & Sublet/img4.PNG">

- urlPatterns(/hello)의 URL이 호출되면 서블릿 코드가 실행(해당 서블릿 객체가 호출됨)
- HTTP 요청 정보를 편리하게 사용할 수 있는 HttpServletRequest (요청정보를 알아서 파싱해줌)
- HTTP 응답 정보를 편리하게 제공할 수 있는 HttpServletResponse (응답 메세지를 자동으로 생성해줌)

이처럼 Servlet은 개발자가 다른 기계적인 동작들 말고, 오로지 비즈니스 로직 작성에만 집중할 수 있게 도와줌

**<HTTP 요청 시 Servlet의 구체적인 동작>**
<img src="../../images/Spring MVC/Web application & Sublet/img5.PNG">

- WAS는 Request, Response 객체를 새로 만들어서 서블릿 객체 호출
- 개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
- 개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력
- WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

**서블릿 컨테이너**
- Tomcat처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함
- 서블릿 컨테이너는 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
- 서블릿 객체는 **싱글톤**으로 관리(Request 객체와 Response 객체는 당연히 요청 때마다 새로 생성)
- JSP도 서블릿으로 변환 되어서 사용
- 동시 요청을 위한 멀티 쓰레드 처리 지원

### 동시 요청 - 멀티 쓰레드

> WAS에 요청이 들어오면 그에 맞는 서블릿을 찾아서 호출하는데, 서블릿 객체를 호출하는 주체는 "쓰레드". 이러한 쓰레드를 여러 개 Pool로 관리할 수 있는데, 이 역할을 WAS가 대신해줌

<img src="../../images/Spring MVC/Web application & Sublet/img6.PNG">

단일 쓰레드 환경에서 다중 요청이 들어올 경우, 처리중인 쓰레드를 기다리는 큐잉발생

> 그럼 요청이 들어올 때마다 쓰레드를 새로 생성해서 요청을 처리하고, 다 쓰면 제거하면 되는건가요?
<img src="../../images/Spring MVC/Web application & Sublet/img7.PNG">
이 경우 동시 요청을 알맞게 처리할 수 있지만, 쓰레드를 새로 생성하는 비용이 매우 비싸기에, 고객의 요청마다 쓰레드를 생성하면 응답속도가 느려짐. 또한 컨텍스트 스위칭 비용이 발생하고, 쓰레드 생성에 제한이 없으면, CPU 와 메모리 임계점을 넘어서 서버가 다운될 수 있음. <mark>이 문제를 해결하는 방법이 쓰레드 풀</mark>

<img src="../../images/Spring MVC/Web application & Sublet/img8.PNG">

<img src="../../images/Spring MVC/Web application & Sublet/img9.PNG">

미리 쓰레드를 일정 개수 만큼 생성해서 쓰레드 풀에 담아놓고 요청이 들어올 때마다 이 풀 안에서 꺼내 쓴 후 다 쓰면 반납. 쓰레드 풀이 비었으면 요청은 대기하거나 거절됨

<장점>
- 쓰레드가 미리 생성되어 있으므로, 쓰레드를 생성하고 종료하는 비용(CPU)이 절약되고, 응답시간이 빠름
- 생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리됨

> WAS의 주요 튜닝 포인트는 최대 쓰레드(max thread)수 인데, 너무 낮게 설정하면, 동시 요청이 많은 경우 클라이언트는 금방 응답 지연을 겪게 됨.(CPU는 더 많은 쓰레드를 처리할 수 있음에도 최대 쓰레드 수가 낮아서 제한됨) 너무 높게 설정하면 동시 요청이 많은 경우 CPU 와 메모리 임계점 초과로 서버 다운될 수 있음. 따라서 애플리케이션 로직의 복잡도, CPU, 메모리, IO리소스 상황 등에 따라 성능 테스트를 거쳐 적당한 숫자의 최대 쓰레드를 설정해야 됨

<mark>결론은 WAS는 멀티 쓰레드를 사용할 수 있는 쓰레드 풀을 지원하기에 개발자는 멀티 쓰레드 관련 코드를 신경쓰지 않아도 알아서 관리해줌. 다만, 멀티 쓰레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용해야 됨</mark>

### HTML, HTTP API, CSR, SSR

> 정적 리소스, 동적 리소스, 데이터 각각 주고받는 방식이 다름. 또한, 웹을 만드는 주체가 Client 인지 Server 인지에 따라서도 구분할 수 있음

1. 정적 리소스
<img src="../../images/Spring MVC/Web application & Sublet/img10.PNG">

고정된 HTMl, CSS, JS, 이미지, 영상 등은 Web Server가 보내줌

2. HTML 페이지
<img src="../../images/Spring MVC/Web application & Sublet/img11.PNG">

WAS는 동적으로 필요한 HTML 파일을 생성해서 HTML 파일을 직접 전달함. 이 때 WAS가 HTML 파일을 만들어야 되는데 이 기능은 타임리프를 사용해서 제작함

3. HTTP API
<img src="../../images/Spring MVC/Web application & Sublet/img12.PNG">

HTML이 아니라 데이터만 전달하는 경우 HTTP API를 사용해서 전달함. 보통 JSON 형식의 데이터를 전달함

<img src="../../images/Spring MVC/Web application & Sublet/img13.PNG">

HTTP API로 데이터를 주고받는 경우는 다양하게 존재하는데, WEB Client 외에도 APP Client, DB, 또 다른 WAS와 소통할 수 있음

> **SSR(Server Side Rendering) vs CSR(Client Side Rendering)**
</br>
HTML 최종 결과(Client에게 보여지는 화면)를 누가 만드느냐에 따라 SSR 과 CSR 로 구분 가능함. 
<img src="../../images/Spring MVC/Web application & Sublet/img14.PNG">
SSR은 Client가 특정 URL을 요청하면 Server에서 최종 HTML을 생성해서 Client에게 전달함
<img src="../../images/Spring MVC/Web application & Sublet/img15.PNG">
CSR은 Client가 특정 URL을 요청하면 Server에서 HTML 파일이 아닌 Javascript 파일과 페이지를 채우는 데 필요한 데이터들을 제공하여 Client에서 실행중인 웹 브라우저가 직접 HTML 파일을 랜더링(생성)하도록 함. 
</br>
*SSR 기술: JSP, 타임리프*
</br>
*CSR 기술: React, Vue.js*
