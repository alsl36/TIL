# Spring MVC의 사용

## Servlet

### 서블릿 환경 구성 및 서블릿 등록하기

``` java
@ServletComponentScan 
@SpringBootApplication
public class ServletApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServletApplication.class, args);
	}

}
```
@ServletComponentScan 어노테이션은 서블릿을 자동으로 등록해줌

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override 
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException  {
        System.out.println("HelloServlet.service");
        System.out.println("request = " + request);
        System.out.println("response = " + response);

        String username = request.getParameter("username");
        System.out.println("username = " + username);

        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");
        response.getWriter().write("hello " + username);
    }
}
```
1. HttpServlet : 등록하고자 하는 서블릿을 HttpServlet class를 상속하도록 하여 기본 동작들을 상속받음. 
2. @WebServlet : @WebServlet이라는 어노테이션은 이 클래스를 서블릿으로 등록하겠다는 의미이며, name 필드로 서블릿의 이름을 지정하고, urlPatterns 필드로 어떤 url로 요청이 들어올 때 생성할 서블릿인지를 지정가능
3. protected void service(HttpServletRequest request, HttpServletResponse response) : HTTP 요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 service() 메서드를 실행함. 이 때, client의 요청은 request 객체가 생성되면서 이 안에 담기고, 서버가 client에게 전송하는 응답 메세지는 response 객체에 담겨 전송됨
4. String username = request.getParameter("username") : request 객체의 getParameter("쿼리파라미터 필드이름") 메서드를 통해 쿼리파라미터를 가져올 수 있음
5. setContentType, setCharacterEncoding, getWriter().wirte() : response 객체의 해당 메서드들을 통해 Client에게 전송되는 응답 메세지의 contentType 필드, characterEncoding 필드, body 필드를 채워 넣을 수 있음

<img src="../../images/Spring MVC/Spring MVC의 사용/img1.PNG">

Spring Boot는 내장되어 있는 Tomcat Server를 생성하고, 이 Tomcat Server는 WAS이므로 helloServlet이라는 서블릿을 하나 생성함(서블릿 자체는 싱글톤으로 관리)

<img src="../../images/Spring MVC/Spring MVC의 사용/img2.PNG">

Client에게 HTTP 요청이 들어오면 request 객체와 response 객체가 생성되면서 URL 매핑 된 helloServlet이 호출됨. 이 서블릿이 종료될 때 response 객체가 client에게 응답으로 전송됨

> *참고*
</br>
webapp 경로에 index.html 파일을 두면, 루트 URL로 GET 요청 시 index.html 페이지가 열림

### HttpServletRequest - 기본 사용법
> Servlet은 Client에게 요청된 정보를 HttpServletRequest 객체에 담아 제공하여 개발자가 직접 request를 파싱하거나 분석하지 않아도 편리하게 각각의 필드 값들을 찾을 수 있도록 제공함

**<Start-line 정보>**
- request.getMethod() : method 종류를 반환(ex.GET, POST)
- request.getProtocol : protocol 정보 반환(ex.HTTP/1.1)
- request.getScheme() : scheme 정보 반환(ex.HTTP, HTTPS, FTP)
- request.getRequestURL() : URL 정보 반환
- request.getRequestURI() : URI 정보만 반환
- request.getQueryString() : queryData 정보를 반환
- request.isSecure() : https 사용 유무 반환

**<Header 정보>**
</br>
``` java
request.getHeaderNames().asIterator()
                .forEachRemaining(headerName -> System.out.println(headerName + ": " + headerName));
