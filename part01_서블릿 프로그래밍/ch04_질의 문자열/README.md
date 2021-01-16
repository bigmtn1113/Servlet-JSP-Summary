# 4. 질의 문자열
## 4.1. 질의 문자열의 개요
### 4.1.1. 질의 문자열이란?
웹 클라이언트에서 웹서버에 정보를 요청할 때 정해진 방식으로 데이터를 전달할 수 있는데  
이때 사용하는 문자열을 `질의 문자열`이라고 한다.

<br/>

### 4.1.2. 질의 문자열 전송 규칙
질의 문자열 작업을 하려면 사용자가 데이터를 입력할 수 있는 화면을 만들어야 한다.  
또한, 입력한 자료를 서버로 전달해야 하며, 서버에서는 전달받은 문자열들을 추출하여 사용자가 요청한 서비스를 처리해야 한다.

클라이언트가 입력한 데이터는 그대로 서버로 전달되는 것이 아니라, 정해진 규칙으로 `인코딩(encoding)`되어 전달된다.
- **`name=value` 형식으로 전달되며, 여러 개의 `name=value` 쌍이 있을 때는 &를 구분자로 사용한다.**  
  
  ex) id=guest&name=Amy
  
  `name`은 개발자가 구현할 때 정하고, `value`는 사용자가 입력한 값이다.  
  `name`을 지정할 때는 충분히 데이터의 의미를 나타내는 이름으로 지정하는 것이 좋다.
  
- **영문자, 숫자, 일부 특수문자는 그대로 전달되고, 나머지 문자는 % 기호와 함께 16진수로 바뀌어 전달된다.**  
  
  ex) id=guest&name=%C%AB%B1...
  
  영문자, 숫자, `ASCII` 문자코드에 해당하는 문자는 그대로 서버로 전달되나, 나머지 문자는 `16진수`로 변환되어 전달된다.  
  따라서 서버 쪽에서는 적절한 `복원` 처리를 해줘야 문자열이 깨지지 않는다.
  
- **공백 문자는 + 기호로 변경되어 전달된다.**  
  
  ex) id=guest&name=John+Smith
  
  질의 문자열은 URL에 추가되어 전달되는 경우가 있는데 이때, URL은 `공백`을 포함할 수 없기 때문이다.  
  만약 공백이 있다면 공백이 나타나기 바로 전까지를 하나의 URL로 인식한다.

<br/>

## 4.2. HTML 입력양식
HTML에서 지원하는 `<form>`은 웹상에서 입력양식을 제공할 때 사용하는 태그로서, 다양한 GUI 컴포넌트로 구성된 입력양식을 출력한다.

### 4.2.1. `<form>` 태그
```HTML
<form action="서버프로그램 경로" method="요청방식">
  ```
  
  - **action**  
    `<form>`과 `</form>` 태그 사이에 입력한 질의 문자열들을 전달받아 처리할 서버 프로그램이 있는 서버의 경로을 지정한다.  
    이때, 상대 경로나 절대 경로를 사용해 지정한다.
    
  - **method**  
    클라이언트가 서버 프로그램을 어떠한 방식으로 요청할지를 지정한다.  
    다른 값도 더 있지만, 일반적으로 `GET`과 `POST`를 사용한다.

<br/>

### 4.2.2. 입력 태그들
- **텍스트 입력상자(한 줄)**  
  ```HTML
  <input text="text" name="변수이름" maxlength="숫자" size="숫자" value="문자열"/>
  ```

- **체크박스**  
  ```HTML
  <input type="checkbox" name="변수이름" value="문자열"/>
  ```
  
- **라디오 버튼**  
  ```HTML
  <input type="radio" name="변수이름" value="문자열"/>
  ```
  
- **펼침목록**  
  ```HTML
  <select name="변수이름">
    <option value="문자열">항목이름</option>
    <option value="문자열">항목이름</option>
  </select>
  ```
  
- **텍스트 입력상자(여러 줄)**  
  ```HTML
  <textarea cols="숫자" rows="숫자" name="변수이름"></textarea>
  ```
  
- **전송 버튼**  
  ```HTML
  <input type="submit" value="문자열"/>
  ```
  
- **초기화 버튼**  
  ```HTML
  <input type="text" value="문자열"/>
  ```

<br/>

### 4.2.3. HTML 태그 사용 시 주의사항
- HTML 태그의 속성값은 대소문자를 구분한다.
- HTML 태그의 속성값을 지정할 때는 큰따옴표(" ")로 감싸줘야 한다.
- 시작 태그와 끝 태그의 짝을 맞춰라.

<br/>

