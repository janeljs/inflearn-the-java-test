# inflearn-the-java-test
"더 자바, 애플리케이션을 테스트하는 다양한 방법" 강의 코드 실습 저장소

## 학습 정리
### JUnit 5
  - `ctrl` + `shift` + `t` : 테스트 생성 단축키
  - `ctrl` + `shift` + `r` : 테스트 실행 
  (메서드가 없는 빈 줄에 놓고 단축키를 실행하면 해당 클래스의 모든 테스트를 실행)
  - `@BeforeAll`, `@AfterAll`: static void만 허용
  - 테스트 메서드 이름은 가독성이 좋은 snake case로 많이 작성한다고 하셔서 찾아봤더니 camel case로 작성하는게 맞는 것 같다.
    - [Test method naming convention](https://github.com/alexeymezenin/laravel-best-practices/issues/28)
  - 테스트가 실패하는 이유도 적어주면 좋다.
      - assertEquals를 사용하면 세 번째 파라미터에 메시지를 적어주면 되는데, 문자열 연산에 비용이 클 것 같으면 람다식을 사용해주는 것이 좋다.
      - AssertJ의 assertThat에서는 [as나 withFailMessage 사용](https://stackoverflow.com/questions/28994316/can-you-add-a-custom-message-to-assertj-assertthat)를 사용하면 된다. 
   - `assertTimeoutPreemptively` : 설정한 시간이 경과하자마자 테스트를 종료
     - 단 ThreadLocal을 사용하는 코드가 있다면 side effect가 발생할 수 있다.
       &rarr; 스프링 Transaction 설정이 제대로 안되어 롤백이 안되고 db에 테스트 시 실행한 결과가 반영될 수 있다.
### Mockito
   - Mock 객체는 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 행동을 관리한다.
  - Mockito는 Mock 객체를 생성, 관리, 검증할 수 있는 방법을 제공하는 프레임워크이다.
  - 단위 테스트
    - https://martinfowler.com/bliki/UnitTest.html
    - 모든 의존성을 끊어야(mocking) 단위테스트라고 주장하는 사람들도 있다.
    - 그러나 단위를 클래스나 객체가 아닌 행동의 단위라고 생각하고, 그 행동과 연관되어 있는 모든 객체들은 하나의 단위라고 생각할 수도 있다.
  - 이미 구현되어 있는 클래스는 Mocking할 필요가 없지만, 외부서비스는 Mocking하는게 좋지 않을까.
  - bang.com 등의 payment 서비스는 테스트 환경을 제공한다. 
  - 구현체 없고 인터페이스만 있는 경우
    - 인터페이스를 사용해 구현한 코드가 잘 작동하는지 확인할 때 Mock 객체를 사용할 수 있다.
    - `@Mock` 애너테이션 붙여주고, MockitoExtension을 클래스 위에 등록해주면 된다.
    - 파라미터에 바로 `@Mock`을 붙여도 된다.
  - [How about some stubbing?](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#2)
    -  `when(memberService.findById(1L)).thenReturn(Optional.of(member));`
  - verify
     - notify가 1번 호출되었는지 검증 
       - `verify(memberService, times(1)).notify(study);`
     - 어떤 id가 들어와도 validate가 호출되지 않았는지 검증
       - `verify(memberService, never()).validate(any());`
  - inOrder
     - `inOrder().verify(memberService).notify(study);`
  - verifyNoMoreInteractions
     - `verifyNoMoreInteractions(memberService);`
  - BDD 
    - 애플리케이션이 어떻게 **행동**해야 하는지에 대한 공통된 이해를 구성하는 방법    
    - given (시나리오 진행에 필요한 값을 설정) 
       - `import static org.mockito.BDDMockito.given;`
       - `given(memberService.findById(1L)).willReturn(Optional.of(member));`
    - when (시나리오를 진행하는데 필요한 조건을 명시)
    - then (시나리오를 완료했을 때 보장해야 하는 결과를 명시)
      - `then(memberService).should(times(1)).notify(study);`
      - `then(memberService).shouldHaveNoMoreInteractions();`
- [assert 키워드](https://www.baeldung.com/java-assert)
```java
    public StudyService(MemberService memberService, StudyRepository repository) {
        assert memberService != null;
        assert repository != null;
        this.memberService = memberService;
        this.repository = repository;
    }
```
