> - 해당 게시물은 인프런 - "스프링 MVC 2편 - 백엔드 웹 개발 활용 기술" 강의를 참고하여 작성한 글 입니다
> - 유료강의이므로 자세한 내용은 없고, 간단한 설명 위주로 정리했습니다
[강의 링크](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)


## 타임리프 스프링 통합

스프링 통합 매뉴얼

https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html

타임리프는 스프링과 통합을 위한 다양한 기능을 편리하게 제공한다

### 스프링 통합으로 추가되는 기능들
- SpringEL 문법 통합
- ${@myBean.doSomething()}처럼 스프링 빈 호출 지원
- 폼 관리를 위한 추가 속성
  - th:object
  - th:field, th:errors, th:errorclass
- 폼 컴포넌트 기능
  - checkbox, radio buttion, List 등을 편리하게 사용할 수 있는 기능 지원
- 스프링의 메시지, 국제화 기능의 편리한 통합
- 스프링의 검증, 오류 처리 통합


### 설정 방법

`build.gradle`

`implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`

### 입력 폼 처리

form 태그 내부를 타임리프를 이용해 효츌적으로 사용할 수 있다

#### th:object
커맨드 객체를 지정한다

#### *{...}
th:object에서 선택한 객체에 접근한다

#### th:field
HTML 태그의 id, name, value 속성을 자동으로 처리해준다

#### 렌더링 전

`<input type="text" th:field="*{itemName}" />`

#### 렌더링 후

`<input type="text" id="itemName" name="itemName" th:value="*{itemName}" />`

id, name, value 속성을 자동으로 처리한다



## 체크 박스
타임리프가 제공하는 폼 기능 th:field를 사용하면 체크 박스를 체크 할때와 아무것도 체크하지 않았을 때를 자동으로 처리할 수 있다

```html
<div class="form-check">
    <input type="checkbox" th:field="*{open}" class="form-check-input">
    <label for="open" class="form-check-label">판매 오픈</label>
</div>
```

위 타임리프는 다음 HTML로 렌더링 된다

```html
<div class="form-check">
    <input type="checkbox" id="open" class="form-check-input" name="open" value="true">
    <input type="hidden" name="_open" value="on"/>
    <label for="open" class="form-check-label">판매 오픈</label>
</div>
```

`<input type="hidden" name="_open" value="on"/>`

이 input이 추가적으로 생겼다

체크 박스를 체크하지 않으면 스프링 MVC에서 `name=_open`만 있는 것을 확인하고 open 체크박스가 체크되지 않았다고 인식한다


## 멀티 체크 박스

멀티 체크박스는 같은 이름의 여러 체크박스를 만들 수 있다
```html
<div th:each="region : ${regions}" class="form-check form-check-inline">
    <input type="checkbox" th:field="*{regions}" th:value="${region.key}" class="form-check-input">
    <label th:for="${#ids.prev('regions')}" th:text="${region.value}" class="form-check-label">서울</label>
</div>
```

여기서 문제는 label 태그 속성에 있다

`th:for="${#ids.prev('regions')}"`

반복해서 HTML 태그를 생성할 때, name은 같아도 되지만 id는 모두 달라야 한다

따라서 타임리프는 체크박스를 each 루프 안에서 반복해서 만들 때 임의로 1, 2, 3... 숫자를 뒤에 붙여준다

예를 들어 다음 처럼 된다

```html
<input type="checkbox" value="SEOUL" class="form-check-input" id="regions1" name="regions">
<input type="checkbox" value="BUSAN" class="form-check-input" id="regions2" name="regions">
<input type="checkbox" value="JEJU" class="form-check-input" id="regions3" name="regions">
```

HTML의 id가 타임리프에 의해 동적으로 만들어진다

label 태그이 `for=id 값` 속성으로 label의 대상이 되는 id 값을 임의로 지정하는 것은 곤란하다

따라서 타임리프는 `ids.prev(...)`, `ids.next(....)`을 제공해서 동적으로 생성되는 id 값을 사용할 수 있다


#### 타임리프 HTML 생성 결과

```html
<div class="form-check form-check-inline">
    <input type="checkbox" value="SEOUL" class="form-check-input" id="regions1" name="regions">
    <input type="hidden" name="_regions" value="on"/>
    <label for="regions1"
           class="form-check-label">서울</label>
</div>
<div class="form-check form-check-inline">
    <input type="checkbox" value="BUSAN" class="form-check-input" id="regions2" name="regions">
    <input type="hidden" name="_regions" value="on"/>
    <label for="regions2"
           class="form-check-label">부산</label>
</div>
<div class="form-check form-check-inline">
    <input type="checkbox" value="JEJU" class="form-check-input" id="regions3" name="regions">
    <input type="hidden" name="_regions" value="on"/>
    <label for="regions3"
</div>
```

label 태그의 for 속성이 checkbox에서 동적으로 생성된 id인 regions1, regions2, regions3에 맞춰 순서대로 입력된다