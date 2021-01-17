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

<br/>

## 5.2. 서블릿 변수
### 5.2.1. 서블릿 동시 요청
웹 프로그램을 개발하는 방식은 두 가지가 있다.  
`CGI` 방식과 `애플리케이션 서버가 실행`하는 방식이다.

- **CGI 프로그램 실행 방식**  
  클라이언트로부터 요청이 들어올 때마다 `독립적인 프로세스`가 만들어지며,  
  메모리에는 프로세스를 실행하기 위한 데이터가 로딩된다.
  
  CGI 실행 방식은 여러 사용자 요청이 빈번한 성격의 웹 서비스로는 적합하지 않다.  
  왜냐하면, 클라이언트의 요청 수에 비례하여 프로세스가 만들어지고 그만큼 메모리 사용량도 증가하기 때문이다.  
  따라서 `대량의 트래픽`이 발생할 수 있는 서비스에는 `서버에 부하`가 가중될 수 있다.

- **서블릿 실행 방식**  
  서블릿은 `서블릿 컨테이너`라고 하는 웹 애플리케이션 서버가 실행하는 방식이다.
  
  서블릿이 최초 요청이면 서블릿 객체의 메모리 로딩과 객체 생성, 그리고 init() 메소드가 호출되는데  
  이 작업을 진행하기 전에 `프로세스`가 생성된다.
  
  service() 메소드는 최초 요청 시 만들어진 프로세스 안에 작은 프로세스를 만들고, 그 작은 프로세스 안에서 실행된다.  
  이와 같은 작은 프로세스를 `스레드(Thread)`라고 한다.
  
  메모리에 로딩하는 데이터양을 보면 최초 요청 시에 데이터를 로딩했다면, 두 번째 이후의 요청부터는  
  기존의 프로세스를 `그대로 사용`하고 새로운 스레드를 만들기 위한 데이터만 로딩하면 된다.  
  따라서 메모리 사용량이 적어진다.

- **차이**  
  `CGI 프로그램`은 요청할 때마다 독립적으로 프로세스를 만들어서 실행하지만,  
  `서블릿`은 최초 요청 시 하나의 프로세스를 생성하고 이후부터는 이 프로세스 안에 스레드만 생성하여 실행한다.
  
  그래서 CGI 프로그램 실행 방식보다 서블릿 실행 방식이 서버 부하나 메모리 사용 면에서 더 `효율적`이다.

<br/>

### 5.2.2. 서블릿 변수 특징
서블릿을 개발하면서 변수를 사용할 때 `멤버변수`인지 `지역변수`인지 구분하여 사용해야 한다.

서블릿은 웹서버에서 실행하는 프로그램으로서 여러 클라이언트가 `하나의 서블릿`을 동시에 실행할 수 있어야 하므로  
여러 클라이언트가 `공통`으로 사용하는 데이터가 있을 수 있고, 또는 각각의 클라이언트가 `독립적`으로 사용해야 하는  
데이터가 있을 수 있다.  
전자는 `멤버변수`로, 후자는 `지역변수`로 선언해야 한다.

`멤버변수`는 객체 생성 시 `힙(heap)` 메모리에 생성되며 서블릿을 실행하는 클라이언트들이 `공통`으로 사용한다.  
그러나 service() 메소드가 사용하는 `지역변수`는 `스택(stack)` 메모리에 생성되며, 클라이언트마다 `독립적`으로 사용한다.  
이는 서블릿의 service() 메소드를 실행하는 스레드마다 스택 메모리가 독립적이기 때문이다.

즉, 서블릿의 `지역변수`는 각 요청마다 스레드별로 `스택` 영역에 할당되어 사용되지만  
`멤버변수`는 `힙` 영역에 할당되어 서블릿을 실행하는 여러 스레드에서 공유한다.

<br/>

### 5.2.3. 서블릿 지역 변수
서블릿의 `지역변수`는 여러 클라이언트가 동시에 요청했을 때, 요청마다 `개별적`으로 할당된다.  
그러므로 각 클라이언트의 요청 수만큼 메모리 영역을 개별적으로 할당하여 `지역적`으로 처리할 때만 활용한다.

```java
public class Example extends HttpServlet {
  public void doGet(..., ...) throws ... {
    String str = request.getParameter("msg");
  }
}
```

2개의 브라우저를 준비하고 하나는 GET 방식으로 msg값에 one을 넘기고  
다른 하나는 GET 방식으로 msg값에 two을 넘기면 각각 str 값은 one과 two가 된다.  
즉, 지역변수 str은 클라이언트 간에 서로 영향을 받지 않고 `독립적`으로 사용이 가능하다.

<br/>

### 5.2.4. 서블릿 멤버 변수
동일한 서블릿을 여러 클라이언트가 동시에 요청했을 때 서블릿 객체는 `하나만 생성`되어 `멀티 스레드`로 동작하므로  
서블릿의 멤버변수는 여러 클라이언트가 `공유`하게 된다.  
그러므로 각 클라이언트들의 동시 요청 수와 관계없이 하나의 메모리 공간을 활용하여 `전역적`으로 처리할 때만 활용한다.

```java
public class Example extends HttpServlet {
  String str;
  public void doGet(..., ...) throws ... {
    str = request.getParameter("msg");
  }
}
```

2개의 브라우저를 준비하고 하나는 GET 방식으로 msg값에 one을 넘기고  
다른 하나는 GET 방식으로 msg값에 two을 넘기면 str 값은 one이 였다가 two가 된다.  
즉, 멤버변수 str은 클라이언트별로 서로 `공유`된다.
