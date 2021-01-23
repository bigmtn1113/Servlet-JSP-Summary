# 15. 웹 애플리케이션 디자인 패턴
`디자인 패턴`은 프로그램을 개발할 때 사용하는 일종의 `뼈대(프레임)` 또는 `틀`이라고 생각하면 된다.  
디자인 패턴에는 구현 시 페이지를 어떻게 나눠야 하는지, 각 페이지의 기능은 무엇인지,  
페이지와 페이지 간의 관계까지 정의되어 있어서 개발자는 고민없이 정해진 디자인 패턴대로 구현하면 된다.

## 15.1. MVC 디자인 패턴
### 15.1.1. MVC 디자인 패턴 기본 예제
- **Member.java**: JSP Bean 객체
- **MemberService.java**: 회원 서비스 처리 객체
- **MemberDAO.java**: 회원 데이터베이스 처리 객체
- **memberInput.jsp**: 회원가입 입력 화면 페이지
- **memberOutput.jsp**: 회원가입 처리 결과 페이지
- **memberProc.jsp**: 회원가입 처리 페이지
- **memberTable.jsp**: member 테이블 생성 페이지

**실행 흐름**  
memberTable.jsp를 실행시켜 member 테이블을 데이터베이스에 생성한 다음의 흐름이다.

memberInput.jsp -> memberProc.jsp -> MemberService.java -> MemberDAO.java -> DB  
-> MemberDAO.java -> MemberService.java -> memberProc.java -> memberOutput.jsp
