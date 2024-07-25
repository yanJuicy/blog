> - 해당 게시물은 인프런 - "스프링 핵심 원리 - 기본편" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
[강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)


## 컴포넌트 스캔과 의존관계 자동 주입

컴포넌트 스캔을 사용하려면 `@ComponentScan` 어노테애션을 설정 클래스에 붙인다

```Java
@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {
}
```

컴포넌트 스캔은 `@Component` 어노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다

```Java
@Component
public class MemoryMemberRepository implements MemberRepository {
}

@Component
public class RateDiscountPolicy implements DiscountPolicy {
}

@Component
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

`@Autowired` 어노테이션으로 의존관계를 자동으로 주입해준다


## 컴포넌트 스캔과 자동 의존관계 주입 과정

### 1. @ComponentScan

<img width="885" alt="image" src="https://github.com/user-attachments/assets/5ffa81e1-f47e-4b7b-b533-4ee73bc63349">

`@ComponentScan`은 `@Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다

이때 스프링 빈의 기본 이름은 클래스명을 사용하는데, 맨 앞글자를 소문자로 한다

ex. MemberServiceImpl -> memberServiceImpl


### 2. @Autowired 의존관계 자동 주입

<img width="910" alt="image" src="https://github.com/user-attachments/assets/6ac470c0-b3da-4946-a327-d24f4d31d885">

생성자에 `@Autowired`를 지정하면, 스프링 컨테이너가 자동으로 해당 빈을 찾아서 주입한다



## 탐색 위치와 기본 스캔 대상

### 탐색할 패키지 지정

```Java
@ComponentScan(
         basePackages = "hello.core",
}
```

`basePackages`: 탐색할 패키지를 포함해서 하위 패키지를 모두 탐색한다

만약 지정하지 않으면 `@ComponentScan`이 붙은 클래스의 패키지가 시작 위치다 


### 컴포넌트 스캔 기본 대상

- `@Component`
- `@Controller`
- `@Service`
- `@Repository`
- `@Configuration`


## 필터

- `includeFilters`: 컴포넌트 스캔 대상을 추가로 지정
- `excludeFilters`: 컴포넌트 스캔에서 제외할 대상을 지정

```Java
@Configuration
@ComponentScan(
        includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
)
static class ComponentFilterAppConfig {
}
```


## 중복 등록과 충돌

### 1. 자동 빈 등록 vs 자동 빈 등록

컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록될 때, 이름이 같은 경우 다음 오류를 발생시킨다

`ConflictingBeanDefinitionException`


### 2. 수동 빈 등록 vs 자동 빈 등록

이 경우 수동 빈 등록이 우선권을 가진다

수동 빈이 자동 빈을 오버라이딩 한다


### 수동 빈 등록, 자동 빈 등록시 스프링 부트 오류

스프링 부트에서는 수동 빈 등록과 자동 빈 등록이 충돌이 나면 오류가 발생하도록 바뀌었다
