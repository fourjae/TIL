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

### mockMvc를 이용한 controller 테스트

```Java
void 헬스_체크시_200_응답() {
    mockMvc.perform(get("helth_check.html")
    .andExpect(status().isOk())
    .andExpect(jsonPath("$.email").value("test@naver.com"))
    );
}

void 사용자가_내_정보_수정() {
    //given
    UserUpdateDto userUpdateDto = UserUpdateDto.builder()
    .nickname("tester")
    .address("seoul")
    .build();
    //when & then
    mockMvc.perform(put("/api/users/me")
        .header("EMAIL","test@naver.com")
        .content(objectMapper.writeValueAsString(userUpdateDto))
    .andExpect(status().isCreated())
    .andExpect(jsonPath("$.email").value("test@naver.com"))
    );
}
``` 

이와 같은 테스트들의 경우
사용자에게 가는 메세지가 제대로 가는지, 마지막 로그인 시간은 알 수 있는 지 등 알기 힘든 부분들이 많다.

또한, 테스트들이 굉장히 느리다. (h2 db 사용 등)