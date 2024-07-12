> - 해당 게시물은 인프런 - "스프링 핵심 원리 - 기본편" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
[강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)


## 스프링 컨테이너 생성

스프링 컨테이너가 생성되는 과정은 다음과 같다

```Java
//스프링 컨테이너 생성
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

`ApplicationContext`를 스프링 컨테이너라고 한다

`ApplicationContext`는 인터페이스고 `AnnotationConfigApplicationContext`는 구현체다

이는 애노테이션 기반의 자바 설정 클래스고, 스프링 컨테이너는 XML을 기반으로도 만들 수 있다


### 스프링 컨테이너 생성 과정

![image](https://github.com/yanJuicy/blog/assets/43159295/bb5c23f6-4646-445c-b991-9a419335f2f1)

다음 코드로 스프링 컨테이너를 생성한다

`new AnnotationConfigApplicationContext(AppConfig.class)`

스프링 컨테이너를 생성할 때는 구성 정보(AppConfig.class)를 지정해야 한다

![image](https://github.com/yanJuicy/blog/assets/43159295/da264222-ad98-4f45-b90e-353c20da1fdb)

스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록한다

![image](https://github.com/yanJuicy/blog/assets/43159295/55ee98a2-bcba-4a53-9305-08e963e7197b)

![image](https://github.com/yanJuicy/blog/assets/43159295/bc85fcfa-f7ca-445d-ace2-72540ca957f7)

스프링은 빈을 생성하고, 의존관계를 주입한다

의존관계를 주입할 때는 설정 정보를 참고한다



## 컨테이너에 등록된 빈 조회

스프링 컨테이너에서 스프링 빈을 찾는 기본적인 방법은 다음과 같다

- `AnnotationConfigApplicationContext.getBean(빈이름, 타입)`
- `AnnotationConfigApplicationContext.getBean(타입)`

조회 대상 스프링 빈이 없으면 다음 예외가 발생한다

`NoSuchBeanDefinitionException: No bean named 'xxxxx' available`



## BeanFactory, ApplicationContext

![image](https://github.com/user-attachments/assets/ac7c7f4c-7a69-4849-a414-8fa22a67bcf7)


### BeanFactory

스프링 컨테이너의 최상위 인터페이스다

스프링 빈을 관리하고 조회하는 역할을 담당한다

`getBean()` 메소드를 제공한다


### ApplicationContext

`BeanFactory` 기능을 모두 상속받아서 제공한다

또한 다양한 부가 기능을 제공한다

![image](https://github.com/user-attachments/assets/c73a54c5-43cb-4950-8692-ea9668ca3a2b)

- 메시지소스를 활용한 국제화 기능
- 환경 변수
- 애플리케이션 이벤트
- 편리한 리소스 조회



## 스프링 빈 설정 메타 정보 - BeanDefinition

![image](https://github.com/user-attachments/assets/48adf3d4-0e11-47e6-b414-0f42a05d5d65)

스프링은 `BeanDefinition`이라는 추상화로 다양한 설정 형식을 지원한다

역할과 구현을 개념적으로 나눈 것이다

- XML을 읽어서 BeanDefinition을 만든다
- 자바 코드를 읽어서 BeanDefinition을 만든다
- 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 BeanDefinition만 알면 된다

`BeanDefinition`을 빈 설정 메타정보라고 한다

스프링 컨테이너는 메타정보를 기반으로 스프링 빈을 생성한다

![image](https://github.com/user-attachments/assets/e6443d39-479f-4af6-84dd-e48adbcde8e4)

`AnnotationConfigApplicationContext`는 `AnnotatedBeanDefinitionReader`를 사용해서 `AppConfig.class`를 읽고 `BeanDefinition`을 생성한다


### BeanDefinition 정보

- BeanClassName: 생성할 빈의 클래스명
- factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름
- factoryMethodName: 빈을 생성할 팩토리 메소드 지정
- Scope: 싱글톤
- lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때까지 최대한 생성을 지연처리 하는지 여부
- InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메소드 명
- DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메소드 명
- Constructor arguments, Properties: 의존관계 주입에서 사용

