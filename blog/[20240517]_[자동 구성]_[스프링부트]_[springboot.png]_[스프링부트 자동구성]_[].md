## 수동 Bean 등록

스프링 부트가 제공하는 자동 구성(Auto Configuration)을 알기 위해 다음 예제를 만든다

`JDBCTemplate`을 사용하기 위한 빈 등록 예제다

###### DbConfig
```java
@Slf4j
@Configuration
public class DbConfig {

    @Bean
    public DataSource dataSource() {
        log.info("dataSource 빈 등록");
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setDriverClassName("org.h2.Driver");
        dataSource.setJdbcUrl("jdbc:h2:mem:test");
        dataSource.setUsername("sa");
        dataSource.setPassword("");
        return dataSource;
    }

    @Bean
    public TransactionManager transactionManager() {
        log.info("transactionManager 빈 등록");
        return new DataSourceTransactionManager(dataSource());
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        log.info("jdbcTemplate 빈 등록");
        return new JdbcTemplate(dataSource);
    }
}
```

## Bean 등록 확인
`JdbcTemplate` , `DataSource` , `TransactionManager`가 스프링 컨테이너에 잘 등록되었는지 확인

```java
@Slf4j
@SpringBootTest
class DbConfigTest {
    @Autowired
    DataSource dataSource;
    
    @Autowired
    TransactionManager transactionManager;
    
    @Autowired
    JdbcTemplate jdbcTemplate;
    
    @Test
    void checkBean() {
         log.info("dataSource = {}", dataSource);
         log.info("transactionManager = {}", transactionManager);
         log.info("jdbcTemplate = {}", jdbcTemplate);
         assertThat(dataSource).isNotNull();
         assertThat(transactionManager).isNotNull();
         assertThat(jdbcTemplate).isNotNull();
    }
}
```
출력 결과를 보면 빈이 정상 등록된 것을 확인할 수 있다

![image](https://github.com/yanJuicy/boot-source/assets/43159295/e4aea788-0771-4005-9007-0d6d58c9aced)


### 빈 등록 제거
`DbConfig` 클래스의 빈들을 등록하지 않아도 스프링 부트에서는 자동으로 등록해 준다
```java 
@Slf4j
//@Configuration
public class DbConfig {
...
```

출력 결과는 다음과 같다

![image](https://github.com/yanJuicy/boot-source/assets/43159295/4c9ae217-3ce4-4755-b3a7-62ef7dc82bca)
