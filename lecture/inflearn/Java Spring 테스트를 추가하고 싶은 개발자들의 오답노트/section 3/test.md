### 레이어드 아키텍처의 문제점과 해결책

- 모든 테스트가 h2를 필요로 한다.
    - rdb에 결합되어 있고, mockito에 의존함
    - 설계가 잘못되어있을 수 있음

- 특정 기능에 대해 동시 작업이 힘들다.
    - Repository - Service - Controller 순서대로 개발되어야 함
    - 절차지향적인 코드를 작성하게 됨

- 업무 도메인에 대해 파악하기 어렵다.
    - Service 계층이 모든 처리를 진행하는 부담을 갖는다.
    - 모든 객체에 대해 setter, getter가 열려있을 확률이 있다.

#### 아키텍처 개선

- Domain 레이어를 만든다. 이 곳에서 실제 업무를 한다.
- Service 레이어는 Repository와 연결 후 Domain에 일만 시킨다.
- Domain은 계층간 연결된 의존성이 없어야 한다.

아래 아키텍처를 의존성 역전하여 해결
Controller -> Service -> Domain
              Service ->        -> JpaRepository

Controller -> Service -> Domain
              Service -> Repository(interface) <- RepositoryImpl -> JpaRepository


이 변경사항으로 인해
Repository(interface) <- FakeRepository 를 구현하여 사용할 수 있고
Testability를 높일 수 있다.

마찬가지로 Controller 테스트를 위해 아래와 같이 아키텍처 변경이 가능하다.
Controller -> Service(interface) <- FakeService

개선된 아키텍처 흐름

Presentation : Controller -> Service(interface)

Business : ServiceImpl -> JpaRepository(interface), NetworkInteraction(interface)

Domain (OOP, 순수 POJO 코드 쉬운 인스턴스화) : Domain

infrastructure : JpaRepository, NetworkInteractionImpl

### 어떻게 변경할 것인가?

테스트 범위?

라이브러리, 프레임워크 등으로 구현된

Controller 구현은 Spring Team에서

Repository에서는 JPA(Hibernate)에서 충분히 만들고 테스트하고 있으니

DDD에서 가장 중요한 애플리케이션인 Service, Domain을 중심적으로 테스트 해보고자 한다.

Controller, Repository의 테스트가 없으면 테스트 커버리지가 낮아진다면,
도메인의 역할이 빈약하다는 것이고 TODO에 가까운 CRUD 앱을 만들고 있다고 생각 될 수 있다.
- 그럼 이런 애플리케이션에 굳이 테스트가 필요한가? 의심이 들게 된다.


#### 의존성 역전 원리

- 의존성 역전의 원리를 사용하여 모든 외부 호출에도 똑같이 적용하고 테스트가 가능하다.

- 의존 관계를 약화시키고, 호출자와 구현체를 독립적으로 구성하겠다.

- Test용 FakeRepository를 통해 일관된 테스트 결과를 얻을 수 있을 것이다.

#### 도메인 주도 설계

MVC 대신 domain(post, user 등)을 바깥으로 빼내고, 
각 내부에 Controller, model, repository, service를 구성한다.

- MSA로 손쉽게 시스템 확장이 가능하다.

- domain별로 순환참조가 생길 수 있어 주의가 필요하다.

영속성 객체의 Jpa Entity와 Domain Entity를 분리한다.
- Jpa Entity는 CRUD용
- Setter를 지우고 Domain Entity로 이동시킨다.

추가적으로 CQRS(Command and Query Responsibility Segregation)을 적용한다.
