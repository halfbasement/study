# 스프링 애노테이션

- @RequestParm -> 넘어온 매개변수 저장 ( ex: input속성의 name값)
- @ModelAttribute -> 넘어온 매개변수 VO에 저장 후 바인딩 ( vo 변수값이랑 이름 동일해야함)
- Model 클래스 -> 값을 바로 바인딩하여 전달 가능
- @AutoWired -> 의존성 자동 주입 (생성자를 추구하니 알아만 두자)
```java
-------------의존성 주입-----------------
@Service
/*
	<bean id="memberService" class="com.spring.meber.MemberServiceImpl">
	</bean>
*/
@Transactional
public class MemberServiceImpl implements MemberService {



	@Autowired
	private MemberDAO memberDAO;

 /*
	<bean id="memberService" class="com.spring.meber.MemberServiceImpl">
		<property name="memberDAO" ref="memberDAO"/>
	</bean>
	
	<bean id="memberDAO" class="com.spring.member.MemberDAOImpl"> <-- memberDAOImpl에서 이미 생성 됨
	
*/ 
	
	@Override
	public List listMembers() throws DataAccessException {
		List membersList = memberDAO.selectAllMemberList();
		return membersList;
	}

	@Override
	public int addMember(MemberVO member) throws DataAccessException {
		return memberDAO.insertMember(member);
	}

	@Override
	public int removeMember(String id) throws DataAccessException {
		
		return memberDAO.deleteMember(id);
	}
	
}

```

## Rest API

- @RestController -> 그대로 뷰에 보여줌 ( 객체는 json형태로 )

- @PathVariable -> 
```java
	@RequestMapping("/notice/{num}") // localhost:8090/test/notice/123
 	public int notice(@PathVariable("num") int num) throws Exception{
		return num; //123 
	}
	
```

- @RequestBody -> 브라우저에 전달되는 json데이터를 객체로 자동 변환

- @ResponseBody -> RestController를 특정 메서드에 적용하는 것

- @ResponseEntity -> 예외를 알려줌



## spring boot

- @SpringBootApplication // 스프링 부트의 자동설정,스프링 bean읽기와 생성을 모두 자동으로 설정

### lombok

- @RequiredArgsConstructor //final이 붙은 필드만 포함된 생성자 생성 (DI)
- @NoArgsConstructor // 기본 생성자 자동 추가
- @Builder // 생성자 상단에 선언하면 생성자의 매개변수만 포함된 빌더 클래스 자동 생성

### JPA

- @Entity //테이블과 링크될 클래스 , 클래스 이름을 언더스코어 네이밍으로 테이블 이름과 매칭 ( SalesManager.java -> sales_manager table)


### test

- @ExtendWith(SpringExtension.class) // 테스트를 진행할 때 junit 내장된 실행자 외에 다른 실행자 실행(부트테스트와 junit사이의 연결자) 여기선 SpringExtension ( 구 @Runwith)



- dto Test

```java
    @Test
    public void 롬복_기능_테스트(){
        //given
        String name = "test";
        int amount = 1000;

        //when
        HelloResponseDto dto = new HelloResponseDto(name,amount);

        //then
        assertThat(dto.getName()).isEqualTo(name); //assetj의 assertThat사용 이유 ( 자동완성 명확 , 추가 라이브러리 설치 x)
        assertThat(dto.getAmount()).isEqualTo(amount);
    }


```


- controller Test

```java
@Test
    public void hellodto_리턴() throws Exception{
        String name = "sik";
        int amount = 123;

        mvc.perform(get("/hello/dto").param("name",name).param("amount",String.valueOf(amount))) //param의 값은 string만 허용(문자열로 변경)
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name",is(name))) //$.name -> 실제 dto의 필드명 명시
                .andExpect(jsonPath("$.amount",is(amount)));

    }

```