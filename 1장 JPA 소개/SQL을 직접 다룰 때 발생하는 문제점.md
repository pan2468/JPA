### JPA 사용하는 이유
SI회사와 솔루션 회사 같은 경우는 스프링 프레임워크 영속성 프레임워크 MyBatis xml 설정으로 반복적인 CRUD 쿼리를 작성하였다. 하지만 이것은 개발의 생산성과 유지보수가 비효율적이다. 이러한 부분을 개선하기위해서 JPA 등장하였다. 지금 현대 IT서비스 회사에서 대중적으로 인기가 많으편이다. JPA 인터페이스에서 제공해준 CRUD 메소드로 반복적인 불필요한 쿼리를 줄여준다. 개발의 생산성뿐만 아니라 버그가 발생 하였을때 쉽게 찿는점과 유지보수가 안정적이다.

### SQL을 직접 다룰 때 발생하는 문제점
관계형 데이터베이스는 가장 대중적이고 신뢰할 만한 안전한 데이터의 저장소다. 그래서 자바로 개발하는 애플리케이션은 대부분 관계형 데이터베이스를 데이터 저장소로 사용한다.<br>
데이터베이스에 데이터를 관리하려면 SQL을 사용해야 한다. 자바로 작성한 애플리케이션은 JDBC API를 사용해서 SQL을 데이터베이스에 전달한다.

<img src="https://velog.velcdn.com/images%2Fyu-jin-song%2Fpost%2Fc0ea3b9a-51a5-45d3-a167-5a5a2fe3750d%2FJDBC_API%EC%99%80_SQL.png" width="450px" height="300px">

### 반복, 반복 그리고 반복
반복적인 SQL 쿼리를 작성하여 직접 다룰때 문제점 알아보기

#### 회원 객체
~~~

public class Member{

private String memberId;
private String name;

}

~~~

👉 회원 객체를 데이터베이스에 저장하기 위해서 DAO 설정하기

#### 회원용 DAO
~~~

public class MemberDAO{

public Member find(String memberId)

}

~~~

<br>
MemberDAO의 find() 메소드를 완성해서 회원을 조회하는 기능을 개발하기

#### 1. 회원 조회용 SQL 작성한다.

~~~
SELECT MEMBER_ID, NAME FROM MEMBER M WHERE MEMBER_ID = ?
~~~

#### 2. JDBC API를 사용해서 SQL을 실행한다.

~~~
ResultSet rs = stmt.executeQuery(sql);
~~~

#### 3. 조회 결과를 Member 객체로 매핑한다.

~~~
String memberId = rs.getString("MEMBER_ID");
String name = rs.getString("NAME");

Member member = new Member();
member.setMemberId(memberId);
member.setName(name);
~~~

회원 등록 기능 진행

#### 회원 등록 기능 추가

~~~
public class MemberDAO{

public Member find(String memberId);
public void save(Member member);

}
~~~

#### 1. 회원 등록용 SQL 작성한다.

~~~
String sql = "INSERT INTO MEMBER(MEMBER_ID, NAME) VALUES(?,?)";
~~~

#### 2. 회원 객체의 값을 꺼내서 등록 SQL에 전달한다.

~~~

pstmt.setString(1, member.getMemberId());
pstmt.setString(2, member.getName());

~~~

#### 3. JDBC API를 사용해서 SQL를 실행한다.

~~~
pstmt.executeUpdate(sql);
~~~

### SQL에 의존적인 개발

#### 등록 코드 변경하기
회원 테이블에 TEL 컬럼 추가하기 > 회원 객체 tel 필드를 추가

~~~
public class Member{

private String memberId;
private String name;
private String tel; // 

}
~~~

#### 연관된 객체

~~~
class Member{

private String memberId;
private String name;
private String tel;
private Team team; //추가

}

//추가된 팀
class Team{

private String teamName;

}
~~~

코드를 실행해보니 member.getTeam()의 값이 항상 null이다. 회원과 연관된 팀이 없어서 그럴 것으로 생각하고 데이터베이스를 확인해보니 모든 회원이 팀에 소속되어 있다.

#### MemberDAO에 추가된 findWithTeam()

~~~
public class MemberDAO{

public Member find(String memberId);
public Member findWithTeam(String memberId);

}
~~~

### JPA와 문제 해결

조회, 등록, 수정, 삭제 등 JPA에서 인터페이스 통해서 CRUD 메소드를 제공하므로 개발자가 SQL를 일일이 작성할 필요가 없어졌다. 여기서 수정 메소드는 없지만, 조회한 결과값을 수정하여 트랙잭션으로 통해서 변경값 요청하면 UPDATE 실행이 가능하다.





