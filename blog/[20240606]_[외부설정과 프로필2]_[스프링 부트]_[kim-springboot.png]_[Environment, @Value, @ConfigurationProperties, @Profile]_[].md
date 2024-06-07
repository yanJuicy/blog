> - 해당 게시물은 인프런 - "스프링 부트 - 핵심 원리와 활용" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
> [강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)


## Environment

다음과 같은 외부 설정은 스프링이 제공하는 `Environment`를 통해서 일관된 방식으로 조회할 수 있다

- 설정 데이터(application.properties)
- OS 환경변수
- 자바 시스템 속성
- 커맨드 라인 옵션 인수


*application.properties* 예시
```Java
my.datasource.url=local.db.com
my.datasource.username=local_user
my.datasource.password=local_pw
my.datasource.etc.max-connection=1
my.datasource.etc.timeout=3500ms
my.datasource.etc.options=CACHE,ADMIN
```

다음 예시처럼 설정 데이터 값을 가져올 수 있다

```Java
@Slf4j
@Configuration
public class MyDataSourceEnvConfig {

    private final Environment env;

    public MyDataSourceEnvConfig(Environment environment) {
        this.env = environment;
    }

    @Bean
    public MyDataSource myDataSource() {
        String url = env.getProperty("my.datasource.url");
        String username = env.getProperty("my.datasource.username");
        String password = env.getProperty("my.datasource.password");
        int maxConnection = env.getProperty("my.datasource.etc.max-connection", Integer.class);
        Duration timeout = env.getProperty("my.datasource.etc.timeout", Duration.class);
        List<String> options = env.getProperty("my.datasource.etc.options", List.class);

        return new MyDataSource(url, username, password, maxConnection, timeout, options);
    }

}
```



## @Value

`@Value`를 사용하면 외부 설정값을 편리하게 주입받을 수 있다

`@Value`도 내부에서는 `Environment`를 사용한다

```Java
@Configuration
public class MyDataSourceValueConfig {

    @Value("${my.datasource.url}")
    private String url;

    @Value("${my.datasource.username}")
    private String username;

    @Value("${my.datasource.password}")
    private String password;

    @Value("${my.datasource.etc.max-connection}")
    private int maxConnection;

    @Value("${my.datasource.etc.timeout}")
    private Duration timeout;

    @Value("${my.datasource.etc.options}")
    private List<String> options;
        
    @Bean
    public MyDataSource myDataSource1() {
        return new MyDataSource(url, username, password, maxConnection, timeout, options);
    }
    
    @Bean
    public MyDataSource myDataSource2(
            @Value("${my.datasource.url}") String url,
            @Value("${my.datasource.username}") String username,
            @Value("${my.datasource.password}") String password,
            @Value("${my.datasource.etc.max-connection}") int maxConnection,
            @Value("${my.datasource.etc.timeout}") Duration timeout,
            @Value("${my.datasource.etc.options}") List<String> options) {
        
        return new MyDataSource(url, username, password, maxConnection, timeout, options); 
    }
}
```

`@Value`는 필드에 사용할 수도 있고, 파라미터에 사용할 수도 있다


`@Value`로 하나씩 외부 설정 정보의 키 값을 입력받고, 주입 받아와야 하는 부분이 번거롭다


## @ConfigurationProperties

`@ConfigurationProperties`를 사용하면 외부 설정의 묶음 정보를 객체로 변환하는 기능을 제공한다

이렇게 하면 잘못된 타입이 들어오는 문제도 방비할 수 있고, 객체를 통해서 활용할 수 있는 부분들이 많아진다

다음과 같이 적용할 수 있다

```Java
@Data
@ConfigurationProperties("my.datasource")
public class MyDataSourcePropertiesV1 {

    private String url;
    private String username;
    private String password;
    private Etc etc = new Etc();

    @Data
    public static class Etc {
        private int maxConnection;
        private Duration timeout;
        private List<String> options = new ArrayList<>();
    }

}
```

`@ConfigurationProperties` 어노테이션이 있으면 외부 설정을 주입 받는 객체를 의미한다

여기에 외부 설정 key의 묶음 시작인 `my.datasource`를 적는다

외부 설정을 주입 받을 객체를 생성하고 각 필드를 외부 설정의 키 값에 맞춘다

기본 주입 방식은 `Getter`, `Setter`가 필요하다


그리고 스프링에게 사용할 `@ConfigurationProperties`를 지정해야 한다

```Java
@EnableConfigurationProperties(MyDataSourcePropertiesV1.class)
public class MyDataSourceConfigV1 {

    private final MyDataSourcePropertiesV1 properties;

    public MyDataSourceConfigV1(MyDataSourcePropertiesV1 properties) {
        this.properties = properties;
    }

    @Bean
    public MyDataSource dataSource() {
        return new MyDataSource(
                properties.getUrl(),
                properties.getUsername(),
                properties.getPassword(),
                properties.getEtc().getMaxConnection(),
                properties.getEtc().getTimeout(),
                properties.getEtc().getOptions()
        );
    }

}
```

`@EnableConfigurationProperties(MyDataSourcePropertiesV1.class)` 이렇게 하면 해당 클래스는 스프링 빈으로 등록되고 필요한 곳에서 주입 받아 사용 가능하다



## YAML

스프링에서는 설정 데이터로 `application.properties` 뿐만 아니라 `application.yml` 형식도 지원한다


`application.yml` 예시

```yaml
my:
  datasource:
    url: local.db.com
    username: local_user
    password: local_pw
    etc:
      max-connection: 1
      timeout: 60s
      options: LOCAL, CACHE
```



## @Profile

환경마다 다른 빈을 등록할 수 있다

예를 들어 로컬에서는 가짜 결제 기능이 있는 빈을 등록하고, 운영 환경에서는 실제 결제 기능이 있는 빈을 등록한다

```Java
@Configuration
public class PayConfig {

    @Bean
    @Profile("default")
    public LocalPayClient localPayClient() {
        return new LocalPayClient();
    }

    @Bean
    @Profile("prod")
    public ProdPayClient prodPayClient() {
        return new ProdPayClient();
    }

}
```

`@Profile` 어노테이션을 사용하면 해당 프로필이 활성화된 경우에만 빈을 등록한다

- `default` 프로필(기본값)일 경우 `LocalPayClient`를 빈으로 등록한다
- `prod` 프로필인 경우 `ProdPayClient`를 빈으로 등록한다



