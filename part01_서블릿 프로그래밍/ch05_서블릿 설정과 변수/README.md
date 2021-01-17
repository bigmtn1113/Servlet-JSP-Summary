# 5. 서블릿 설정과 변수
## 5.1. 서블릿 환경설정
### 5.1.1. web.xml
서버에서 서블릿 실행에 관한 정보를 설정할 때는 web.xml에 `<servlet>` 태그로 설정한다.  
web.xml 파일은 서버가 시작할 때 `웹서버`가 사용하는 파일이며,  
웹 애플리케이션 서비스 실행에 관한 전반적인 내용을 정의하는 `환경설정` 파일이다.  
서블릿 또한 웹 애플리케이션 서비스를 실행하기 위해 존재하는 파일이므로 web.xml에 정의한다.

**web.xml**
```xml
<servlet>
  <servlet-name></servlet-name>
  <servlet-class></servlet-class>
  <init-param>
    <param-name>id</param-name>
    <param-value>guest</param-value>
  </init-param>
  <init-param>
    <param-name>pw</param-name>
    <param-value>12345</param-value>
  </init-param>
  <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
  <servlet-name></servlet-name>
  <url-pattern></url-pattern>
</servlet-mapping>
```

- **`<init-param>` 태그**  
  서블릿에 변수를 전달할 때 사용한다.  
  서블릿을 실행하면서 필요한 값을 `외부`에서 전달받아 실행할 수 있다.  
  이렇게 하면 `동적`으로 값을 할당할 수 있는 장점도 있지만, `유지 보수성`이 높다는 장점도 있다.
  
  서블릿 소스에서 직접 값을 수정하면 `컴파일`한 다음 다시 서버에 `재배치`해야 한다.  
  그러나 이 방법으로 하면 서블릿 소스는 손댈 필요가 없고, `<init-param>` 태그의 값만 수정하면 된다.

- **`<load-on-startup>`**  
  웹 서비스가 시작될 때 서블릿 객체를 생성하도록 하는 태그이다.  
  `미리 준비`되어 있다가 서비스 처리에 관여하는 서블릿은 클라이언트 요청과 상관없이  
  웹 서비스가 `시작`될 때 객체를 생성하여 대기하고 있어야 한다.
  
  숫자 값이 낮을수록 우선순위가 높다.

<br/>

### 5.1.2. ServletConfig
만약 서블릿 속성 중에서 `<init-param>` 속성이 지정되었다면, web.xml에서 서블릿으로 파라미터를 전달한 것이므로  
서블릿 페이지 내에서 `<init-param>`의 변수를 추출해서 사용해야 한다.

**서블릿 실행 시 객체 생성 순서**  
client -> 웹서버 -> 서블릿 컨테이너 -> 최초요청 O -> 메모리 로딩 -> 서블릿 객체 생성 -> ServletConfig 객체 생성  
-> init(ServletConfig) -> HttpServletRequest, HttpServletResponse 객체 생성 -> service(HttpServletReqeust, HttpServletResponse)

마지막의 두 단계는 서블릿의 요청이 최초 요청이든 아니든 `매번 실행`된다.  
ServletConfig 객체는 서블릿이 최초로 요청이 들어왔을 때 `한 번만` 생성된다.  
따라서 서블릿 페이지당 `하나씩` 존재하며 서블릿에 대한 정보를 처리하는 기능을 한다.

```java
public class Example extends HttpServlet {
  String id, pw;

  @Override
  public void init(ServletConfig config) throws ServletException {
    id = config.getInitParameter("id");
    pw = config.getInitParameter("pw");
  }
}
```

**ServletConfig 또 다른 사용방법**  
`HttpServlet` 객체는 웹상에서 자바 프로그램이 요청되어 실행될 수 있는 조건을 가지고 있는 객체이므로  
서블릿 페이지를 작성할 때 반드시 상속받아야 하는 객체이다.

그런데 이 객체는 `GenericServlet`을 상속받는다.  
그리고 GenericServlet은 `ServletConfig` 인터페이스를 구현한 상태이다.  
GenericServlet은 ServletConfig 메소드들을 모두 `재정의` 했으므로 서블릿 페이지에서 init() 메소드를 재정의하지 않아도  
ServletConfig 객체의 메소드들을 바로 사용할 수 있다.

```java
public class Example extends HttpServlet {
  public void doPost( ..., ... ) throws ... {
    id = this.getInitParameter("id");
  }
}
```
