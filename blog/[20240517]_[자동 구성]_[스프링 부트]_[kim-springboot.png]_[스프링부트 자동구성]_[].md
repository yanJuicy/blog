> - 해당 게시물은 인프런 - "스프링 부트 - 핵심 원리와 활용" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
    > [강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)


## 스프링 부트의 자동 구성
스프링 부트는 자동 구성(Auto Configuration) 기능을 통해 일반적으로 자주 사용하는 많은 빈들을 자동으로 등록해준다

`spring-boot-autoconfigure`라는 프로젝트 안에서 수 많은 자동 구성을 제공한다

`JDBCTemplate`을 설정하고 빈으로 등록하는 자동 구성은 다음과 같다

```java
@AutoConfiguration(
    after = {DataSourceAutoConfiguration.class}
)
@ConditionalOnClass({DataSource.class, JdbcTemplate.class})
@ConditionalOnSingleCandidate(DataSource.class)
@EnableConfigurationProperties({JdbcProperties.class})
@Import({DatabaseInitializationDependencyConfigurer.class, JdbcTemplateConfiguration.class, NamedParameterJdbcTemplateConfiguration.class})
public class JdbcTemplateAutoConfiguration {
    public JdbcTemplateAutoConfiguration() {
    }
}
```

#### @AutoConfiguration
자동 구성을 사용하려면 위 애노테이션을 등록해야 한다

`after = ...`을 통해 자동 구성이 실행되는 순서를 지정할 수 있다


#### @ConditionalOnClass
지정된 클래스가 있는 경우에만 설정이 동작한다

`@Conditional...`시리즈가 있고 자동 구성의 핵심이다

#### @Import
스프링에서 자바 설정을 추가할 때 사용한다


#### 스프링 부트가 제공하는 자동 구성(AutoConfiguration)
https://docs.spring.io/spring-boot/docs/current/reference/html/auto-configuration-classes.html

스프링 부트는 수 많은 자동 구성을 제공하고 `spring-boot-autoconfigure`에 모아둔다

