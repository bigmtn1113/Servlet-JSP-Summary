# 6. 상태정보 유지 기술
인터넷 웹 서비스에서는 클라이언트와 서버가 통신할 때 `HTTP` 프로토콜을 사용한다.  
HTTP는 `무연결`과 `무상태`로 동작하는 프로토콜이라 이전 연결 때의 상태정보를 서버가 유지해주지 않는다.  
그러므로 상태정보를 일정 시간 동안 유지해주는 기술이 필요한데, 이러한 기술을 `상태정보 유지 기술`이라 한다.

`클라이언트 측`에 저장하여 유지하는 기술과 `서버 측`에 저장하여 유지하는 기술이 있다.

## 6.1. 상태정보 유지
정보를 유지하지 않는 `무상태` 통신 방식은 다수의 사용자를 상대로 하는 인터넷에서 네트워크나 서버의 `오버헤드를 줄일 수 있는 장점`이 있다.
그러나 이전 서비스 처리 결과를 사용해야 할 때는 서버나 클라이언트 측에 이전의 처리 결과를 저장하여 유지하는 추가 작업을 하지 않으면
`지속적인 서비스 처리가 어렵다는 단점`도 있다.

이처럼 클라이언트나 서버에 계속된 요청에서 사용할 수 있도록 저장한 정보들을 `상태정보(State Information)`라고 한다.

### 6.1.1. 저장 위치 분류
- **클라이언트 측 저장 기술**  
  서블릿에서 쿠키(Cookie) 기능이 있는 객체
  ```java
  javax.servlet.http.Cookie
  ```

- **서버 측 저장 기술**  
  서블릿에서 서버 측에 상태정보를 저장할 수 있는 객체
  ```java
  javax.servlet.ServletContext
  javax.servlet.http.HttpSession
  javax.servlet.http.HttpServletRequest
  ```

<br/>

### 6.1.2. 유지 기간 분류
클라이언트나 서버 측에 저장된 정보들은 무한정 유지되는 것이 아니라 `기간이 한정`되어 있다.

- **웹 애플리케이션 단위 유지**  
  웹 애플리케이션이 서비스되고 있는 동안 유지
  ```java
  javax.servlet.ServletContext
  ```

- **클라이언트 단위 유지**  
  클라이언트별로 구분해서 상태정보 유지
  ```java
  javax.servlet.http.Cookie
  javax.servlet.http.HttpSession
  ```

- **요청 단위 유지**  
  클라이언트의 서비스 요청 단위로 유지
  ```java
  javax.servlet.http.HttpServletRequest
  ```

<br/>

## 6.2. ServletContext
웹 애플리케이션 단위로 서비스하는 웹서버에서 `서블릿 컨테이너`는 웹 애플리케이션 단위로 `Context`를 생성하여 관리한다.  
이 Context가 `ServletContext` 객체다.

### 6.2.1. ServletContext 생성
ServletContext는 서블릿 컨테이너와 통신하기 위해서 사용되는 메소드를 지원하는 인터페이스이다.

서블릿 컨테이너가 시작될 때 웹서버에 등록된 웹 애플리케이션 단위로 하나의 ServletContext 객체가 자동 생성된다.  
그리고 웹 애플리케이션 서비스가 중지될 때 소멸한다.  
즉, `웹 애플리케이션과 생명 주기(life cycle)`가 같다.

웹 애플리케이션 단위로 컨텍스트가 생성되는 이유는 서블릿 컨테이너가 웹 애플리케이션 단위로 `모든 자원을 관리`할 수 있게 하기 위해서이다.
즉, 웹 애플리케이션 내에 있는 모든 서블릿과 JSP 간에 정보를 공유할 수 있고, 서블릿 컨테이너에 대한 정보를 추출할 수 있게 하는
기술이 바로 ServletContext이다.

웹 애플리케이션에 존재하는 서블릿들은 동일한 ServletContext 객체를 사용한다.
- **init() 메소드를 재정의하여 추출하는 방법**
  ```java
  public class Example extends HttpServlet {
    ServletContext sc;
    @Override
    public void init(ServletConfig config) throws ServletException{
      sc = config.getServletContext();
    }
  }
  ```

