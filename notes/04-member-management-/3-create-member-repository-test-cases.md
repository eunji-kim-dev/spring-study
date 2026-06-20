# 회원 리포지토리 테스트 정리

## 핵심 정리

회원 리포지토리 테스트는 `MemoryMemberRepository`가 의도한 대로 동작하는지 확인하는 코드이다.

직접 `main()` 메서드를 실행하거나 화면에서 하나씩 확인하지 않고,
테스트 코드를 통해 저장, 조회, 전체 조회 기능이 제대로 동작하는지 자동으로 검증한다.

```text
테스트 코드 실행
→ 기능 실행
→ 실제 결과와 기대 결과 비교
→ 같으면 테스트 성공
→ 다르면 테스트 실패
```

---

## 테스트하는 기능

이번 테스트에서 확인하는 기능은 3가지이다.

```text
save()
→ 회원을 저장한 뒤, id로 다시 조회되는지 확인

findByName()
→ 이름으로 회원을 찾을 수 있는지 확인

findAll()
→ 저장된 전체 회원 목록을 조회할 수 있는지 확인
```

---

## JUnit

JUnit은 자바에서 테스트 코드를 실행하기 위한 테스트 프레임워크이다.

`@Test`가 붙은 메서드는 JUnit이 테스트 메서드로 인식하고 실행한다.

```text
@Test
→ 이 메서드는 테스트로 실행하라는 표시
```

테스트 메서드는 기능을 실행하고, 결과가 기대한 값과 같은지 확인하는 역할을 한다.

---

## AssertJ와 assertThat

AssertJ는 테스트 결과를 검증할 때 사용하는 라이브러리이다.

`assertThat()`은 실제 결과가 기대한 결과와 같은지 확인한다.

```text
assertThat(result).isEqualTo(member)

result
→ 실제 실행 결과

member
→ 기대한 결과
```

결과가 같으면 테스트가 성공하고, 다르면 테스트가 실패한다.

`System.out.println()`은 결과를 출력만 하기 때문에 사람이 직접 확인해야 한다.
반면 `assertThat()`은 테스트 코드가 직접 성공과 실패를 판단한다.

```text
System.out.println()
→ 사람이 눈으로 확인

assertThat()
→ 코드가 자동으로 검증
```

---

## save 테스트

`save()` 테스트는 회원을 저장한 뒤, 저장된 회원이 id로 다시 조회되는지 확인하는 테스트이다.

```text
회원 객체 생성
→ 이름 설정
→ repository.save(member)
→ 저장되면서 id 부여
→ findById(member.getId())로 다시 조회
→ 조회 결과가 처음 저장한 회원과 같은지 검증
```

이 테스트의 핵심은 다음이다.

```text
저장한 회원을 id로 다시 찾았을 때,
같은 회원 객체가 나와야 한다.
```

---

## findByName 테스트

`findByName()` 테스트는 이름으로 회원을 조회했을 때, 해당 이름을 가진 회원이 정확히 반환되는지 확인하는 테스트이다.

```text
member1 생성
→ name = "spring1"
→ 저장

member2 생성
→ name = "spring2"
→ 저장

findByName("spring2")
→ 이름이 spring2인 회원 조회
→ 결과는 member2여야 함
```

검색한 이름과 기대하는 회원 객체가 맞아야 한다.

```text
spring1 → member1
spring2 → member2
```

예를 들어 `findByName("spring2")`로 조회했다면 결과는 `member2`와 비교해야 한다.

```text
findByName("spring2")
→ member2가 나와야 함
```

---

## findAll 테스트

`findAll()` 테스트는 저장소에 저장된 모든 회원을 조회할 수 있는지 확인하는 테스트이다.

```text
회원 2명 저장
→ findAll() 실행
→ 전체 회원 목록 조회
→ 목록 크기가 2인지 검증
```

이 테스트의 관심사는 회원 이름이 아니라, 저장된 회원 수이다.

```text
회원 2명을 저장했으면
→ findAll() 결과의 size는 2여야 한다.
```

---

## @AfterEach와 clearStore

`@AfterEach`는 각 테스트 메서드가 끝난 뒤 자동으로 실행되는 메서드이다.

```text
save 테스트 실행
→ afterEach 실행
→ 저장소 비움

findByName 테스트 실행
→ afterEach 실행
→ 저장소 비움

findAll 테스트 실행
→ afterEach 실행
→ 저장소 비움
```

`clearStore()`를 사용하는 이유는 테스트끼리 서로 영향을 주지 않게 하기 위해서이다.

`MemoryMemberRepository`는 메모리 저장소인 `store`에 데이터를 저장한다.
이전 테스트에서 저장한 회원이 남아 있으면 다음 테스트 결과가 달라질 수 있다.

```text
이전 테스트 데이터가 남아 있음
→ 다음 테스트에서 예상보다 회원 수가 많아짐
→ 테스트 결과가 꼬임
```

그래서 각 테스트가 끝날 때마다 저장소를 비워서, 다음 테스트가 깨끗한 상태에서 시작되도록 만든다.

```text
테스트 끝남
→ clearStore()
→ store 비움
→ 다음 테스트는 빈 저장소에서 시작
```

## 테스트 실행 순서

테스트는 작성한 순서대로 실행된다고 기대하면 안 된다.

```text
save()
findByName()
findAll()
```

이 순서대로 항상 실행된다고 믿으면 안 된다.

테스트는 어떤 순서로 실행되어도 각각 독립적으로 성공해야 한다.

```text
save가 먼저 실행되어도 성공
findByName이 먼저 실행되어도 성공
findAll이 먼저 실행되어도 성공
```

그래서 테스트마다 저장소를 초기화해야 한다.

---

## 인터페이스 타입과 구현체 타입

테스트에서 저장소 객체를 만들 때 다음 두 방식이 가능하다.

```text
MemberRepository repository = new MemoryMemberRepository();

MemoryMemberRepository repository = new MemoryMemberRepository();
```

인터페이스 타입으로 선언하면, 코드는 구체적인 구현체보다 저장소 역할에 의존하게 된다.

```text
MemberRepository
→ 저장소 역할

MemoryMemberRepository
→ 메모리 방식으로 구현한 저장소
```

다만 테스트에서 `clearStore()`처럼 구현체에만 있는 메서드를 사용해야 한다면,
`MemoryMemberRepository` 타입으로 선언해야 사용할 수 있다.

```text
MemberRepository 타입
→ 인터페이스에 있는 메서드만 사용 가능

MemoryMemberRepository 타입
→ 구현체에 있는 clearStore()까지 사용 가능
```

![회원 리포지토리 테스트 코드 설명](../../images/3-create-member-repository-test-cases-(1).jpg)