![image](https://github.com/yanJuicy/blog/assets/43159295/6a36adb4-163c-423f-8024-da49302633d5)


## @Conditional
같은 소스 코드인데 특정 상황일 때만 특정 빈들을 등록해서 사용하도록 도와주는 기능

이 기능을 사용하려면 `Condtion` 인터페이스를 구현해야 한다

#### Condition
```java
package org.springframework.context.annotation;

public interface Condition {
    boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
```
`matches()` 메서드가 `true`를 반환하면 조건에 만족해서 동작한다

위 인터페이스를 구현해 자바 시스템 속성이 `memory=on`일 때만 동작하도록 하려면 다음과 같이 한다
```java
@Slf4j
public class MemoryCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        String memory = context.getEnvironment().getProperty("memory");
        log.info("memory={}", memory);
        return "on".equals(memory);
    }
}
```

아래 `MemoryConfig`의 적용 여부는 `MemoryCondtion`의 조건에 따라 달라진다
```java
@Configuration 
@Conditional(MemoryCondition.class) //추가 
public class MemoryConfig {
...
```

### @CondtionalOn...
스프링은 `@Condtional`과 관련해서 편리하게 사용할 수 있도록 수 많은 `@CondtionalOn...`를 제공한다
- `@CondtionalOnClass`, `@ConditionalOnMissingClass`
    - 클래스가 있는 경우 동작한다, 나머지는 반대
- `@CondtionalOnProperty`
    - 환경 정보가 있는 경우 동작한다

      ...




## 자동 구성  라이브러리 직접 만들기


자동 구성에 대해 알아보기 위해 간단한 예제를 만든다

실시간으로 자바 메모리 사용량을 확인하는 예제고 나머지 코드는 생략한다
```java
@AutoConfiguration
@ConditionalOnProperty(name = "memory", havingValue = "on")
public class MemoryAutoConfig {

    @Bean
    public MemoryController memoryController() {
        return new MemoryController(memoryFinder());
    }

    @Bean
    public MemoryFinder memoryFinder() {
        return new MemoryFinder();
    }

}
```
#### @AutoConfiguration
스프링 부트가 제공하는 자동 구성 기능을 적용할 때 사용하는 애노테이션

#### @ConditionalOnProperty
`memory=on`이라는 환경 정보가 있을 때 라이브러리를 적용한다

라이브러리를 가지고 있더라도 상황에 따라서 기능을 켜고 끌 수 있다


### 자동 구성 대상 지정
스프링 부트 자동 구성을 적용하려면 다음 파일에 자동 구성 대상을 꼭 지정해줘야한다

`src/main/resources/META-INF/spring/
org.springframework.boot.autoconfigure.AutoConfiguration.imports`

![image](https://github.com/yanJuicy/blog/assets/43159295/77f84291-2d31-48d2-8f3b-6e6819d9f8a3)

파일안에 작성하는 내용은 다음이다

`memory.MemoryAutoConfig`

이는 앞서 만든 자동 구성인 `memory.MemoryAutoConfig`를 패키지에 포함해서 지정해준다

스프링 부트는 시작 시점에 `org.springframework.boot.autoconfigure.AutoConfiguration.imports` 정보를 읽어서 자동 구성으로 사용한다

### 빌드
스프링 부트 자동 구성 기능이 포함된 `jar` 파일을 만든다
- 빌드 명령어
    - `./gradlew clean build`
- 빌드 결과
    - `build/libs/*.jar`

![image](https://github.com/yanJuicy/blog/assets/43159295/bdb05a58-0f0f-40e0-b7bf-6083479e71bc)



## 자동 구성 라이브러리 사용
새 프로젝트(`project-v2`)를 만들어서 위에서 만든 자동 구성 라이브러리를 적용한다

- `project-v2/libs` 폴더에 자동 구성 라이브러리(`memory-v2.jar`) 추가
```groovy
dependencies {
    implementation files('libs/memory-v2.jar') //추가
```


### 라이브러리 설정
`memory-v2`의 자동 구성은 `memory=on` 조건을 만족할 때 실행한다

아래와 같이 설정한다

![image](https://github.com/yanJuicy/blog/assets/43159295/e83e06a5-3aef-4031-a932-d7e5dde88f4e)



## 자동 구성 이해

스프링 부트 자동 구성이 동작하는 방식은 다음 순서로 확인할 수 있다

- `@SpringBootApplication`
- -> `@EnableAutoConfiguration`
- -> `@Import(AutoConfigurationImportSelector.class)`
- -> `resources/META-INF/spring/ org.springframework.boot.autoconfigure.AutoConfiguration.imports` 파일을 열어서 설정 정보 선택
- 해당 파일의 설정 정보가 스프링 컨테이너에 등록되고 사용

### @EnableAutoConfiguration 동작 방식
```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {...}
```
`AutoConfigurationImportSelector` 는 `ImportSelector` 의 구현체고 설정 정보를 동적으로 선택할 수 있다.

이 코드는 모든 라이브러리에 있는 다음 경로의 파일을 확인한다

`META-INF/spring/ org.springframework.boot.autoconfigure.AutoConfiguration.imports`


스프링 부트는 `org.springframework.boot.autoconfigure.AutoConfiguration.imports` 파일을 읽어서 자동 구성으로 사용한다


#### memory-v2 - org.springframework.boot.autoconfigure.AutoConfiguration.imports

![image](https://github.com/yanJuicy/blog/assets/43159295/455fb469-bc79-4c7e-bd1c-111f7c7caaad)

#### spring-boot-autoconfigure - org.springframework.boot.autoconfigure.AutoConfiguration.imports

![image](https://github.com/yanJuicy/blog/assets/43159295/190c51e1-2846-4a74-b6b5-3f11b05f7826)
