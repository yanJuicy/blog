> - 해당 게시물은 인프런 - "스프링 핵심 원리 - 기본편" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
[강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)


## 주문과 할인 도메인 설계

다음과 같이 주문과 할인을 할 수 있는 예제를 만든다

![image](https://github.com/yanJuicy/blog/assets/43159295/d73c67ca-79a3-4574-b3eb-34c8a4ab611c)


역할과 구현을 분리해서 자유롭게 구현 객체를 조립할 수 있게 설계했다

회원 저장소, 할인 정책은 유연하게 변경할 수 있다

![image](https://github.com/yanJuicy/blog/assets/43159295/73af849b-efd6-4de3-9ba7-18241832ef84)


주문 서비스 역할에 해당하는 `OrderServiceImpl`은 다음과 같이 구현할 수 있다

```Java
public interface OrderService {

    Order createOrder(Long memberId, String itemName, int itemPrice);

}
```

```Java
public class OrderServiceImpl implements OrderService {
    
    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
     
    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);
        return new Order(memberId, itemName, itemPrice, discountPrice);
    } 
}
```


## 할인 정책 변경

만약 여기서 할인 정책을 변경하려면 위 `OrderServiceImpl` 코드를 다음과 같이 바꾸면 된다

```Java
public class OrderServiceImpl implements OrderService {
    // private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
}
```

여기서 문제가 발생한다

OCP, DIP 같은 객체지향 설계 원칙을 준수하지 않았다

### DIP 위반

`OrderServiceImpl`은 `DiscountPolicy` 인터페이스에 의존하면서 추가로 구체(구현) 클래스도 같이 의존하고 있다
- 추상(인터페이스) 의존: `DiscountPolicy`
- 구체(구현) 클래스 의존: `FixDiscountPolicy`, `RateDiscountPolicy`


### OCP 위반

지금 코드는 기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다

실제로 `new RateDiscountPolicy()`로 코드가 바뀌었다 

따라서 OCP를 위반한다


현재 의존관계를 그림으로 나타내면 다음과 같다

![image](https://github.com/yanJuicy/blog/assets/43159295/ce52a2cc-edaf-4dc1-aeb3-cee76efb9260)

![image](https://github.com/yanJuicy/blog/assets/43159295/21246b27-df83-4ae0-8449-7542b2a97bcc)

다음과 같이 인터페이스에만 의존하도록 바꿔야 한다

![image](https://github.com/yanJuicy/blog/assets/43159295/2fda9ede-f638-4db0-bec4-4ffebc41bcda)


### 의존 관계 해결

다음과 같이 인터페이스에만 의존하도록 코드를 변경한다

```Java
public class OrderServiceImpl implements OrderService {
     //private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
     private DiscountPolicy discountPolicy;
}
```

이러면 지금은 구현체가 없어서 코드를 실행할 수 없다

실행하면 `null pointer exception`이 발생한다

따라서 `OrderServiceImpl`에 `DiscountPolicy`의 구현체를 밖에서 대신 생성하고 주입해줘야 한다



## 관심사 분리

애플리케이션의 전체 동작 방식을 구성(config)하기 위해, 구현 객체를 생성하고 연결하는 책임을 가지는 별도의 설정 클래스를 만든다

```Java
public class AppConfig {
    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }
    
    public OrderService orderService() {
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
    }
}
```

### 1. 필요한 구현 객체 생성

`AppConfig`는 애플리케이션의 실제 동작에 필요한 구현 객체를 생성한다

- MemberServiceImpl
- MemoryMemberRepository
- OrderServiceImpl
- FixDiscountPolicy

### 2. 객체를 생성자를 통해 주입(연결)

`AppConfig`는 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)한다

- MemberServiceImpl -> MemoryMemberRepository
- OrderServiceImpl -> MemoryMemberRepository, FixDiscountPolicy


### DIP 완성

`MemberServiceImpl`은 `MemberRepository`인 추상(인터페이스)에만 의존하면 된다

구체 클래스를 몰라도 된다



## IoC, DI, 컨테이너


### 제어의 역전 IoC(Inversion of Control)

프로그램 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다

프로그램에 대한 제어 흐름 권한은 모두 `AppConfig`가 가지고 있다

`AppConfig`를 만들기 전에는 클라이언트 구현 객체에서 직접 필요한 객체를 생성하고, 연결하고, 실행했다

`AppConfig`가 등장한 이후에는 구현 객체는 자신의 로직을 실행하는 역할만 담당한다

프로그램 제어 흐름은 이제 `AppConfig`가 가져간다


### 의존 관계 주입 DI(Dependency Injection)

애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 의존관계 주입이라 한다

객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결된다

의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다

의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다

`OrderServiceImpl`은 `DiscountPolicy` 인터페이스에 의존한다

실제 어떤 구현 객체가 사용될지는 모른다

애플리케이션 실행 시점에 참조가 연결된 후 알 수 있다


### IoC 컨테이너, DI 컨테이너

`AppConfig`처럼 객체를 생성하고 관리하면서 의존객체를 연결해 주는 것을 IoC 컨테이너 또는 DI 컨테이너라 한다

의존관계 주입에 초점을 맞춰서 주로 DI 컨테이너라 한다



## 스프링으로 전환

`AppConfig` 클래스 내용을 다음과 같이 바꾼다

```Java
@Configuration
public class AppConfig {
    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }
    
    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(
                memberRepository(),
                discountPolicy());
    }
    
    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
    
    @Bean
    public DiscountPolicy discountPolicy() {
        return new RateDiscountPolicy();
    }
}
```

`@Configuration` 어노테이션을 붙인다

이는 스프링 설정 클래스를 나타낸다

각 메소드에 `@Bean`을 붙여준다

이렇게 하면 스프링 컨테이너에 스프링 빈으로 등록한다


### 스프링 컨테이너

다음은 스프링 컨테이너를 사용하는 예제다

```Java
public class MemberApp {
     public static void main(String[] args) {
         ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
         MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
         
         Member member = new Member(1L, "memberA", Grade.VIP);
         memberService.join(member);
         
         Member findMember = memberService.findMember(1L);
         System.out.println("new member = " + member.getName());
         System.out.println("find Member = " + findMember.getName());
    } 
}
```

`ApplicationContext`를 스프링 컨테이너라 한다

스프링 컨테이너는 `@Configuration`이 붙은 `AppConfig` 클래스를 설정(구성) 정보로 사용한다

여기서 `@Bean`이라 적힌 메소드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다

스프링 컨테이너에 있는 스프링 빈은 `application.getBean()` 메소드를 사용해서 찾을 수 있다
