# 비즈니스 요구사항 정리

## 핵심 정리

이번 예제에서는 간단한 회원 기능을 만든다.

필요한 데이터는 회원 ID와 이름이고, 기능은 회원 등록과 회원 조회이다.

아직 실제 데이터 저장소는 정해지지 않았기 때문에, 처음부터 DB에 강하게 묶어서 만들지 않고 기본 구조를 먼저 잡는다.

```text
데이터: 회원 ID, 이름
기능: 회원 등록, 회원 조회
저장소: 아직 결정되지 않음
```

---

## 일반적인 웹 애플리케이션 계층 구조

웹 애플리케이션은 보통 역할을 나누어서 만든다.

```text
Controller
→ Service
→ Repository
→ DB
```

각 계층은 맡는 일이 다르다.

| 계층         | 역할                  |
| ---------- | ------------------- |
| Controller | 웹 요청을 받는다           |
| Service    | 핵심 비즈니스 로직을 처리한다    |
| Repository | 데이터를 저장하거나 조회한다     |
| DB         | 실제 데이터가 저장되는 곳이다    |
| Domain     | 비즈니스에서 사용하는 핵심 객체이다 |

---

## 계층별 역할

## Controller

Controller는 웹 MVC에서 요청을 받는 역할을 한다.

브라우저나 클라이언트에서 요청이 들어오면 Controller가 먼저 받는다.

```text
브라우저 요청
→ Controller가 받음
→ 필요한 처리를 Service에 맡김
```

Controller는 핵심 비즈니스 로직을 직접 처리하기보다는, 요청을 받아서 적절한 Service로 연결해주는 역할을 한다.

---

## Service

Service는 핵심 비즈니스 로직을 처리하는 계층이다.

회원 등록 기능이라면 단순히 데이터를 저장하는 것만이 아니라, 회원가입 규칙을 처리하는 곳이다.

예를 들어 같은 이름의 회원이 이미 있는지 확인하거나, 회원 등록 과정에서 필요한 판단을 Service에서 처리한다.

```text
Controller
→ Service
→ 회원 등록 규칙 처리
```

즉, Service는 애플리케이션의 핵심 규칙이 들어가는 곳이다.

---

## Repository

Repository는 데이터 저장소에 접근하는 계층이다.

Service가 회원을 저장하거나 조회해야 할 때 Repository를 사용한다.

```text
Service
→ Repository
→ 데이터 저장 또는 조회
```

Repository는 DB에 직접 접근하거나, 아직 DB가 정해지지 않은 경우에는 메모리에 임시로 데이터를 저장할 수도 있다.

즉, Repository는 데이터를 관리하는 역할을 한다.

---

## Domain

Domain은 비즈니스에서 사용하는 핵심 객체이다.

이번 예제에서는 회원이 핵심 대상이므로 `Member` 같은 객체가 Domain에 해당한다.

회원 객체에는 회원 ID와 이름 같은 데이터가 들어간다.

```text
Member
→ id
→ name
```

Domain은 Controller, Service, Repository에서 함께 사용되는 중심 데이터이다.

---

## 회원 기능 흐름

회원 등록 흐름은 다음과 같다.

```text
브라우저에서 회원 등록 요청
→ Controller가 요청을 받음
→ Service가 회원가입 규칙을 처리함
→ Repository가 회원 데이터를 저장함
→ 저장소에 회원 정보가 보관됨
```

회원 조회 흐름은 다음과 같다.

```text
브라우저에서 회원 조회 요청
→ Controller가 요청을 받음
→ Service가 조회 기능을 처리함
→ Repository가 저장된 회원 데이터를 찾음
→ 조회 결과를 반환함
```

---

## 정리

이번 구조의 핵심은 역할 분리이다.

```text
Controller = 요청 받기
Service = 핵심 로직 처리
Repository = 데이터 저장과 조회
Domain = 비즈니스 핵심 객체
DB = 실제 저장소
```

## 앵커

웹 애플리케이션은 요청을 받는 부분, 핵심 로직을 처리하는 부분, 데이터를 저장하는 부분을 나누어서 만든다.

```text
Controller → Service → Repository → DB
```

# 클래스 의존관계

## 핵심 정리

아직 데이터 저장소가 정해지지 않았기 때문에, `MemberService`가 특정 저장소 구현체에 직접 의존하지 않도록 설계한다.

현재는 개발을 빠르게 진행하기 위해 메모리 기반 저장소를 사용하지만, 나중에 DB가 정해지면 저장소 구현체를 바꿀 수 있어야 한다.

그래서 `MemberRepository`를 인터페이스로 만들고, 실제 구현체로 `MemoryMemberRepository`를 사용한다.

```text
MemberService
→ MemberRepository 인터페이스
→ MemoryMemberRepository 구현체
```

---

## 클래스 관계

```text
MemberService
→ MemberRepository

MemoryMemberRepository
→ MemberRepository를 구현
```

`MemberService`는 `MemoryMemberRepository`를 직접 바라보지 않는다.
대신 `MemberRepository` 인터페이스를 바라본다.

즉, 서비스는 “회원 저장소 기능”만 알고 있으면 된다.
그 기능이 메모리로 구현되었는지, DB로 구현되었는지는 직접 알 필요가 없다.

---

## 왜 인터페이스를 사용하는가

아직 저장소가 확정되지 않았기 때문이다.

저장소는 나중에 바뀔 수 있다.

```text
초기 개발 단계
→ MemoryMemberRepository 사용

나중에 DB 연결
→ JdbcMemberRepository 또는 JpaMemberRepository로 변경 가능
```

만약 `MemberService`가 처음부터 `MemoryMemberRepository`에 직접 의존하면,
저장소를 바꿀 때 Service 코드도 함께 수정해야 한다.

하지만 인터페이스를 사이에 두면,
Service는 그대로 두고 Repository 구현체만 바꿀 수 있다.

---

## 역할 정리

| 클래스                    | 역할                          |
| ---------------------- | --------------------------- |
| MemberService          | 회원 가입, 회원 조회 같은 핵심 로직 처리    |
| MemberRepository       | 회원 저장소가 가져야 할 기능을 정해둔 인터페이스 |
| MemoryMemberRepository | 메모리에 회원 데이터를 저장하는 구현체       |

---

## 흐름으로 보기

회원을 저장할 때 흐름은 다음과 같다.

```text
MemberService
→ MemberRepository에 저장 요청
→ MemoryMemberRepository가 실제로 메모리에 저장
```

회원을 조회할 때도 마찬가지다.

```text
MemberService
→ MemberRepository에 조회 요청
→ MemoryMemberRepository가 메모리에서 회원을 찾음
```

Service 입장에서는 Repository가 어떤 방식으로 저장하는지 중요하지 않다.
중요한 것은 `save`, `findById`, `findByName`, `findAll` 같은 저장소 기능을 사용할 수 있다는 점이다.

---

## 정리

이 구조의 핵심은 **구현체에 직접 의존하지 않고 인터페이스에 의존하는 것**이다.

```text
MemberService = MemberRepository 인터페이스에 의존
MemoryMemberRepository = MemberRepository를 구현
```

## 앵커

`MemberService`는 실제 저장소 구현체가 아니라, 저장소 역할을 정해둔 `MemberRepository` 인터페이스에 의존한다.
