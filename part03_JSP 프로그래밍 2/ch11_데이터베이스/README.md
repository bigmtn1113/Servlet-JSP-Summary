# 11. 데이터베이스
데이터를 유지하기 위해서 초창기에는 애플리케이션마다 별도로 `파일시스템`에 데이터를 저장하는 방법을 사용했다.  
그러나 이 방법은 여러 애플리케이션에서 데이터에 접근하기가 어렵고 문제가 많았다.  
또한, 중복된 데이터 때문에 보안을 보장하기도 힘들었다.

초창기 파일시스템의 문제였던 데이터의 `종속성`과 `중복성`의 문제를 해결하고, 여러 애플리케이션들이 데이터를
공유할 수 있도록 개선한 시스템이 `DBMS(Data Base Management System)`이다.  
DBMS는 데이터베이스를 `공용`할 수 있도록 관리하는 시스템을 의미한다.

<br/>

## 11.1. JDBC 프로그래밍
JDBC 프로그래밍은 `자바 프로그램` 내에서 `DB 관련 작업`을 하는 프로그램을 의미한다.  
DB 관련 작업을 하려면 DBMS가 필요하며, 자바 프로그램 내에서 DBMS에 접근하여 DB 관련 작업을 하기 위한 API들이 필요하다.

<br/>

### 11.1.1. Oracle 설치
1. **내려받기**
2. **설치**
3. **계정 생성**

<br/>

### 11.1.2. JDBC 개요
JDBC는 Java DataBase Connectivity의 약자로, `자바 언어`로 다양한 종류의 `관계형 데이터베이스`에 접속하고  
`SQL` 문을 수행하여 처리하고자 할 때 사용되는 `표준 SQL 인터페이스 API`이다.

JDBC는 `Javs SE`에서 지원하는 기술로서, 접속하려는 DBMS 서버에 따라서 `JDBC 드라이버`가 필요하다.

**JDBC 연동 과정**  
JDBC 프로그램 - java.sql - JDBC 드라이버 - DB

<br/>

#### [1] JDBC 인터페이스
JDBC 프로그램을 사용하기 위한 API들로서, SE에서 제공하는 `java.sql` 페키지를 의미한다.

java.sql 패키지에는 여러 개의 인터페이스와 클래스 객체들이 있는데 JDBC 프로그래밍을 할 때 주로 인터페이스들을 사용한다.
JDBC 프로그래밍을 할 때는 java.sql의 인터페이스들을 상속하여 메소드의 몸체를 구현한 `클래스 파일`들이 필요하다.  
이 파일들을 `JDBC 드라이버`라고 하며 사용하는 DB 벤더사에서 내려받아 사용하면 된다.

<br/>

#### [2] JDBC 드라이버
JDBC 드라이버 파일은 JDBC 프로그래밍을 하기 전 반드시 준비해서 자바 프로그램에서 사용할 수 있도록 연결해 놓아야 한다.

**JDBC 드라이버 파일 준비**  
- **데이터베이스가 설치된 폴더에 저장된 파일을 사용하는 방법**
- **오라클 웹사이트에 접속하여 내려받는 방법**

**JDBC 드라이버 파일 연결**  
준비된 JDBC 드라이버 파일을 웹 애플리케이션에서 자동으로 인식할 수 있는 곳은 두 군데이다.

- **WAS_HOME/lib**  
  현재 WAS에서 실행되는 `모든 웹 애플리케이션`에서 사용할 수 있다.

- **/WEB-INF/lib**  
  `해당 웹 애플리케이션`에서만 사용할 수 있다.

<br/>

### 11.1.3. 구현
#### [1] JDBC 드라이버 로딩
**static Class<?> forName(String className)**  
준비된 JDBC 드라이버를 사용할 수 있도록 `메모리`에 준비 작업을 해주는 메소드이다.  
인자값으로는 JDBC 드라이버 파일 안에서 드라이버 인터페이스를 상속하고 있는 `클래스이름을 패키지 이름과 함께 명시`한 값이 온다.

```java
Class.forName("oracle.jdbc.driver.OracleDriver");
```

<br/>

#### [2] DBMS 서버 접속
**static Connection getConnection(String url, String user, String password)**  
DB 서버에 접속하여 연결하는 메소드이다.

