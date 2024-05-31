> - 해당 게시물은 인프런 - "스프링 부트 - 핵심 원리와 활용" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
> [강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)

## 외부설정
개발 환경과 운영 환경에 따라서 서로 다른 설정값이 존재한다

예를 들어 다음 같은 설정이 있을 수 있다
- 개발 환경에는 dev.db.com이 필요하고 이 값을 코드에 넣은 다음 빌드한다
- 운영 환경에는 prod.db.com이 필요하고 이 값을 코드에 넣은 다음 빌드한다

빌드는 한번만 하고 각 환경에 맞춰서 실행 실점에 외부 설정값을 주입할 필요가 있다

외부 설정은 일반적으로 4가지 방법이 있다
- OS 환경 변수
- 자바 시스템 속성
- 자바 커맨드 라인 인수
- 외부 파일(설정 데이터)


## OS 환경 변수

OS 환경 변수는 OS를 사용하는 모든 프로그램에서 읽을 수 있는 설정 값이다

`System.getenv()`를 사용해 전체 OS 환경 변수를 Map으로 조회할 수 있다

`System.getenv(key)`를 사용하면 특정 OS 환경 변수의 값을 String으로 조회할 수 있다



## 자바 시스템 속성

실행한 JVM 안에서 접근 가능한 외부 설정이다

자바가 내부적으로 미리 설정해두고 사용하는 속성도 존재한다

다음과 같이 자바 프로그램을 실행할 때 사용한다
- `java -Durl=dev -jar app.jar`
- `-D` VM 옵션을 통해서 key=value 형식으로 속성을 설정 한다 

`System.getProperties()`를 사용하면 Map과 유사한 key=value 형식의 Properties를 받는다

이것을 통해서 모든 자바 시스템 속성을 조회한다

`System.getProperties(key)`를 사용하면 속성값을 조회한다


### 자바 코드로 시스템 속성 설정
- 설정: `System.setProperty(propertyName, "propertyValue")`
- 조회: `System.getProperty(propertyName)`

이 방식을 사용하면 외부로 설정을 분리하는 효과는 없다


## 커맨드 라인 인수

애플리케이션 실행 시점에 외부 설정값을 `main(args)` 메소드의 args 파라미터로 전달한다

다음과 같이 사용한다
- `java -jar app.jar dataA dataB`
- dataA, dataB 2개의 문자가 args에 전달된다

커맨드 라인 인수는 `key=value` 형식이 아니다

단순히 문자열을 여러개 입력 받는 형식이다

실제 애플리케이션을 개발할 때는 주로 `key=value` 형식을 자주 사용하기 때문에 결국 파싱해서 Map 같은 형식으로 변환해야 한다


## 커맨드 라인 옵션 인수

커맨드 라인 인수를 `key=value` 형식으로 구분하는 방법이 필요하다

스프링에서는 커맨드 라인 옵션 인수를 통해 해결해준다

커맨드 라인에 `-(dash)` 2개(`--`)를 연결해서 시작하면 `key=value` 형식으로 정하고 이것을 커맨드 라인 옵션 인수라고 한다
- `--key=value` 형식으로 사용한다
- `--username=userA` `--username=userB` 하나의 키에 여러 값도 지정할 수 있다

스프링이 제공하는 `ApplicationArguments` 인터페이스와 `DefeaultApplicationArguments` 구현체를 사용하여 커맨드 라인 옵션 인수를 파싱해서 편리하게 사용한다

```Java
@Slf4j
public class CommandLineV2 {

    public static void main(String[] args) {
        for (String arg : args) {
            log.info("arg {}", arg);
        }

        ApplicationArguments appArgs = new DefaultApplicationArguments(args);
        log.info("SourceArgs = {}", List.of(appArgs.getSourceArgs()));
        log.info("NonOptionArgs = {}", appArgs.getNonOptionArgs());
        log.info("OptionNames = {}", appArgs.getOptionNames());

        Set<String> optionNames = appArgs.getOptionNames();
        for (String optionName : optionNames) {
            log.info("option args {} = {}", optionName, appArgs.getOptionValues(optionName));
        }

        List<String> url = appArgs.getOptionValues("url");
        List<String> username = appArgs.getOptionValues("username");
        List<String> password = appArgs.getOptionValues("password");
        List<String> mode = appArgs.getOptionValues("mode");

        log.info("url = {}", url);
        log.info("username = {}", username);
        log.info("password = {}", password);
        log.info("mode = {}", mode);
    }
}
```


