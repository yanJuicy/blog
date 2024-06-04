> 해당 게시물은 인프런 - "토비의 스프링 부트 - 이해와 원리" 강의를 참고하여 작성한 글 입니다
> 
> [강의 링크](https://www.inflearn.com/course/%ED%86%A0%EB%B9%84-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EC%9D%B4%ED%95%B4%EC%99%80%EC%9B%90%EB%A6%AC)

## 메타 애노테이션과 합성 애노테이션

### 메타 애노테이션 

![image](https://github.com/yanJuicy/blog/assets/43159295/99b232a9-ed25-46bf-9a76-190db5ceed43)

애노테이션에 적용한 애노테이션을 메타 애노테이션이라고 한다

메타 애노테이션이 붙은 클래스(@Controller, @Service)를 사용하면 메타 애노테이션(@Component)을 직접 사용하는 것과 같은 효력을 가진다 

다른 이름을 사용함으로써 목적을 명확히 구분할 수 있다 

- @Controller: Web MVC에서 컨트롤러 역할을 한다
- @Service: 서비스 로직을 담당한다

이들은 `@Component`가 직접 사용된 것처럼 컴포넌트 스캔의 대상이 된다

또한 기존에 없는 새로운 기능을 따로 추가할 수 있다


이것은 상속과 다르다

애노테이션에는 상속이 없고 메타 애노테이션이 되려면 `@Target`을 특별하게 지정해야 한다


테스트 코드에서 다음과 같이 커스텀 애노테이션을 만들 수 있다

JUnit에서 사용하는 `@Test` 대신 `@UnitTest`를 만들어서 사용했다

이때 `UnitTest` 인터페이스에서는 메타 애노테이션인 `@Test`를 넣어야 한다

```Java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Test
@interface UnitTest {
}


public class HelloServiceTest {

    @UnitTest
    void simpleHelloService() {
        SimpleHelloService helloService = new SimpleHelloService();

        String ret = helloService.sayHello("Test");

        Assertions.assertThat(ret).isEqualTo("Hello Test");
    }

    @Test
    void helloDecorator() {
        HelloDecorator decorator = new HelloDecorator(name -> name);

        String ret = decorator.sayHello("Test");

        Assertions.assertThat(ret).isEqualTo("*Test*");
    }
}
```

`@UnitTest`를 더 빠르게 만든 `@FatUnitTest`를 만든다고 가정하면 다음과 같다


![image](https://github.com/yanJuicy/blog/assets/43159295/32d948a2-92cc-4657-a05e-c8c50be1e6c8)

오류 내용은 다음과 같다

![image](https://github.com/yanJuicy/blog/assets/43159295/974aaffd-d16f-4f7a-ba9f-1bb557896042)

`@UnitTest`가 메타 애노테이션이 아니어서 오류가 난다

다음과 같이 `@UnitTest`의 `@Target` 속성을 추가해 주면 메타 애노테이션이 된다

```Java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@UnitTest
@interface FastUnitTest {
}


@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Test
@interface UnitTest {
}
```

### 합성 애노테이션 

스프링에서는 합성 애노테이션도 활용한다

![image](https://github.com/yanJuicy/blog/assets/43159295/76745f0f-56f3-4322-ae52-49293af46cd0)

메타 애노테이션을 하나 이상 적용해서 만든 애노테이션을 합성 애노테이션이라 한다

`@Restcontroller`는 `@Controller`와 `@ResponseBody` 애노테이션을 합친 애노테이션이다



## 합성 어노테이션 적용

기존 실행 코드는 다음과 같다

```Java
@Configuration
@ComponentScan
public class HellobootApplication {

    public static void main(String[] args) {
        SpringApplication.run(HellobootApplication.class, args);
    }


}
```

`@Configuration`과 `@ComponentScan` 애노테이션을 메타 애노테이션으로 가지는 새로운 애노테이션으로 합쳐서 합성 애노테이션을 만든다

```Java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Configuration
@ComponentScan
public @interface MySpringBootAnnotation {
}


@MySpringBootAnnotation
public class HellobootApplication {

    public static void main(String[] args) {
        SpringApplication.run(HellobootApplication.class, args);
    }

}
```

이처럼 합성 애노테이션을 만들어 적용할 수 있다



## 빈 오브젝트의 역할과 구분 

스프링 컨테이너의 여러가지 빈들은 성격이 다르다 

![image](https://github.com/yanJuicy/blog/assets/43159295/8907d4f4-2ddc-409d-ac23-ba7967480000)

### 애플리케이션 로직 빈

애플리케이션의 비즈니스 로직을 담고 있는 클래스로 만들어지는 빈이다

컴포넌트 스캔에 의해서 빈 구성 정보가 생성되고 빈 오브젝트로 등록된다


### 애플리케이션 인프라스트럭처 빈

빈 구성 정보에 의해 컨테이너에 등록되는 빈이지만 애플리케이션의 로직이 아니라 애플리케이션이 동작하는데 필요한 기술 기반을 제공하는 빈이다

개발자가 어떤 빈을 등록할지 선택할 수 있다


### 컨테이너 인프라스트럭처 빈

스프링 컨테이너의 기능을 확장해서 빈의 등록과 생성, 관계 설정, 초기화 등의 작업에 참여하는 빈이다

스프링 컨테이너가 직접 만들고 사용하는 빈이다 

필요한 경우 일부 빈은 주입 받아서 활용 가능하다 



### 빈 구성 정보

스프링 부트의 빈 구성 정보는 사용자 구성 정보와 자동 구성 정보로 구분한다 

애플리키에션 로직을 담고 있는 빈은 사용자 구성정보를 이용해서 빈을 등록한다 

우측에 있는 빈들은 자동 구성정보로 빈이 만들어진다 

![image](https://github.com/yanJuicy/blog/assets/43159295/64964ede-11c6-4608-a463-8e83d12f9249)


### 자동 구성정보(Auto Configuration)

자동 구성는 다음과 같다 

![image](https://github.com/yanJuicy/blog/assets/43159295/d2520e99-a8b5-4cec-a903-99b9b101d2cf)

애플리케이션에서 사용되는 인프라스트럭처 빈들을 담은 Configuration 클래스를 만든다 

그리고 스프링 부트가 필요한 `Configuration`을 골라서 적용한다 

