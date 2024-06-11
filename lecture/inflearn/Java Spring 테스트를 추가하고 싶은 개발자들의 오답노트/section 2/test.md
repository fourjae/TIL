### h2를 이용한 Repository 테스트

```Java
void UserRepository_연결() {
    // given 
    UserEntity userEntity = new UserEntity();
    userEntity.setEmail("test@naver.com");
    userEntity.setAddress("Seoul");
    userEntity.setNickName("tester");

    // when
    UserEntity result = userRepository.save(userEntity);

    // then
    assertThat(result.getId()).isNotNull();
}
``` 

JpaRepository에 선언한 메서드가 잘 동작하는지 테스트 코드를 구현한다.

같은 데이터를 여러번 넣으면 테스트를 병렬로 실행할 때, 동시성 제어 문제가 발생한다.

@Sql 어노테이션을 활용하여 데이터를 미리 저장하여 해결한다.


### h2를 이용한 service 테스트

```Java
void getByEmail_유저_검색() {
    // given 
    String email = "test@naver.com";

    // when
    UserEntity result = userService.getByEmail(email);

    // then
    assertThat(result.getEmail()).isEqualTo("test@naver.com");
    assertThatThrownBy(() -> {
        userService.verifyEmail(1,"nontest@naver.com");
    }).isInstanceOf(EmailNotMatchedException.class);
}
``` 

테스트를 위한 Sql 쿼리를 분리할 때는 @SqlGroup 어노테이션을 활용한다.