- **String url**: 접속할 서버의 URL을 정확하게 지정한다.
- **String user**: DB 서버에 로그인할 계정을 지정한다.
- **String password**: DB 서버에 로그인할 비밀번호를 지정한다.

```java
Connection conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe", "scott", "tiger");
```
오라클 프로토콜 - jdbc:oracle:thin:@  
서버주소 - localhost  
서버포트 - 1521  
DB 이름 - xe

**Connection**  
DriverManager.getConnection()은 실제 `자바 프로그램`과 `데이터베이스`를 네트워크상에서 연결해주는 메소드이다.  
연결에 성공하면 DB와 연결된 상태를 Connection 객체로 표현하여 반환한다.  
Connection은 `네트워크상의 연결` 자체를 의미한다.

자바 프로그램과 DB 사이에 연결된 `길(road)`이라고 생각하자.

<br/>

#### [3] Statement 객체
자바 프로그램은 DB 쪽으로 `SQL 문`을 전송하고, DB는 `처리된 결과`를 자바 프로그램 쪽으로 전달해야 하는데  
바로 이 역할을 하는 객체가 Statement이다.

길을 통해 SQL 문과 처리 결과를 서로 전달해주는 `트럭`이라 생각하자.

**Statement createStatement()**  
Statement 객체를 생성하려면 Connection 객체에서 제공하는 createConnection() 메소드를 사용한다.  

```java
Statement stmt = conn.createStatement();
```

**SQL 문 실행**  
SQL 문을 실행하기 위해 사용되는 메소드는 Statement 객체에서 제공하는 executeQuery() 또는 executeUpdate()를 사용한다.
- **ResultSet executeQuery(String sql)**
- **int executeUpdate(String sql)**

**ResultSet**  
ResultSet은 executeQuery() 메소드에서 실행된 `select 문의 결괏값`을 갖고 있는 객체이다.

```java
ResultSet rs = stmt.executeQuery("select * from test");
```

ID | PWD
|---|---|
| |
aa | 11
bb | 22
| |

ResultSet 객체의 특징은 내부적으로 위치를 나타내는 `커서(cursor)`라는 개념이 있다.  
또한 ResultSet 객체는 select한 결과 레코드가 있든 없든 항상 `시작 빈 행`과 `끝 빈 행`을 가진다.

- **void afterLast()**
- **void beforeFirst()**

- **boolean next()**

- **String getString(int columnIndex)**
- **String getString(String columnLabel)**

afterLast()는 `커서를 끝 빈 행`으로 위치시키며, beforeFirst()는 `커서를 시작 빈 행`으로 위치시키는 메소드이다.

next()는 커서 다음에 레코드이 있는지를 판단하여 없으면 false를 반환하고, 있으면 true를 반환한 다음 커서를 다음 레코드로 이동시키는 메소드이다.

getter 메소드듣은 모두 `칼럼의 값을 추출`하는 메소드이다. 사용할 땐 데이터 타입에 따라 getter 메소드를 선택하면 된다.
int 타입으로 추출하고자 하면 getInt() 메소드를 사용하면 된다. getter 메소드의 인자값으로는 칼럼의 이름을 지정할 수도 있고,
칼럼의 인덱스값을 지정할 수도 있다.

<br/>

#### [4] PreparedStatement 객체
Statement 객체와 같은 기능을 수행하는 객체이나 생성 시 실행할 SQL 문을 `? 기호`와 함께 작성할 수 있다는 것이 특징이다.  
동적으로 값을 할당할 때 ? 기호를 사용하면 `가독성`과 `유지 보수성`이 좋다.

```java
/* Statement 사용 */
Statement stmt = conn.createStatement();
stmt.executeUpdate("insert into test values ('cc', '33')");

/* PreparedStatement 사용 */
PreparedStatement pstmt = conn.prepareStatement("insert into test values (?, ?)");
pstmt.setString(1, "cc");
pstmt.setString(2, "33");
pstmt.executeUpdate();
```

#### [5] 자원 해제
**JDBC 프로그래밍 객체 생성 순서**  
Connection(DB 연결 객체) -> Statement 또는 PreparedStatement(SQL 문 실행 객체) -> ResultSet(select 문 결과를 가지는 객체)

