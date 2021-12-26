# JPA

1. ORM 기술 / 패러다임 불일치 문제 해결
2. 인터페이스
3. JPA <- Hibernate(구현체) <- Spring Data JPA<BR>
//Spring Data JPA사용 이유 : Hibernate같은 구현체 교체의 용이성(구현체 매핑 지원) , DB 교체의 용이성(저장소 매핑 지원)


## 구조

- domain // sw의 요구사항 혹은 문제영역 (dao와는 조금 다름 )

```java
@Getter
@NoArgsConstructor // 기본 생성자 자동 추가
@Entity // 테이블과 링크 될 클래스
public class Posts {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 500, nullable = false) //@column으로 컬럼에 옵션 설정
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

     @Builder// Enitiy클래스에선 setter 메소드로 값을 변경하지 않음 대신 생성자로 넣어주는데 가시성을 위해 빌더패턴 사용
    public Posts(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }

/*  빌더 클래스 (쌩코드)

        public static Posts.PostsBuilder builder() {
            return new Posts.PostsBuilder();
        }

        public static class PostsBuilder {

            private String title;
            private String content;
            private String author;

            PostsBuilder() {

            }

              public Posts.PostsBuilder title(String title){
                this.title = title;
                return this;
            }
              public Posts.PostsBuilder content(String content){
                this.content = content;
                return this;
            }
              public Posts.PostsBuilder author(String author){
                this.author = author;
                return this;
            }

            public Posts build() {
                return new Posts( this.title , this.content , this.author );
            }

        }


 */


}

```

