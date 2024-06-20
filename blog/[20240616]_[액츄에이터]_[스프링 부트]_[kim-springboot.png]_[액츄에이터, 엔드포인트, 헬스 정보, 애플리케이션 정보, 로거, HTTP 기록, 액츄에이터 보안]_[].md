> - 해당 게시물은 인프런 - "스프링 부트 - 핵심 원리와 활용" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
> [강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)



## 프로덕션 준비 기능

운영 환경에서 서비스할 때 필요한 기능들을 프로덕션 준비 기능이라한다

다음과 같은 것들을 준비해야 한다

- 지표(metric), 추적(trace), 감사(auditing)
- 모니터링

스프링 부트가 제공하는 액추에이터는 이런 프로덕션 준비 기능을 매우 편리하게 사용할 수 있는 다양한 편의 기능들을 제공한다


액츄에이터 사용을 위해 라이브러리를 추가한다


## 액츄에이터 시작

`implementation 'org.springframework.boot:spring-boot-starter-actuator'`


액츄에이터는 `/actuator` 경로를 통해서 기능을 제공한다


<img width="1386" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/549552b9-65b6-4fc1-9d7e-72c79ef57c36">


액츄에이터의 수 많은 기능을 알기 위해서 다음과 같이 액츄에이터 기능을 웹에 노출하도록 설정한다

```yaml
management:
   endpoints:
     web:
       exposure:
        include: "*"
```



## 엔드포인트 설정

엔드포인트를 사용하려면 다음 2가지 과정이 필요하다

- 엔드포인트 활성화
- 엔드포인트 노출


대부분의 엔드포인트는 기본으로 활성화 되어 있다

특정 엔드포인트를 활성화라려면 `management.endpoint.{엔드포인트명}.enabled=true`를 적용한다

다음은 shutdown 엔드포인트를 활성화하는 예제다

```yaml
management:
  endpoint:
    shutdown:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "*"
```


## 헬스 정보

헬스 정보를 사용하면 애플리케이션에 문제가 발생했을 때 빠르게 인지할 수 있다

단순히 애플리케이션이 요청에 응답할 수 있는지 판단하는 것을 넘어서 데이터베이스 응답, 디스크 사용량 등 다양한 정보들을 포함한다

다음 url에서 확인한다 

`/actuator/health`

![image](https://github.com/yanJuicy/blog/assets/43159295/53c87530-f15b-451b-a013-70205fa54322)


헬스 정보를 더 자세히 보려면 다음 옵션을 지정한다

`management.endpoint.health.show-details=always`



## 애플리케이션 정보

`info` 엔드포인트는 애플리케이션의 기본 정보를 노출한다

기본적으로 다음 기능들을 제공한다

- java: 자바 런타임 정보
- os: OS 정보
- env: `Environment`에서 `info.`로 시작하는 정보
- build: 빌드 정보, `META-INF/build-info.properties` 파일이 필요
- git: git 정보, `git.properties` 파일 필요


다음 내용을 추가한다

```yaml
management:
  info:
    java:
      enabled: true
    os:
      enabled: true
    env:
      enabled: true
info: 
  app:
     name: hello-actuator
     company: yh
```

![image](https://github.com/yanJuicy/blog/assets/43159295/7a396258-be22-48dd-8b45-6be390435517)


git 정보를 노출하려면 `gradle`에 다음 플러그인을 추가해야한다

`id "com.gorylenko.gradle-git-properties" version "2.4.1"`




## 로거

`loggers` 엔드포인트를 사용하면 로깅과 관련된 정보를 확인하고, 실시간으로 변경할 수도 있다

설정 파일에 로깅 레벨을 설정한다

```yaml
logging:
  level:
    hello.controller: debug
```

`/actuator/loggers`에서 전체 로그 정보를 알 수 있다

다음과 같이 패턴을 사용해서 특정 로거 이름을 기준으로 조회할 수도 있다

`http://localhost:8080/actuator/loggers/{로거 이름}`

다음은 설정 파일에서 지정한 `hello.controller`에 대한 로깅 정보를 조회한다

![image](https://github.com/yanJuicy/blog/assets/43159295/5b4bd385-1db2-4274-ab29-30d4bd7a860d)

### 실시간 로그 레벨 변경

현재 `logging level`은 DEBUG다

`loggers` 엔드포인트를 사용하면 애플리케이션을 다시 시작하지 않고, 실시간으로 로그 레벨을 변경할 수 있다

POST 요청으로 로그 레벨을 바꿀 엔드포인트에 `application/json` `content/type`으로 아래 내용을 전달한다

```json
{
    "configuredLevel": "TRACE"
}
```

![image](https://github.com/yanJuicy/blog/assets/43159295/63fa6e41-4b08-4481-b710-10900138acc4)



## HTTP 요청 응답 기록

HTTP 요청과 응답의 기록을 확인하고 싶다면 `httpexchange` 엔드포인트를 사용한다

`HttpExchangeRepository` 인터페이스의 구현체를 빈으로 등록해야 `httpexchange` 엔드포인트를 사용할 수 있다

스프링 부트는 기본적으로 `InMemoryHttpExchangeRepositry` 구현체를 제공한다

```Java
@SpringBootApplication
public class ActuatorApplication {

    public static void main(String[] args) {
        SpringApplication.run(ActuatorApplication.class, args);
    }

    @Bean
    public InMemoryHttpExchangeRepository httpExchangeRepository() {
        return new InMemoryHttpExchangeRepository();
    }

}
```

다음 엔드포인트에 접속하여 확인 가능하다

`/actuator/httpexchanges`

![image](https://github.com/yanJuicy/blog/assets/43159295/b25a420e-37f8-4ee4-81d3-a0db4107bc36)

위에서 로그 레벨을 확인하기 위해 접속했던 url 기록을 확인할 수 있다




## 액츄에이터 보안

액츄에이터의 엔드포인트들은 외부 인터넷에서 접근이 불가능하게 막고, 내부에서만 접근 가능한 내부망을 사용하는 것이 안전한다

또한 액츄에이터를 다른 포트에서 실행시킬 수 있다

`management.server.port=9292`

포트를 분리하는 것이 어렵고 외부 인터넷 망을 통해서 접근해야 한다면 `/actuator` 경로에 서블릿 필터, 스프링 시큐리티 등을 통해서 인증된 사용자만 접근 가능하도록 개발이 필요하다


