> - 해당 게시물은 인프런 - "스프링 MVC 2편 - 백엔드 웹 개발 활용 기술" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
[강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)


## 타임리프 소개
공식 사이트 https://www.thymeleaf.org/

### 타임리프 특징
- 서버 사이드 HTML 렌더링
- 네츄럴 템플릿
- 스프링 통합 지원

### 타임리프 사용 선언
`<html xmlns:th="http://www.thymeleaf.org">`


## 텍스트
`th:text`를 사용해 데이터를 출력한다

`<span th:text="${data}">`


## 변수

변수를 사용할 때는 변수 표현식을 사용한다

변수 표현식: `${...}`

변수 표현식에는 `SpringEL`이라는 스프링이 제공하는 표현식을 사용할 수 있다

### SpringEL 다양한 표현식 사용

user의 username 프로퍼티에 접근하는 표현식이다

- `<span th:text="${user.username}">`
- `<span th:text="${user['username']}">`
- `<span th:text="${user.getUsername()}">`


list에서 첫 번째 user를 찾고 username 프로퍼티에 접근하는 표현식이다

- `<span th:text="${users[0].username}">`
- `<span th:text="${users[0]['username']}">`
- `<span th:text="${users[0].getUsername()}">`

map에서 userA를 찾고 username 프로퍼티에 접근하는 표현식이다

- `<span th:text="${userMap['userA'].username}">`
- `<span th:text="${userMap['userA']['username']}">`
- `<span th:text="${userMap['userA'].getUsername()}">`

## 기본 객체들

thymeleaf는 기본 객체들을 제공한다
- `${#request}` 
- `${#response}` 
- `${#session}` 
- `${#servletContext}`
- `${#locale}`

스프링 부트 3.0 부터는 기본 객체를 지원하지 않고 `model`에 해당 객체를 추가해서 사용해야 한다

```Java
public String basicObjects(Model model, HttpServletRequest request,
 HttpServletResponse response, HttpSession session) {
     session.setAttribute("sessionData", "Hello Session");
     model.addAttribute("request", request);
     model.addAttribute("response", response);
     model.addAttribute("servletContext", request.getServletContext());
     return "basic/basic-objects";
}
```

## 유틸리티 객체와 날짜

타임리프는 문자, 숫자, 날짜 URI 등을 편리하게 다루는 다양한 유틸리티 객체를 제공한다

- #message: 메시지, 국제화 처리
- #uris: URI 이스케이프 지원
- #dates: java.util.Date 서식 지원
- #calendars: java.util.Calendar 서식 지원
- #temporals: 자바 8 날짜 서식 지원
- #numbers: 숫자 서식 지원
- #strings: 문자 관련 편의 기능
- #objetcs: 객체 관련 기능 제공
- #bools: boolean 관련 기능 제공
- #arrays: 배열 관련 기능 제공
- #lists, #sets, #maps: 컬렉션 관련 기능 제공
- #ids: 아이디 처리 관련 기능 제공

### 자바 8 날짜

`LocalDate`, `LocalDateTime`, `Instant`를 사용하려면 추가 라이브러리가 필요하다.

`thymeleaf-extras-java8time`

스프링 부트 타임리프를 사용하면 해당 라이브러리가 자동으로 추가된다

#### 자바 8 날짜용 유틸리티 객체
`#temporals`

#### 사용 예시
`<span th:text="${#temporals.format(localDateTime, 'yyyy-MM-dd HH:mm:ss')}">`


## URL 링크

`@{...}` 문법으로 URL을 생성한다

### 단순한 URL
`<a th:href="@{/hello}">`

`@{/hello}` -> `/hello`


### 쿼리 파라미터
`<a th:href="@{/hello(param1=${param1}, param2=${param2})}">`

`@{/hello(param1=${param1}, param2=${param2})}` -> `/hello?param1=data1&param2=data2`


### 경로 변수
`<a th:href="@{/hello/{param1}/{param2}(param1=${param1}, param2=$
{param2})}">`

