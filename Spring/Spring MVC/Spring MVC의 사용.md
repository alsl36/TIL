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