> - 해당 게시물은 인프런 - "토비의 스프링 부트 - 이해와 원리" 강의를 참고하여 작성한 글 입니다
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