`@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}` -> `/hello/data1/data2`



## 리터럴
타임리프에서 문자 리터럴은 항상 '(작은 따옴표)로 감사야 한다
`<span th:text="'hello world'">`

### 리터럴 대체
리터럴 대체 문법을 사용하면 템플릿을 사용하는 것처럼 편리하다

`<span th:text="|hello ${data}|">`


## 연산
HTML 안에서 연산을 할 수 있다

### 비교 연산
`>` (gt), `<` (lt), `>=` (ge), `<=` (le), `!` (not), `==` (eq), `!=` (neq, ne)

#### 사용 예
`<span th:text="1 gt 10">`

`<span th:text="1 == 10">`


### 조건식
`<span th:text="(10 % 2 == 0)? '짝 수':'홀수'">`


### Elvis 조건식
조건식의 편의 버전

`<span th:text="${data}?: '데이터가 없습니다.'">`

data가 null일 경우 '데이터가 없습니다' 출력


## 속성 값 설정
`th:*`로 속성을 적용하면 기존 속성을 대체한다. 기존 속성이 없으면 새로 만든다

### 속성 설정
`<input type="text" name="mock" th:name="userA" />`

-> 타임리프 렌더링 후 `<input type="text" name="userA" />`

### 속성 추가
- `th:attrappend`: 속성 값의 뒤에 값을 추가한다
- `th:attrprepend`: 속성 값의 앞에 값을 추가한다
- `th:classappend`: class 속성에 자연스럽게 추가한다



## 반복
`th:each`를 사용해 반복해서 출력한다

```html
<tr th:each="user : ${users}">
     <td th:text="${user.username}">username</td>
     <td th:text="${user.age}">0</td>
```

`${users}`의 값을 하나씩 꺼내 `user`에 담아서 태그를 반복 실행한다

`List` 뿐만 아니라 `java.util.Iterable`, `java.util.Enumeration`을 구현한 모든 객체를 반복에 사용할 수 있다

`Map`도 사용할 수 있는데 이 때는 `Map.Entry` 값이 담긴다




## 조건부 평가

`if` , `unless` ( `if` 의 반대)

타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않는다

```html
<span th:text="'미성년자'" th:if="${user.age lt 20}"></span> 
<span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>
```


## 블록 

`<th:block>`은 타임리프의 유일한 자체 태그다

```html
<th:block th:each="user : ${users}">
    <div>
        사용자 이름1 <span th:text="${user.username}"></span>
        사용자 나이1 <span th:text="${user.age}"></span> </div>
    <div>
        요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>
    </div>
 </th:block>
```

`th:block` 안에 내용들이 `th:each`만큼 반복한다


## 템플릿 조각

타임리프는 공통된 웹 페이지 개발을 위해 템플릿 조각과 레이아웃 기능을 지원한다

```html
<footer th:fragment="copy"> 
    푸터 자리 입니다.
</footer>
```

`th:fragment`가 잇는 태그는 다른 곳에 포함되는 코드 조각이다


```html
<h2>부분 포함 insert</h2>
<div th:insert="~{template/fragment/footer :: copy}"></div>

<h2>부분 포함 replace</h2>
<div th:replace="~{template/fragment/footer :: copy}"></div>
```

템플릿에 있는 `th:fragment="copy"`라는 부분을 템플릿 조각으로 가져와서 사용한다

`th:insert`를 사용하면 현재 태그 내부에 추가한다

`th:replace`를 사용하면 현재 태그를 대체한다


### 파라미터 사용
다음과 같이 파라미터를 전달해서 동적으로 조각을 렌더링 할 수 있다
```html
<h1>파라미터 사용</h1>
<div th:replace="~{template/fragment/footer :: copyParam ('데이터1', '데이터2')}"></div>
```

```html
<footer th:fragment="copyParam (param1, param2)"> 
    <p>파라미터 자리 입니다.</p>
    <p th:text="${param1}"></p>
    <p th:text="${param2}"></p>
</footer>
```