```
위의 코드를 통해 모든 header 정보들을 꺼낼 수 있음
</br>
*특정 헤더 필드만 꺼내고 싶으면, request.getHeader("Host")*

**<Header 편리한 조회>**
- request.getServerName() : Server 이름 반환
- request.getServerPort() : Server의 port 반환
- request.getLocales() : accept-language를 모두 반환
- request.getLocale() : accept-language를 하나만 반환(최우선되는 언어)
- request.getCookies() : client의 Cookie 값 반환
- request.getContentType() : content 타입 반환 (POST 요청 시 사용)
- request.getContentLength() : content 길이 반환 (POST 요청 시 사용)
- request.getCharacterEncoding() : 인코딩 방식 반환 

**<기타 정보>**
- request.getRemoteHose()
- request.getRemoteAddr()
- request.getLocalName()
- request.getLocalAddr()
- request.getLocalPort()

### HTTP 요청 데이터
> HTTP 요청 데이터를 통해 Client에서 Server로 데이터를 전달하는 방법이 크게 3가지가 존재함

- GET - 쿼리 파라미터
    - /url?username=hello&age=20
    - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
    - ex.검색, 필터, 페이징 등에서 많이 사용
- POST - HTML Form
    - HTML 파일에서 Form 태그를 사용하여 Server로 데이터 전달
    - 메시지 바디에 쿼리 파라미터 형식으로 전달 username=hello&age=20
    -content-type 필드에는 메시지 바디에 들어가는 데이터의 형식을 알려줌    
<img src="../../images/Spring MVC/Spring MVC의 사용/img3.PNG">

- HTTP message body에 데이터를 직접 담아서 요청
    - HTTP API에서 주로 사용
    - JSON, XML, TEXT 형식으로 전달 가능하지만 주로 JSON
    - POST, PUT, PATCH 메서드에서 사용 가능


**1. GET - 쿼리 파라미터**
> request 객체의 getParameterNames, getParameter 메서드를 사용하면 Query parameter 값들을 뽑아낼 수 있음

- getParameterNames : 모든 쿼리 파라미터의 필드 이름들을 반환함(iterator로 변환 후 사용 가능)
- getParameter : 파라미터의 필드 명을 매개값으로 전달하면 해당 필드의 value를 반환함

```java
request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> System.out.println(paramName + " = " + request.getParameter(paramName)));
```

*쿼리 파라미터의 필드명이 같은 복수의 파라미터 값이 존재하는 경우*
``` java
String[] usernames = request.getParameterValues("username");
```
getParamterValues 메서드를 통해 해당 필드의 모든 값들을 배열로 받아올 수 있음

**2. POST - HTML Form**
> HTML의 Form을 활용해서 client가 server에 데이터를 보내는 방법도 존재함. 이 방식의 경우 client 입장에서는 GET - Query Parameter 방식과 다르게 체감하지만, server 입장에서는 결과적으로 동일한 request를 받게 됨

POST의 HTML Form을 전송하면 웹 브라우저는 다음의 형식으로 HTTP 메세지를 만듬
- 요청 URL : http://localhost:8080/request-param
- content-type : application/x-www-form-urlencoded
- message body : username=hello&age=20

<mark>즉, server에게 도착하는 HTTP 메세지는 GET - Query Parameter 방식과 동일함</mark>
따라서, POST - HTML Form 방식에서도 Query Paramter에서 사용했던 것과 동일하게 request.getParamter() 메서드나 getParamterNames(), getParamterValues() 메서드를 사용할 수 있음

**3. HTTP 요청 데이터 - API 메세지 바디**
> HTTP message body에 데이터를 직접 담아서 client가 server에게 제공할 수 있음. 데이터 형식은 JSON, XML, TEXT 타입이 될 수 있는데 보통 JSON 형식으로 주고받음. POST, PUT, PATCH method에서 사용

1. message body 에 TEXT를 담는 경우

``` java
public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messageBody = " + messageBody);

}
```
request 안의 message body에 담긴 TEXT 형식은 request.getInputStream() 메서드를 통해서 뽑아낼 수 있음. 하지만, 이렇게 뽑은 데이터는 바이트스트림으로 나오기 때문에, String 형식으로 변환해주어야 함. 이 때는 Spring에서 지원하는 StreamUtils.copyToString() 메서드를 사용하면 쉽게 해결 가능

*content-type : text/plain*

2. message body 에 JSON을 담는 경우

``` java
public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messageBody = " + messageBody);

        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);

        System.out.println("helloData.username = " + helloData.getUsername());
        System.out.println("helloData.age = " + helloData.getAge());

}
```
JSON 형식으로 메세지가 오는 경우에도 똑같이 request.getInputStream() 메서드를 통해 body를 가져올 수 있음. 근데, 이렇게 뽑은 JSON을 문자열 형식으로 사용하는 것보다 객체 안에 담아서 사용하는 것이 훨씬 편리함

``` java
@Getter @Setter 
public class HelloData {
    private String username;
    private int age;
}
```
그래서 JSON 데이터를 담을 수 있는 클래스를 미리 만들어서 이 안에 담아낼 수 있음. 이 때, JSON을 객체로 변환시켜 주는 것이 Jackson 라이브러리의 ObjectMapper

### HttpServletResponse - 기본 사용법
> HttpServletResponse 객체는 Server가 response를 client에게 전송할 때 status-code, header, data 등의 귀찮은 설정들을 일일히 타이핑하지 않고 객체로 관리할 수 있도록 지원함

``` java
    //[status-line]
    response.setStatus(HttpServletResponse.SC_OK);

    //[response-headers]
    // response.setHeader("Content-Type", "text/plain;charset-utf-8");
    response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
    response.setHeader("Pragma", "no-cache");
    response.setHeader("my-header", "hello");
