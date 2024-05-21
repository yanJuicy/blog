## Wrapper 객체

> 객체 지향 프로그래밍에서는 모든 것들을 객체로 다뤄야 한다
> 하지만 성능상의 이유로 현실과 타협할 수밖에 없는 경우가 있다

지금까지 다음과 같은 원시 타입을 사용했고, 이들을 객체화할 수 있다

| Primitive Data Type | Wrapper Class |
|---------------------|---------------|
| char                | Character     |
| byte                | Byte          |
| short               | Short         |
| int                 | Integer       |
| long                | Long          |
| float               | Float         |
| double              | Double        |
| boolean             | Boolean       |

char, int, long과 같은 데이터 타입은 실제로 값 이상의 의의를 가지지 않기 때문에, 비용이 많이 드는 객체 대신 원시 타입을 사용한다

자바는 기본형들을 추상화해서 객체화는 했지만, 값 이상의 의의를 가지지 않으면 성능상의 이유로 원시 값을 그대로 사용한다

객체로의 기능이 필요할 때 원시 값들을 잠시 객체로 만들 수 있다

기본값을 객체화해서 감싼다는 의미로 `Wrapper Class`라고 한다

기본값을 객체화하는 것을 박싱이라고 하며, 객체를 다시 기본값으로 만드는 것을 언박싱이라고 한다

문법적으로 자연스럽게 보여주기 위해 오토 박싱, 오토 언박싱도 존재한다

박싱을 통해 객체화된 원시 타입은 클래스처럼 구현되어 있는 메소드들을 자유롭게 사용하고 객체의 기능도 할 수 있다

```java
Integer num = new Integer(17);  // Boxing
int n = num.intValue(); // UnBoxing

Character ch = 'X'; // AutoBoxing
char c = ch; // AutoUnBoxing
```