- **HttpServlet을 통해 추출하는 방법**
  ```java
  public class Example extends HttpServlet {
    public void doGet(..., ...) throws ... {
      ServletContext sc = this.getServletContext();
    }
  }
  ```
  
  상속관계는 ServletConfig - GenericServlet - HttpServlet - Example 이렇게 이루어져 있다.  
  GenericServlet이 ServletConfig의 메소드들을 재정의 했으므로 Example에서 getServletConfig()를 사용할 수 있다.

<br/>

### 6.2.2. ServletContext 변수
ServletContext 객체가 갖고 있는 변수는 동일한 웹 애플리케이션에 속한 모든 페이지에서 사용할 수 있는 글로벌한 변수이다.

**ServletContext 변수 설정**  
**web.xml**
```xml
<context-param>
  <param-name>testName</param-name>
  <param-value>testValue</param-value>
</context-param>

<servlet>
  ...
```

**ServletContext 변수 추출**  
```java
  public class Example extends HttpServlet {
    public void doGet(..., ...) throws ... {
      ServletContext sc = this.getServletContext();
      String value = sc.getInitParameter("testName");
    }
  }
```

**`<context-param>` 태그의 실제 사용 용도**  
웹서버가 사용하는 환경설정 파일은 `web.xml`이지만 서비스 처리를 위해서는 각 페이지 수준의 환경설정 파일들도 읽어들이도록 설정해야 한다.
환경설정 파일들은 서버의 웹 애플리케이션 `서비스 시작과 동시에` 읽어 들여야 한다.

따라서 웹 애플리케이션 서비스 시작과 동시에 생성되는 ServletContext 객체에 `환경설정 파일`들에 대한 정보를 변수로 전달하고,
실제 환경설정을 하는 페이지에서는 ServletContext 객체를 통해 전달받은 변수를 사용해 환경설정 파일을 찾아가 설정 작업을 진행한다.

```xml
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>
    /WEB-INF/presentation_layer.xml,
    /WEB-INF/service_layer.xml,
    /WEB-INF/persistent.xml,
  </param-value>
</context-param>
```

환경설정 파일들을 읽어 들여 처리하는 객체의 값을 추출한 후 콤마(,)를 구분자로 문자열을 나눈 다음 각 파일에 대한 정보를 얻어서 처리한다.

<br/>

### 6.2.3. 서버 정보 추출
웹 애플리케이션 단위로 만들어진 ServletContext 객체를 통해 웹 애플리케이션에 관한 정보를 추출할 수 있다.

```java
public class Example extends HttpServlet {
  public void doGet(..., ...) throws ... {
    ServletContext sc = this.getServletContext();
    
    String contextPath = sc.getContextPath(); // 웹 애플리케이션 경로
    String servletContextName = sc.getServletContextName(); // 웹 애플리케이션 이름
  }
}
```

getContextPath()는 웹 애플리케이션을 찾아가기 위해 사용하는 경로를 반환한다.  
getServletContextName()은 web.xml에 `<display-name>`와 `</display-name>`사이에 지정되어 있는 웹 애플리케이션 이름을 반환한다.

<br/>

### 6.2.4. 웹 애플리케이션 단위 정보 공유
- **void setAttribute(String name, Object value)**  
  웹 애플리케이션 범위에서 공유할 데이터를 ServletContext 객체에 `등록`하는 메소드이다.  
  첫 번째 인자는 등록하는 `데이터의 이름`, 두 번째 인자는 공유하기 위해 등록하는 `데이터`이다.
  ```java
  // ShareObject 클래스가 있다고 가정하자.
  ShareObject obj = new ShareObject();
  servletContext.setAttribute("data", obj);
  ```
  
- **Object getAttribute(String name)**  
  ServletContext 객체에 등록한 데이터를 `추출`하는 메소드이다.  
  인자값으로 찾으려는 데이터의 등록된 이름을 지정하면 동일한 이름으로 등록된 데이터를 찾아서 값을 반환한다.  
  `반환 값이 Object`이니 데이터를 추출한 다음에는 항상 원래 데이터 타입으로 `캐스팅`해야 한다.
  ```java
  ShareObject obj = (ShareObject) servletContext.getAttribute("data");
  ```

- **void removeAttribute(String name)**  
  ServletContext 객체에 등록한 데이터를 `삭제`한다.  
  인자값으로 삭게할 데이터의 등록된 이름을 지정한다.
  ```java
  servletContext.removeAttribute("data");
  ```