**JDBD 프로그래밍 자원 해제 순서**  
rs.close() -> stmt.close() 또는 pstmt.close() -> conn.close()

<br/>

## 11.2. DataSource
### 11.2.1. 개요
#### [1] Connection 관리
웹 프로그램은 동시에 여러 사용자에 의해 요청이 일어날 수 있는 특성이 있으므로 자원 배분과 응답 속도에 대하여  
`효율적`으로 구현해야 한다.

그런데 웹 클라이언트로부터 요청이 있을 때마다 Connection을 얻기 위한 작업을 하면 문제점이 있다.
- **DB 프로그램에서 트랜잭션 처리와 Connection 관리는 시스템의 성능과 안전성에 큰 영향을 미친다.**
- **Connection 과정은 일정 시간이 필요한 부담되는 작업이다.**
- **불필요한 연결에 의한 서버 자원의 낭비를 발생한다.**

<br/>

#### [2] Connection Pool
Connection 객체를 프로그램이 실행될 때마다 생성하는 것이 아니라, 웹 애플리케이션이 서비스되기 전에 웹서버에서  
`미리 생성하여 준비`한 다음, 필요할 때 준비된 Connection을 가져와 사용함으로써 JDBC 프로그래밍의 문제점을 개선한 기술이다.

Connection 객체는 서버가 시작할 때 서버에서 여러 개를 준비할 수 있다.  
이렇게 준비된 Connection 객체들을 갖고 있는 리소스를 `Connection Pool`이라 한다.

<br/>

#### [3] DataSource
Connection Pool에는 여러 개의 Connection 객체가 생성되어 운용되는데, 각각을 직접 웹 애플리케이션에서 이용하면  
`체계적인 관리`가 힘들게 되므로 DataSource라는 개념을 도입하여 사용하고 있다.

DataSource라는 객체는 `Connection Pool을 관리`하는 목적으로 사용되는 객체로 웹 애플리케이션에서는 이 DataSource 객체를  
통해 Connection을 얻어오고 반납하는 등의 작업을 수행할 수 있다.  
이 DataSource 객체는 `JNDI Server`에 의해 웹 애플리케이션에 전달하는 방식을 따르고 있다.

DataSource를 이용하지 않는 기존의 방식(DriverManager를 이용한 방식)은 내부적으로 `static한 필드`를 사용하기 때문에  
`스레드 환경`에서는 바람직하지 않다.

**DataSource 이용방법**  
1. JNDI Server에서 `lookup()` 메소드를 통해 DataSource 객체를 획득한다.
2. DataSource 객체의 getConnection() 메소드를 통해 Connection Pool에서 `Free 상태의 Connection`을 획득한다.
3. Connection 객체를 통한 DBMS 작업을 수행한다.
4. 모든 작업이 끝나면 DataSource 객체를 통해 Connection Pool에 Connection을 반납한다.

<br/>

#### [4] JNDI(Java Naming and Directory Interface)
`API`와 `SPI`로 이루어져 있으며, API는 애플리케이션에서 네이밍 혹은 디렉터리 서비스에 접근하는 데 사용하고  
SPI는 새로운 서비스를 개발할 때 사용된다.

**Naming & Directory 서비스**  
`분산환경`은 네트워크로 연결된 수많은 컴퓨터에 `분산된 여러 자원이 하나의 시스템`으로 구성되며, 네트워크상에서  
어떤 하나의 애플리케이션이 필요한 자원을 찾고 획득하는 일은 가장 중요한 일 중 하나이다.

이렇게 분산 컴퓨팅과 엔터프라이즈 컴퓨팅 환경에서의 자원 획득과 이용의 효율성을 위해 제공하는 서비스가 `Naming & Directory`이다.

Naming & Directory 서비스는 실제 어떤 자원을 갖고 서비스한다는 의미가 아니라, 어떤 서버나 애플리케이션에서 분산환경에  
서비스하고자 하는 자원을 이 Naming & Directory 서버에 `이름값과 실제 자원을 연결하여 등록`하면,  
해당 자원을 이용하고자 하는 다른 애플리케이션에서 Naming & Directory 서버에 접근하여 이름값만을 갖고 자원을 연결하여  
이용할 수 있게 하는 개념이다.

