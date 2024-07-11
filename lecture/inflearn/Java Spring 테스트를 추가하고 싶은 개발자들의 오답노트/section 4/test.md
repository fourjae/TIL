#### 패키지 구조 개선

- 먼저 도메인을 만든다.(user, post, common 등)
    - 이후 각 내부에 controller(+ responseDto), domain(+ domainDto), service, infrastructure(+entity, repository) 생성

#### 의존성 역전하기

- interface를 생성한다. (interface)
    - 구현체를 생성한다.(Impl)

    - 구현체로 interface를 상속한다.    
        ```Java
        public class UserRepositoryImpl extends UserRepository {
        }
        ```
    
    - infrastructure에 있는 Repository의 경우 상위 모듈인 service에서 infrastructure에 의존하게 되므로 Service로 변경해 준다.

    - service 외부 연동을 담당하는 port 패키지 생성

        - service에서 사용할 Repository interface들을 생성한다.

    - service -> port interface를 통해 -> Repository 참조하도록 변경

    - RepositoryImpl에서 Repository(interface)를 @Override 하여 구현

    - 마찬가지로 외부 통신 또한 infrastructure에 @Override 하여 구현

- @Test 어노테이션만 사용하여 테스트 진행

Test (**CertificationServiceTest**)
```Java
@Test
public void 외부_연동_테스트(){
    // given - Service를 Bean에 등록하지 않음
    //         직접 구현하여 만든 Fake 객체를 생성자 주입을 통해 Service 객체 생성
    FakeMailSender fakeMailSender = new FakeMailSender();
    CertificationService certificationService = new CertificationService(fakeMailSender);

    // when
    certificationService.send("test@naver.com", 1, "test-naver-com");

    // then
    assertThat(fakeMailSender.email).isEqualTo("test@naver.com");
    assertThat(fakeMailSender.content).isEqualTo(content+"test-naver-com");



}
```
Mock (**FakeMailSender** implements MailSender)
```Java
@Override
public void send(String email, String title, String content){
    this.email = email;
    this.title = title;
    this.content = content;
}
```


### 도메인과 영속성 객체 구분하기

- Domain Entity, Jpa Entity를 분리한다.

- Repository에서 사용중인 JpaEntity -> DomainEntity로 변경

- RepositoryImpl에서 사용중인 JpaEntity -> DomainEntity로 변경

- RepositoryImpl에서 JpaEntity -> DomainEntity로 바꿔주는 메서드 생성

    - JpaEntity에 DomainEntity 객체를 반환해주는 toModel() builder 객체 생성

    - JpaEntity에 JpaEntity 객체를 반환해주는 fromModel() 객체 생성

        - (Domain은 Infrastructure Layer를 모르는 것이 좋음)

    - userJpaRepository.findById(id).map(JpaEntiy::toModel);

- Service에서 JpaEntity -> DomainEntity로 변경

    - DomainEntity가 책임을 가지고 있어야 할 경우 객체로 부터 받은 값을 DomainEntity로 변경해주는 from builder 객체 생성

- JpaRepository.findById() 이후 DomainEntity로 값 변환시 jpa가 변경된 엔티티를 감지하고 수정하지 못하여 JpaRpoeisotry.save 필요함

- Controller에서 JpaEntity -> DomainEntity로 변경

- Controller가 들고있는 부자연스러운 DomainEntity -> Response로 변환해주는 객체는 응답객체에 builder() 객체 생성하여 위임