## 커맨드 라인 옵션 인수와 스프링 부트

스프링 부트는 커맨드 라인 옵션 인수를 활용할 수 있는 `ApplicationArguments`를 스프링 빈으로 등록한다

그 안에 힙력한 커맨드 라인을 저장한다

해당 빈을 주입 받으면 커맨드 라인으로 입력한 값을 어디서든 사용할 수 있다

```Java
@Slf4j
@Component
public class CommandLineBean {

    private final ApplicationArguments arguments;

    public CommandLineBean(ApplicationArguments arguments) {
        this.arguments = arguments;
    }

    @PostConstruct
    public void init() {
        log.info("source {}", List.of(arguments.getSourceArgs()));
        log.info("optionNames {}", arguments.getOptionNames());
        Set<String> optionNames = arguments.getOptionNames();
        for (String optionName : optionNames) {
            log.info("option args {} = {}", optionName, arguments.getOptionValues(optionName));
        }
    }

}
```



## 스프링 통합

스프링은 외부 설정값이 더이에 위치하든 상관없이 일관성 있고 편리하게 `key=value` 형식의 외부 설정값을 읽을 수 있다

`Environment`와 `PropertySource` 추상화를 통해 가능하다

![image](https://github.com/yanJuicy/blog/assets/43159295/3f30c788-e80d-47bf-a35f-8dc202d0f319)

### PropertySource

스프링은 `PropertySource`라는 추상 클래스를 제공하고, 각각 외부 설정을 조회하는 `XxxPropertySource` 구현체를 만들어두었다
- `CommandLinePropertySource`
- `SystemEnvironmentPropertySource`

스프링은 로딩 시점에 필요한 `PropertySource`를 생성하고, `Environment`에서 사용할 수 있게 연결해둔다


### Environment
`Environment`를 통해서 특정 외부 설정에 종속되지 않고, 일관성 있게 `key=value` 형식의 외부 설정에 접근할 수 있다
- `environment.getProperty(key)`를 통해서 값을 조회활 수 있다
- `Environment`는 내부에서 여러 과정을 거쳐서 `PropertySource`들에 접근한다
- 같은 값이 있을 경우 스프링은 미리 우선순위를 정해둔다

### 설정 데이터(파일)
`application.properties`, `application.yml`도 `PropertySource`에 추가된다

따라서 `Environment`를 통해서 접근할 수 있다


## 설정 데이터 외부 파일

설정 값을 파일에 넣어서 관리하고 애플리케이션 로딩 시점에 해당 파일을 읽는다

`.properties` 파일은 `key=value` 형식을 사용해서 설정값을 관리하기 적합하다

다음과 같이 작성할 수 있다

```Plain Text
url=local.db.com
username=local_user
password=local_pw
#---
spring.config.activate.on-profile=dev
url=dev.db.com
username=dev_user
password=dev_pw
#---
spring.config.activate.on-profile=prod
url=prod.db.com
username=prod_user
password=prod_pw
```

`#---` 파일을 분할해서 프로필을 따로 지정할 수 있다

실행은 다음과 같이 한다
- 커맨드 라인 옵션 인수 실행 
  - `--spring.profiles.active=dev`
- 자바 시스템 속성 실행
  - `-Dspring.profiles.active=dev`
- Jar 실행
  - `java -Dspring.profiles.active=dev -jar xx.jar`
  - `java -jar **.jar --spring.profiles.active=dev`



## 우선순위

스프링 부트는 같은 애플리케이션 코드를 유지하면서 다양한 외부 설정을 사용할 수 있도록 지원한다

https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config

우선순위는 위에서 아래로 적용된다. 아래가 더 우선순위가 높다
- 설정 데이터(`application.properties`)
- OS 환경 변수
- 자바 시스템 속성
- 커맨드 라인 옵션 인수
- `@TestPropertySource`

설정 데이터 우선순위
- jar 내부 `application.properties`
- jar 내부 프로필 적용 파일 `application-{profile}.properties`
- jar 외부 `application.properties`
- jar 외부 프로필 적용 파일 `application-{profile}.properties`

### 우선순위 이해 방법

더 유연한 것이 우선권을 가진다

변경하기 어려운 파일 보다 실행시 원하는 값을 줄 수 있는 자바 시스템 속성이 더 우선권을 가진다

범위가 넓은 것보다 좁은 것이 우선권을 가진다

OS 환경변수 보다 자바 시스템 속성이 우선권이 있다

자바 시스템 속성 보다 커맨드 라인 옵션 인수가 우선권이 있다