## 4.3. 요청방식에 따른 처리
### 4.3.1. GET 방식으로 처리
**특징**
- 전달되는 질의 문자열이 요청정보 헤더의 `URI`에 추가되어 전달된다.  
  ex) GET /edu/queryTest?name=value&.. HTTP/1.1
  
- 전달되는 질의 문자열의 내용이 외부에 `노출`된다.

- 전달되는 질의 문자열의 길이는 `제한적`이다.  
  서버에서 인식할 수 있는 URI 길이가 255바이트 미만
  
- 전달되는 질의 문자열의 `인코딩/디코딩` 작업이 필요없어서 처리속도가 빠르다.

- 전달되는 질의 문자열을 직접 URI에 추가할 수 있다.
  ```java
  <a href="BoardList?page=1">게시글 보기</a>
  ```

이러한 특징은 전달되는 질의 문자열이 요청정보 헤더의 URI에 추가돼서 나타나는 것이다.

**GET 방식으로 요청되는 상황**  
- `<a>` 태그를 클릭하여 요청하는 경우
- 브라우저 주소 줄에 URL을 입력하여 요청하는 경우
- `<form>` 태그에서 method 속성을 생략하여 요청하는 경우

<br/>

### 4.3.2. POST 방식으로 처리
**특징**
- 전달되는 질의 문자열이 요청정보의 `몸체`에 포함되어 전달된다.
- 전달되는 질의 문자열이 외부에 노출되지 않는다.
- 전달되는 질의 문자열의 길이에 제한이 없다.

**POST 방식으로 요청되는 상황**
- `<form>` 태그에서 method 속성을 POST라 지정하는 경우

<br/>

## 4.4. 서블릿 작성
### 4.4.1. 메소드 구현
서블릿을 구현할 때 service() 메소드를 재정의했다면 재정의한 메소드가 실행되고  
재정의하지 않았다면 HttpServlet의 service() 메소드가 실행된다.

이때, service()는 요청방식에 따라 메소드를 호출한다.  
GET 방식 요청은 `doGet()`, POST 방식 요청은 `doPost()`를 호출한다.

<br/>

### 4.4.2. 서블릿 연결
QueryTestServlet.java
```java
@webServlet("/queryTest")
public class QueryTestServlet extends HttpServlet {
  @Override
  public void doGet( ... ) throws ... { ... }
  
  @Override
  public void doPost( ... ) throws ... { ... }
}
```

member.html
```HTML
<form action="queryTest">
  ...
```

QueryTestServlet이 속한 웹 애플리케이션의 경로는 /edu이고, @WebServlet 값을 "/queryTest"로 지정했다면  
실제 지정되는 `URI`는 /edu/queryTest이다.

member.html 파일이 속한 웹 애플리케이션의 경로는 /edu이고, 파일은 /(루트) 디렉터리에 있다고 가정하면  
실제 요청되는 `URI`는 /edu/queryTest이다.

따라서 member.html에서 전송 버튼을 누르면 QueryTestServlet이 실행된다.
이때, GET 방식으로 요청하면 `doGet()`이, POST 방식으로 요청하면 `doPost()`가 호출된다.

<br/>

### 4.4.3. 질의 문자열 추출
질의 문자열은 어떠한 방식으로 요청하든지 HTTP의 요청정보에 포함되어 전달된다.  
HTTP의 요청정보에 대한 값을 추출하는 기능을 갖고 있는 객체는 `HttpServletRequest`이다.  
이 객체의 메소드를 사용해서 질의 문자열을 추출할 수 있다.

- **String getParameter(String name)**  
  질의 문자열로 넘어온 값을 `하나씩` 추출할 때 사용한다.
  
- **String[] getParameterValues(String name)**  
  같은 이름으로 `여러 개`의 변수가 전달되었을 때 한번에 `모든 값`을 추출한다.
  
- **String getQueryString()**  
  클라이언트가 전달한 질의 문자열 `전체`를 추출한다.  
  HTTP 요청정보의 URI 정보에서 ? 다음에 나오는 문자열들을 추출하므로 `GET` 방식에서만 사용할 수 있다.
  
- **ServletInputStream getInputStream() throws IOException**  
  HTTP의 요청정보 몸체와 서블릿 프로그램 간에 연결된 `입력스트림`을 생성하여 반환한다.  
  `POST` 방식의 질의 문자열 전체를 한번에 추출할 때 사용한다.
  
  ```java
  ServletInputStream input = request.getInputStream();
  int len = request.getContentLength();
  byte[] buf = new byte[len];
  input.readLine(buf, 0, len);
  String s = new String(buf);
  ```
