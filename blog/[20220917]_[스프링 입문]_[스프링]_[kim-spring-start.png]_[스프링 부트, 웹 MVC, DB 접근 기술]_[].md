> - 해당 게시물은 인프런 - "스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술" 강의를 참고하여 작성한 글 입니다
> - [강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)


## 프로젝트 설정

다음 라이브러리들을 사용한다

### 스프링 부트 라이브러리

- spring-boot-starter-web
  - spring-boot-starter-tomcat: 톰캣 (웹서버)
  - spring-webmvc: 스프링 웹 MVC
- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
  - spring-boot
    - spring-core
  - spring-boot-starter-logging
    - logback, slf4j

### 테스트 라이브러리
- spring-boot-starter-test
  - junit: 테스트 프레임워크
  - mockito: 목 라이브러리
  - assert: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
  - spring-test: 스프링 통합 테스트 지원



## View 설정

![image](https://github.com/yanJuicy/blog/assets/43159295/c7a510b7-40a7-45c3-80b9-200410359945)

컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(`ViewResolver`)가 화면을 찾아서 처리한다

스프링 부트 템플릿엔진 기본으로 `viewName`을 다음과 같이 매핑한다

`resources:templates/`+{ViewName}+`.html`


다음과 같이 `@Controller` 클래스 안에서 "hello"를 리턴하면 `resources:templates/hello.html` 파일을 찾아서 리턴한다

```Java
@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "hello!!");
        return "hello";
    }
}
```

<img width="406" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/663d1c70-7b26-4a53-8d0b-fb1493a34eb0">



## 정적 컨텐츠

![image](https://github.com/yanJuicy/blog/assets/43159295/fa45d4c1-750a-4b45-a924-b70e16149853)

스프링은 사용자 요청에 해당하는 컨트롤러를 먼저 찾고 없으면 정적 컨텐츠를 찾아서 리턴해준다

`resources:static` 폴더에서 정적 컨텐츠를 찾는다

<img width="387" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/f998f656-988d-4fee-8fa5-eb218e810e5c">



## MVC와 템플릿 엔진

MVC: Model, View, Controller

![image](https://github.com/yanJuicy/blog/assets/43159295/db90d8e1-956a-4a31-a4cd-05f4a167c438)

스프링에서 위 그림과 같이 MVC를 지원한다

`Controller` 클래스는 `@Controller` 어노테이션을 사용한다

```Java
@Controller
public class HelloController {

    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }
}
```

`Model` 클래스를 통해 `View`에 데이터를 넘겨줄 수 있다



## API

![image](https://github.com/yanJuicy/blog/assets/43159295/98a303fb-dfb9-46cb-af88-ec22914b7595)

`@ResponseBody`를 사용하면 뷰 리졸버(`ViewResolver`)를 사용하지 않는다

대신에 HTTP의 Body에 문자 내용을 직접 반환한다

`ViewResolver` 대신에 `HttpMessageConverter`가 동작한다

기본 문자처리는 `StringHttpMessageConverter`, 기본 객체처리는 `MappingJackson2HttpMessageConverter`가 동작한다

또한 byte 처리 등 여러 `HttpMessageConverter`가 기본으로 등록되어 있다

다음은 문자를 반환해주는 예제다

```Java
@Controller
public class HelloController {

     @GetMapping("hello-string")
     @ResponseBody
     public String helloString(@RequestParam("name") String name) {
         return "hello " + name;
     }
}
```

다음은 객체를 반환하는 예제다

```Java
@Controller
public class HelloController {

    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }
     
    static class Hello {
         private String name;
         
         public String getName() {
             return name;
        }
        
         public void setName(String name) {
             this.name = name;
        } 
    }
    
}
```


## 회원 관리 예제 개발

일반적인 웹 애플리케이션의 구조는 다음과 같다

<img width="742" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/89e2de47-cd51-41f0-bbec-76aa4dc50de6">

- 컨트롤러: 웹 MVC의 컨트롤러 역할
- 서비스: 핵심 비즈니스 로직 구현
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인: 비즈니스 도메인 객체(회원, 주문, 쿠폰 등등)


### 비즈니스 요구사항 정리

- 데이터: 회원 ID, 회원 이름
- 기능: 회원 조회, 회원 등록
- 아직 데이터가 선정되지 않음


아직 데이터 저장소가 선정되지 않아서 다음과 같이 인터페이스로 구현 클래스를 변경할 수 있도록 설계한다

<img width="667" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/77c2320d-02bd-4305-83ba-9590e966aa9d">

개발을 진행하기 위해서 초기 단계에서는 구현체로 가벼운 메모리 기반 데이터 저장소를 사용한다


### 회원 도메인과 리포지토리 구현

인터페이스와 구현체는 다음과 같다

```Java
public interface MemberRepository {

    Member save(Member member);

    Optional<Member> findById(Long id);

    Optional<Member> findByName(String name);

    List<Member> findAll();

}
```

```Java
public class MemoryMemberRepository implements MemberRepository {

    private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }

    public void clearStore() {
        store.clear();
    }
}
```

### 리포지토리 테스트 케이스 작성

자바는 `JUnit`이라는 프레임워크로 테스트 케이스를 실행한다

테스트를 작성할 때는 given-when-then 순서로 작성한다

```Java
class MemoryMemberRepositoryTest {

    MemoryMemberRepository repository = new MemoryMemberRepository();

    @AfterEach
    void afterEach() {
        repository.clearStore();
    }

    @Test
    void save() {
        Member member = new Member();
        member.setName("spring");

        repository.save(member);

        Member result = repository.findById(member.getId()).get();
        assertThat(result).isEqualTo(member);
    }

    @Test
    void findByName() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        Member result = repository.findByName("spring1").get();

        assertThat(result).isEqualTo(member1);
    }

    @Test
    void findAll() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertThat(result.size()).isEqualTo(2);
    }

}
```

`@AfterEach`를 사용해 각 테스트가 종료될 때마다 함수를 실행시킬 수 있다

여기서는 메모리 DB에 저장된 데이터를 삭제한다

한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다

이렇게 되면 이전 테스트 때문에 다음 테스트가 실패할 수 있다

테스트간에는 순서가 있으면 좋은 테스트가 아니며, 각각 독립적으로 실행되야 한다



## 컴포넌트 스캔과 자동 의존관계 설정

컨트롤러가 서비스와 리포지토리를 사용하기 위해 의존 관계를 설정해야 한다

```Java
@Controller
public class MemberController {

    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

생성자에 `@Autowired`가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다 

이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI(Dependency Injection), 의존성 주입이라고 한다

스프링 빈을 등록하는 방법은 2가지가 있다

- 컴포넌트 스캔과 자동 의존관계 설정
- 자바 코드로 직접 스프링 빈 등


### 컴포넌트 스캔 원리

`@Component` 애노테이션이 있으면 자동으로 스프링 빈으로 등록된다

`@Component`를 포함하는 다음 어노테이션도 자동으로 스프링 빈으로 등록된다 
- `@Controller`
- `@Service`
- `@Repository`

`MemberService`, `MemoryMemberRepository`를 스프링 빈으로 등록하면 다음과 같이 빈 간에 의존관계가 설정된다

<img width="883" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/a0f5f6bb-0746-432c-9a0c-409ab2e22d72">



## 자바 코드로 스프링 빈 직접 등록

기존에 `@Service`, `@Controller`, `@Repository`를 사용하는 대신, 다음처럼 빈 설정 클래스에 직접 등록할 수 있다

`@Configuration` 어노테이션을 이용해 스프링 빈 설정 클래스를 만든다

`@Bean` 어노테이션을 이용해 빈을 직접 등록한다

```Java
@Configuration
public class SpringConfig {
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }
    
    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```



## 스프링 DB 접근 기술

Jdbc, JdbcTemplate, JPA, 스프링 데이터 JPA를 사용해서 기존에 있던 `MemberRepository` 인터페이스의 구현체를 쉽게 바꿀 수 있다

![image](https://github.com/yanJuicy/blog/assets/43159295/ad30067d-cccd-4b46-bece-688d466041f8)

위 사진처럼 기존 `MemoryMemberRepository` 대신 `JdbcMemberRepository` 클래스로 구현체를 바꾸려면 다음처럼 코드를 변경하면 된다

```Java
@Configuration
public class SpringConfig {
    private final DataSource dataSource;
    
    public SpringConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }
    
    @Bean
    public MemberRepository memberRepository() {
        // return new MemoryMemberRepository();
        return new JdbcMemberRepository(dataSource);
    }
}
```

설정 클래스에서 단순히 구현체를 변경하면 기존 코드를 전혀 손대지 않을 수 있다

이는 OCP(개방-폐쇄 원칙)를 만족하는 코드다

스프링의 DI를 이용해 쉽게 변경했다

`JdbcTemplate`, `JPA`, `스프링 데이터 JPA`를 이용해 구현 클래스를 만들어도 똑같이 DI를 이용해 OCP를 지키면서 쉽게 변경 가능하다



> `DataSource` 클래스는 데이터베이스 커넥션을 획득할 때 사용하는 객체다
> 
> 스프링 부트는 데이터베이스 커넥션 정보를 바탕으로 `DataSource`를 생성하고 스프링 빈으로 만들어 둔다



## AOP가 필요한 순간

다음 그림처럼 모든 메소드의 호출 시간을 측정하고 싶을 수 있다

![image](https://github.com/yanJuicy/blog/assets/43159295/bee1af45-e727-4bd1-9e26-97c94c50c6ba)

이는 핵심 로직이 아니라 공통 관심 사항이다

모든 메소드마다 일일이 시간 측정 로직을 넣으면 오래 걸리고 유지 보수도 어렵다


## AOP 적용

AOP: Aspect Oriented Programming

공통 관심 사항을 분리해서 원하는 곳에 적용한다

![image](https://github.com/yanJuicy/blog/assets/43159295/be64213c-3e24-4095-805d-e3f8e4eeb425)

```Java
@Component
@Aspect
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();

        System.out.println("START: " + joinPoint.toString());

        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;

            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
        }
    }

}
```

핵심 로직에서 시간을 측정하는 공통 관심 사항을 분리해서 시간을 측정하는 로직을 별도로 만든다

이러면 핵심 로직을 깔끔하게 유지할 수 있고, 시간 측정 관련해서 변경이 필요하면 위에서만 변경하면 된다

또한 시간 측정을 원하는 적용 대상을 선택할 수 있다


### 스프링 AOP 동작 방식

![image](https://github.com/yanJuicy/blog/assets/43159295/11a67006-ddc5-4ca8-8395-9422f9abe4cf)

프록시를 이용해 가짜 빈을 만들고 `joinPoint.proceed()` 메소드를 호출할 때 진짜 빈 객체에 있는 메소드를 호출한다

