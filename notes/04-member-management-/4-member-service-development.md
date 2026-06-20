# 회원 서비스 만들기

`MemberService`는 회원과 관련된 핵심 비즈니스 로직을 처리하는 클래스이다.

Repository는 데이터를 저장하고 조회하는 역할을 하고,
Service는 회원가입 과정에서 필요한 규칙을 처리한다.

```text
Controller
→ 요청을 받음

Service
→ 핵심 비즈니스 로직 처리

Repository
→ 데이터 저장 / 조회
```

Repository는 데이터를 저장하고 조회하는 역할이다.
Service는 회원 기능의 흐름과 규칙을 처리하는 역할이다.

findAll(), findById() 같은 조회 기능은 Repository에 이미 있지만,
Controller가 Repository를 직접 사용하지 않도록 Service에서 한 번 감싸서 제공한다.

지금은 단순히 Repository 메서드를 호출하는 것처럼 보이지만,
나중에 조회 조건, 권한 검사, 정렬, 예외 처리 같은 비즈니스 규칙이 추가되면 Service에서 처리할 수 있다.

즉, Service는 Repository를 대신 반복하는 코드가 아니라,
회원 기능에 대한 모든 접근이 거쳐 가는 계층이다

## MemberService의 역할

```

`MemberService`는 회원가입, 전체 회원 조회, 단일 회원 조회 기능을 제공한다.

join()
→ 회원가입

validateDuplicateMember()
→ 중복 회원 검증

findMembers()
→ 전체 회원 조회

findOne()
→ 회원 id로 단일 회원 조회
```

![회원 기능 개발 코드 설명](../../images/4-member-service-development-(1).jpg)
![회원 기능 개발 코드 설명](../../images/4-member-service-development-(2).jpg)