ex) DNS 서버는 실제 인터넷 서비스를 수행해주는 곳이 아니라, 단지 도메인과 IP 주소만을 연결해주는 기능을 한다.

<br/>

### 11.2.2. 구현
#### [1] server.xml 설정
Connection Pool은 서버에서 관리하는 자원이다. 그래서 서버 환경설정 파일인 `server.xml`에서 설정한다.  
설정한 내용에 따라 서버가 시작하면서 리소스 준비 작업이 이루어진다.

```xml
<GlobalNamingResources>
  <Resource driverClassName="oracle.jdbc.driver.OracleDriver"
            url="jdbc:oracle:thin:@127.0.0.1:1521:xe"
            username="scott"
            password="tiger"
            name="jdbc/myoracle"
            type="javax.sql.DataSource"
            maxActive="4"
            maxIdle="2"
            maxWait="5000" />
</GlobalNamingResources>
```

- **driverClassName**  
  DB 작업을 위해 로딩할 JDBC 드라이버 파일에 드라이버 인터페이스를 상속하는 파일명을 전체 이름으로 지정한다.  
  Class.forName() 메소드의 인자값이다.

- **url**  
  접속할 DB 서버의 URL을 지정한다.

- **username**  
  DB 서버에 로그인할 계정을 지정한다.

- **password**  
  DB 서버에 로그인할 계정의 비밀번호를 지정한다.

- **name**  
  현재 리소스를 등록할 이름을 지정한다.

- **type**  
  리소스의 타입을 지정한다.  
  Connection Pool을 사용할 수 있도록 해주는 객체의 타입은 javax.sql.DataSource이다.

- **maxActive**  
  생성할 Connection 수를 지정한다.

- **maxIdle**  
  일반적으로 활용할 Connection 수를 지정한다.

- **maxWait**  
  Connection의 사용 요청이 있을 때 대기 시간을 지정한다.  
  5000은 5초를 의미하며, 5초가 지난 후에도 Connection을 얻지 못하면 Exception이 발생한다.

서버에서의 설정은 server.xml에 Connection을 만들기 위해 정보를 설정한 후 `WAS_HOME/conf/context.xml`에 Connection Pool의 이름을 등록함으로써 완료된다.
  
```xml
  ...
  <ResourceLink global="jdbc/myoracle" name="jdbc/myoracle" type="javax.sql.DataSource" />
</Context>
```
서버에 등록된 리소스를 웹 애플리케이션에서 찾아서 사용할 수 있도록 글로벌한 이름을 지정한다.

#### [2] web.xml 설정
서버에서 관리하는 리소스를 웹 애플리케이션에서 사용하기 위해 `web.xml`에 사용할 리소스에 대한 정보를 지정해야 한다.

```xml
...
  <resource-ref>
    <description>Oracle DataSource example</description>
    <res-ref-name>jdbc/myoracle</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
  </resource-ref>
...
```

- **`<description>`**  
  리소스에 대한 설명을 지정한다.

- **`<res-ref-name>`**  
  사용하고자 하는 리소스의 이름을 지정한다.

- **`<res-type>`**  
  사용하고자 하는 리소스의 타입을 지정한다.

- **`<res-auth>`**  
  리소스에 대한 권한이 누구인지 지정한다.

```java
InitialContext ic = new InitialContext();
DataSource ds = (DataSource) ic.lookup("java:comp/env/jdbc/myoracle");
Connection conn = ds.getConnection();
```

- **new InitialContext()**  
Connection Pool에 접근하려면 `JNDI` 서비스를 사용해야 하므로 JNDI 서버 역할을 하는 객체를 생성한다.

- **ic.lookup()**  
리소스를 찾은 후 리소스를 사용할 수 있도록 객체를 반환하는 메소드이다.

`jdbc/myoracle`는 리소스 이름이고, `java:comp/env`는 톰캣에서 리소스를 관리하는 가상 디렉터리의 경로이다.  
톰캣을 사용할 때는 리소스 이름 앞에 디렉터리 경로를 지정해 주어야 한다.