```
HtpServletResponse 객체의 setHeader() 메서드를 사용해서 Header 각각의 필드들을 채울 수도 있고,

``` java
private void content(HttpServletResponse response) {
    response.setContentType("text/plain");
    response.setCharacterEncoding("utf-8");
}
```
``` java
private void cookie(HttpServletResponse response) {
    Cookie cookie = new Cookie("myCookie", "good");
    cookie.setMaxAge(600);
    response.addCookie(cookie);
}
```
``` java
private void redirect(HttpServletResponse response) throws IOException {
    // response.setStatus(HttpServletResponse.SC_FOUND);
    // response.setHeader("Location", "/basic/hello-form.html");

    response.sendRedirect("/basic/hello-form.html");
}
```
각각의 편의 메서드를 미리 만들어두고 servlet 안에서는 이 메서드들을 호출만 하는 형식으로 response를 만들 수 있음. 각각의 편의 메서드에서는 setContentType(), setCharacterEncoding(), addCookie(), sendRedirect() 메서드들을 사용해서 보다 편리하게 response 필드를 채워나갈 수 있음. 추가로 Cookie 객체를 생성해서 cookie도 편리하게 관리 가능

### HTTP 응답 데이터 - 단순 텍스트, HTML
> HTTP 응답 메세지는 주로 단순 텍스트 응답, HTML 응답, JSON 응답(HTTP API에서 사용) 이렇게 3가지의 경우가 존재함

1. 단순 텍스트 응답
``` java
PrintWriter writer = response.getWriter();
writer.println("ok");
```
writer.println() 안에 text를 담아서 텍스트 응답 가능

2. HTML 응답
``` java
response.setContentType("text/html");
response.setCharacterEncoding("utf-8");
PrintWriter writer = response.getWriter();
writer.println("<html>");
writer.println("<body>");
writer.println("    <div>안녕?</div>");
writer.println("</body>");
writer.println("</html>");
```
content-type을 text/html로 지정한 이후에 html 파일을 전달하면 HTML 응답 가능

3. JSON 응답
``` java
response.setContentType("application/json");
response.setCharacterEncoding("utf-8");
//JSON 데이터 하나 생성
HelloData helloData = new HelloData();
helloData.setUsername("kim");
helloData.setAge(20);

String result = objectMapper.writeValueAsString(helloData);
response.getWriter().write(result);
```
content-type을 application/json 으로 지정한 이후에 response.getWriter().write() 메서드 안에 json 데이터를 매개값으로 넣어주면 JSON 데이터를 response로 응답 가능

