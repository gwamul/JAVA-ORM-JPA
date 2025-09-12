## 애플리케이션 개발할 때...

보통 객체 지향적으로

### 하지만 데이터베이스 세계에서는 관계형 DB 사용

# 지금 시대 : 객체를 관계형 DB에 보관

그럼, SQL을 사용하여 집어 넣어야 해..

## SQL의 문제

### 무한반복

- CRUD

INSERT INTO...

UPDATE..

- 객체를 CRUD 하기 위해서..

  - INSERT, SELECT, UPDATE .. 를 class 하나하나의 필드를 다 적용해야 한다.

  - 객체에 필드를 새롭게 추가하게 되면.. SQL 쿼리를 모두 고쳐야 해.

- SQL에 의존적인 상황

### 패러다임의 불일치

> 객체 지향 프로그래밍과 관계형 DB는 패러다임이 다르다.

- 객체는 다양한 저장소에 영구 보관할 수 있다.

- 현실적으로 실무에 적용할 수 있는 건 관계형 데이터베이스 (8~90%)

### 이 과정이 지루하다.

객체를 SQL문(insert, select) 로 변환하여,

RDB에 SQL로 전달해야 한다.

- 이 과정을 개발자가 진행해야 함.

- 개발자가 SQL 매퍼의 역할을 하고 있는 상황

## 객체와 관계형 DB 차이

### 상속

- 객체 상속 관계

  - 부모의 추상화된 값

- DB에서는 상속이라는게 있기 어려움,

- 상속관계를 보이기위해 부모 같은 테이블을 만들고, 자식 같은 테이블을 만들어.

- super type, sub type 관계

ITEM 하위 ALBUM 객체를 저장하기 위해서...

1. 객체 분해

2. Insert(album)

3. Insert(item)

ITEM 하위 ALBUM 객체를 조회하기 위해서...

1. 각 테이블에 따른 Join SQL 작성

2. 각각 객체 생성

3. 복잡하다.

- DB에 저장할 객체에는 상속관계에 잘 안쓴다.

## 자바 컬렉션에 저장한다면?

```java
list.add(album);
```

```java
Album album = list.get(albumId);
```

> 뭔가 엄청 간단해 보인다.

## 연관관계

- 객체는 참조를 사용한다. member.getTeam();

- 테이블은 외래키를 사용한다. JOIN ON M.TEAM_ID = T.TEMA_ID (foreign key)

멤버 객체랑 팀 객체를 테이블에 저장하기 위해서,,

class Member, class Team 만들때

- Member 클래스가 Team 의 id 를 가지고 있어야한다.

- Foreign key

## 객체다운 모델링

- 객체는 참조로 연관관계를 맺어야 한다.

- 객체 답게 모델링하기 위해 Team team으로 가지고 있어야 함

- 근데 이렇게 구현하면 insert 하기가 너무 어려워짐 SQL

```SQL
INSERT INTO MEMBER(MEMBER_ID, TEAM_ID, USERNAME) VALUES ...
```

를 수행하고 싶은데 현재 TEAM_ID가 없는 상황.

class 안에는 Team 타입 team 필드만 가지고 있음

그럼 member.getTeam().getId(); 로 TEAM_ID를 받아와야 함.

### 등록은 이렇게 한다 해도, 조회는 ?

- member랑 team을 둘다 조회해야 함

- member, team 조인 하고 가져와야 함

- 번거로움

### 하지만 자바 컬렉션에 보관했을 때는 쉬웠다.

```java
list.add(member);
Member member = list.get(memberId);
Team team = member.getTeam();

```

### SQL은 처음 실행하는게 어디까지냐에 따라 탐색 범위가 달라짐.

- 계층형으로 구현했으면 다음 계층을 믿고 쓸 수 있어야 한다.

- 누군가 member 타입 find() 를 구현해놨다면, 내가 member.getTeam() 을 쓸 수 있나?

- 바로 알 수 없음, 코드를 까봐야 함.

> 결국 모든 객체를 다 까보기 전에는 신뢰할 수 없음

- 메서드 이름에 조회할 필드 정확하게 적어둬야 함.

### 물리적으로 계층분할이 되어있으나, 논리적으로 구분되지 않음

## 비교하기

자바 컬렉션에서 list.get(memberId) 는 결과가 같음( 같은 인스턴스 )

# 객체답게 모델링 할수록 매핑 작업만 늘어난다.

- SQL 변환하는 비용이 너무 크다.

## 객체를 자바 컬렉션에 저장하듯이 DB에 저장하고 싶다.

## JPA : Java Persistence API
