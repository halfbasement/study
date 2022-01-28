# JPA

스프링 < -- >DB

jpa <--> hibernate <--> jdbc <--> db

1.  Entity

- @GeneratedValue(strategy = GenerationType.IDENTITY) -> 키 생성 전략 (AUTO_)INCREMENT

2. crud
- update -> save()
- select->
findById()  : 쿼리 바로 실행/ getOne() : 객체 호출시 쿼리실행

3. 페이징 / 정렬 처리

- 내부적으로 Dialect 사용
- findAll() 사용
- 리턴타입 Page<T>일경우 파라미터로 Pageable 넣어줘야함
- count쿼리도 같이 처리
- 정렬 조건 추가 : Sort타입을 넘길 수 있음

4. 쿼리메서드 & @Query

- 쿼리메서드 : 메서드 이름 자체가 쿼리 , Pageable파라미터 결합 가능
- @Query : 쿼리등록 , 파라미터 바인딩 ( ? , : , #{}) , 엔티티가아닌 Object형식으로 추출가능


BooleanExpression -> where 조건 생성

## 1:N

- PK를 기준으로 잡고 DB모델링하는 방식
- @ManyToOne ( fk 위에다 설정 )
- 연관관계를 맺은 데이터를 가져오면 내부적 조인
- fetch : 연관관계의 데이터를 어떻게 가져올 것인가
    -   member -< board -< comment 일때
    -   comment하나 가져오려고 내부적으로 여러번의 조인을 하는 것은 비효율-(eager loading 즉시로딩)
    - 곧 fetch는 lazy loading 권장 
    - @ManyToOne(fetch=FecthType.LAZY)@Transactional을 사용해야함
-> 레이지 로딩은 조인 대신 fk의 부모 테이블을 다시 호출 하는데 @Transactional 을 설정 안하면 부모 테이블을 호출하기 직전에 db연결이 끝나서 에러 남
lazyLoading사용하면 조인을 하는 대신 부모 테이블 재 호출

- 연관관계에서는 ToString() 주의
    - member -< board -< comment 일때 board의 member(fk)를 호출 하면 member엔티티의 toString까지 호출하게 됨 (db연결이 필요하게 됨) 
    - 습관적으로 fk에 exclude속성을 지정해줘야함 (toString제외) 

- 지연 로딩을 디폴트로 잡고 상황에 맞게 바꾼다.



- JPQL 과 left join
    - join 에서 on 과 where의 차이점 (left join기준) : on 조인 전 / where 조인 후
    - jpa는 엔티티클래스 내에 전혀 연관관계가 없더라조 조인 가능
    - 엔티티 클래스 내부에 연관관계가 있는 경우 ( 엔티티클래스내에 연관관계가 있는 경우)<br>
        - member-< board(member)<br>
        - @Query("select b, w from Board b left join b.write w where b.bno = :bno")<br>
        - 연관관계를 맺고 있으므로 member를 따로 호출 할 필요가 없다 (on이 필요 없음)<br>
        - 지연로딩으로 처리되었으나 내부적으로 조인처리 됨






