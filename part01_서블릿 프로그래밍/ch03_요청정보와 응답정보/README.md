# 3. 요청정보와 응답정보
서블릿을 구현할 때 기본으로 알아야 할 `요청정보`와 `응답정보`를 알아보자.

## 3.1. 객체 생성 및 삭제
`HttpServletRequest`는 클라이언트가 서버에 보내는 요청정보를 처리하는 객체이고,  
`HttpServletResponse`는 서버가 클라이언트로 보내는 응답정보를 처리하는 객체이다.

이전 챕터에서 서블릿 처리 순서에서 최초요청일 경우 init() 메소드가 실행된다고 했었는데 
init() 메소드 실행이 끝난 다음에는 최초요청이든 그렇지 않든 서블릿 실행 요청이 들어올 때마다
서블릿 컨테이너는 `HttpServletRequest`와 `HttpServletResponse` 객체를 생성한다.

그 다음 service() 메소드를 호출하는데 이때, 생성된 `HttpServletRequeest`와 `HttpServletResponse` 객체의 주소를 인자로 넘긴다.  
service() 메소드는 인자로 받은 두 객체를 사용하여 프로그램을 구현한다.

service() 메소드가 완료되면 클라이언트에게 응답을 보내고 서버에서 실행하는 프로그램은 완료된다.  
이때, `HttpServletRequest`와 `HttpServletResponse` 객체는 소멸한다.

<br/>

## 3.2. 응답정보 처리 - HttpServletResponse
`HttpServletResponse` 인터페이스는 우리가 웹 애플리케이션을 개발하면서 응답 관련 작업을 수행할 때 사용하는
`ServletResponse` 인터페이스를 상속한다.

따라서 일반적인 네트워크 통신에서 응답과 관련된 메소드들을 포함하고 있으며, HTTP 프로토콜 통신 기반의 응답 관련
메소드들도 확장하여 포함하고 있다.

- ServletResponse: 일반적인 네트워크 통신에서의 응답 관련 메소드 제공
- HttpServletResponse: HTTP 통신 기반의 응답 관련 메소드 제공

### 3.2.1. 출력 응답
```java
PrintWriter out = response.getWriter();
out.print("<html><head><title>Test</title></head>");
out.print("<body><h1>have a nice day!!</h1></body>");
out.print("</html>");
out.close();
```

**getWriter()**  
클라이언트에게 응답 관련 작업을 수행하는데, 데이터를 출력하기 위한 `출력스트림`을 추출하는 메소드이다.

<br/>

### 3.2.2. 한글 응답
응답정보의 헤더에 `데이터 타입`과 `문자셋`을 설정해서 보내면 클라이언트는 받은 데이터를 타입에 맞게
처리할 수 있고, `인코딩` 작업도 할 수 있다.

문서타입 기본값은 `text/html`이고 문자열 기본값은 `8859_1`이다.  
`text/html`로 설정하면 웹 브라우저는 응답받은 문자열을 모두 HTML 태그로 인식하여 처리한다.  
`8859_1` 문자셋은 한글을 지원하지 않으므로 이 문자셋을 사용하면 한글을 깨져서 나온다.

```java
response.setContentType("text/html;charset=UTF-8");
PrintWriter out = response.getWriter();
...
```

**setContentType()**  
클라이언트에게 보내는 데이터의 문서타입과 문자셋을 응답정보 헤더에 설정하는 메소드이다.  
이 메소드는 getWriter() 메소드보다 먼저 실행되어야 한다.

<br/>

## 3.3. 요청정보 처리 - HttpServletRequest
`HttpServletRequest` 인터페이스는 `ServletRequest` 인터페이스를 상속한다.

- ServletRequest: 일반적인 네트워크 통신에서의 요청 관련 메소드 제공
- HttpServletRequest: HTTP 통신 기반의 요청 관련 메소드를 확장하여 제공

ServletRequest는 HTTP 프로토콜에 기반하는 세션이나 쿠키와 같은 정보를 추출하는 메소드는 지원하지 않는다.  
그런 메소드는 HttpServletRequest 객체에서 확장 지원하고 있다.

### ※ 참고
- **Enumeration과 Iterator**  
  그룹 안에 있는 요소에 접근할 때 `인덱스`나 `키`로 접근하는 것이 아니고, `커서(Cursor)` 개념으로 접근한다.
  주로 Collection 안에 있는 모든 요소를 차례로 접근할 때 사용하면 편리하며, 속도 면에서도 빠르다.

- **자바의 다형성(Polymorphism)**  
  여러 가지 형태를 가질 수 있는 성질이다.  
  한 타입의 참조변수로 여러 타입의 객체를 참조할 수 있도록 한 것이다.
  
  상속 관계가 Object - A - B - C라 가정하자.
  ```java
  Object o = new C();
  ```
  
  이러면 힙 메모리에 만들어진 C 객체의 시작 주솟값을 o가 가진다.  
  C가 상속하고 있는 상위 객체들도 메모리에 만들어지므로 참조변수의 타입을 지정할 수 있다.
  
  다만, C가 상속하고 있지 않은 객체는 객체를 생성할 때 메모리에 만들어지지 않아서 참조변수 타입으로 사용할 수 없다.  
  그리고 Object로 타입을 지정했으므로 하위 객체의 정보를 사용할 수 없다.  
  만약 타입을 B로 지정했으면 B가 상속한 내용에만 접근 가능하다.
  
  상속 관계에 있는 모든 객체들의 내용을 사용하고 싶으면 가장 하위 객체의 타입으로 지정하면 된다.  
  ```java
  C c = new C();
  ```
  
  Object 타입인데 하위 객체의 정보를 사용하고 싶으면 `타입 캐스팅(type casting)`하면 된다.
  ```java
  C c = (C)o;
  ```

- **제네릭(Generics)**  
  자바의 다형성 설명에 있는 코드들만 봐도 타입 캐스팅이라는 번거로운 작업들을 해야하는 걸 알 수 있다.
  JDK 1.5부터 추가된 제네릭은 이러한 타입 캐스팅을 생략할 수 있게 해준다.
  
  어떤 타입의 데이터가 저장될 것인지 미리 설정하면 저장할 때나 추출할 때나 설정된 타입으로 자동으로 처리된다.
  ```java
  ArrayList<String> list = new ArrayList<String>();
  ```
