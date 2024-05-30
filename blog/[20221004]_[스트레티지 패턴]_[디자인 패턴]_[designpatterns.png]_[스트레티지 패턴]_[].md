## 스트래티지 패턴(Strategy Pattern)

스트래티지 패턴은 전략을 쉽게 바꿀 수 있도록 해주는 디자인 패턴이다

전략이란 어떤 목적을 달성하기 위해 일을 수행하는 방식, 비즈니스 규칙, 문제를 해결하는 알고리즘 등이다

프로그램에서 전략을 실행할 때는 쉽게 전략을 바꿔야 할 필요가 있는 경우가 많이 발생한다

특히 게임 프로그래밍에서 게임 캐릭터가 공격이나 행동을 바꾸고 싶을 때 스트래티지 패턴은 유용하다

> 스트래티지 패턴은 같은 문제를 해결하는 여러 알고리즘이 클래스별로 캡슐화되어 있고 이들이 필요할 때 교체할 수 있도록 함으로써 동일한 문제를 다른 알고리즘으로 해결할 수 있게 한다

## 스트래티지 패턴 컬레보레이션

![image](https://github.com/yanJuicy/blog/assets/43159295/f752d22e-8a3c-4907-bd6b-f97e3790d36e)

- Strategy: 인터페이스나 추상 클래스로 외부에서 동일한 방식으로 알고리즘을 호출하는 방법을 명시
- ConcreteStrategy: 스트래티지 패턴에서 명시한 알고리즘을 실제로 구현한 클래스
- Context: 스트래티지 패턴을 이용하는 역할

## 스트래티지 패턴 순차 다이어그램

다음은 스트래티지 패턴의 행위 모델을 순차 다이어그램으로 표현한다

![image](https://github.com/yanJuicy/blog/assets/43159295/b5ef3b04-0210-4918-8925-378ab1c22447)

클라이언트가 원하는 스트래티지 객체를 생성하고 Context에 바인딩하면, Context 객체는 바인딩된 스트래티지 객체 타입에 따라 적절한 행위를 실행한다


## 로봇 만들기

예제를 통해 스트래티지 패턴을 알아본다

만들 로봇은 아톰과 태권V다.

두 로봇은 공격 기능과 이동 기능이 있다

다음은 로봇을 설계할 때 사용하는 클래스 다이어그램이다

![image](https://github.com/yanJuicy/blog/assets/43159295/2a4f9cda-3971-4914-b26c-8044a2999ed4)

Atom 클래스와 TaekwonV 클래스는 Robot 추상 클래스의 자식 클래스로 설계했다

이렇게 설계한 이유는 둘 다 공격(attack), 이동(move) 기능이 있는 로봇이기 때문이다

코드는 다음과 같다

*Robot*
```Java
public abstract class Robot {

    private String name;

    public Robot(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public abstract void attack();

    public abstract void move();
}
```

*Atom*
```Java
public class Atom extends Robot {

    public Atom(String name) {
        super(name);
    }

    @Override
    public void attack() {
        System.out.println("I have strong punch and can attack with it");
    }

    @Override
    public void move() {
        System.out.println("I can fly");
    }
}
```

*TaekwonV*
```Java
public class TaekwonV extends Robot {

    public TaekwonV(String name) {
        super(name);
    }

    @Override
    public void attack() {
        System.out.println("I have Missile and can attack with it");
    }

    @Override
    public void move() {
        System.out.println("I can only walk");
    }
}
```

*Client*
```Java
public class Client {

    public static void main(String[] args) {
        Robot t = new TaekwonV("TaekwonV");
        Robot a = new Atom("Atom");

        System.out.println("My name is " + t.getName());
        t.move();
        t.attack();

        System.out.println();

        System.out.println("My name is " + a.getName());
        a.move();
        a.attack();
    }
}
```

## 문제점

지금 설계는 다음 문제가 발생한다

1. 기존 로봇의 공격 또는 이동 방법을 수정하려면 어떤 변경 작업을 해야 하나? 예를 들어 Atom이 날 수 없고 오직 걷게만 만들고 싶다면? 또는 TaekwonV를 날게 하려면?
2. 새로운 로봇을 만들어 기존의 공격 또는 이동 방법을 추가하거나 수정하려면? 예를 들어 새로운 로봇으로 Sungard를 만들어 TakewonV의 미사일 공격 기능을 추가하려면?

### 1. 기존 로봇의 공격, 이동 방법을 수정하는 경우

Atom이 날 수 없고 오직 걷게만 만들고 싶다면 다음과 같이 move 코드를 수정해야 한다

*Atom*
```Java
    ...

    @Override
    public void move() {
        // System.out.println("I can fly");
        System.out.println("I can only walk");
    }
```

이는 새로운 기능으로 변경하려고 기존 코드의 내용을 수정한다

이는 OCP를 위반한다

또한 Atom의 move 메소드와 TaekwonV의 move 메소드가 동일한 기능을 실행하므로 중복 코드가 발생한다

만약 걷는 방식에 문제가 있거나 새로운 방식으로 수정하려면 모든 중복된 코드를 일관성 있게 변경해야만 한다

로봇의 종류가 많아질수록 중복 코드를 일관성 있게 유지 관리하는 일은 힘들어진다

### 2. 새로운 로봇에 공격, 이동 방법을 추가 or 수정하는 경우

현재 설계는 로봇 자체가 캡슐화 단위이므로 새로운 로봇을 추가하기 쉽다

다음과 같이 Sungard를 추가할 수 있다

![image](https://github.com/yanJuicy/blog/assets/43159295/c2d653a6-b3aa-445a-a025-c23888563915)

그러나 새로운 로봇에 기존의 공격 또는 이동 방법을 추가하거나 변경하려고 하면 문제가 발생한다

Sungard 클래스에서 TaekwonV의 미사일 공격 기능을 사용하려고 하면 attack 메소드가 중복해서 사용된다

이러한 중복 코드는 나중에 심각한 문제를 일으킨다

새로운 방식의 이동 기능과 공격 기능을 로봇에게 제공하려면 관련된 기존의 모든 코드를 수정해야 한다



## 해결책

문제를 해결하려면 무엇이 변화되는지 찾아야 한다

변화된 것을 찾은 후에는 이를 클래스로 캡슐화해야 한다

로봇 예제에서 변화되면서 문제를 발생시키는 요인은 이동과 공격의 변화다

이를 캡슐화하려면 외부에서 구체적인 이동 방식과 공격 방식을 담은 구체적인 클래스들을 은닉해야 한다

이를 위해 공격과 이동을 위한 인터페이스를 각각 만들고 이를 구현한 클래스도 만들어야 한다

![image](https://github.com/yanJuicy/blog/assets/43159295/57102848-f283-4bb4-a51a-b8b79f8d99f9)

MovingStrategy는 이동 기능을 캡슐화하는 인터페이스고 AttackStrategy는 공격 기능을 캡슐화하는 인터페이스다


> 무엇이 변화되는지를 찾은 후에 이를 클래스로 캡슐화한다


클라이언트에서는 연관 관계를 이용해 이동 기능과 공격 기능의 변화를 포함시킨다

Robot 클래스가 이동 기능과 공격 기능을 이용하는 클라이언트 역할을 수행한다

Robot 클래스는 변화를 처리하기 위해 MovingStrategy와 AttackStrategy 인터페이스를 포함한다

![image](https://github.com/yanJuicy/blog/assets/43159295/68844fab-c243-49f0-8cb5-9c9e62c57d38)

구체적인 이동 방식과 공격 방식이 MovingStrategy와 AttackStrategy 인터페이스에 의해 캡슐화 됐다

따라서 이 기능을 이용하는 로봇 객체와 상관없이 이동 방식과 공격 방식의 변화뿐만 아니라 현재 변화도 잘 처리할 수 있다

새로운 공격 방식이 개발되어도 AttackStrategy 인터페이스가 Robot 클래스의 변경을 차단해준다

이는 새로운 코드가 추가되어도 기존 코드에 영향을 미치지 못하므로 OCP를 만족한다


정리하면 다음과 같다

| 클래스 이름                          | 클래스 설명                                     |
|---------------------------------|--------------------------------------------|
| Robot                           | 이동과 공격을 실행하는 메소드가 있고, 이를 상속받아 구체적인 로봇을 만드다 |
| Atom, TaekwonV                  | Robot 클래스를 상속받아 실제 로봇을 구현한다                |
| AttackStrategy                  | 각 로봇이 취할 수 있는 공격 방법에 대한 인터페이스              |
| PunchStrategy, MissileStrategy  | 각 공격 방법을 실제로 구현한다                          |
| MovingStrategy                  | 각 로봇이 취할 수 있는 이동 방법에 대한 인터페이스              |
| WalkingStrategy, FlyingStrategy | 각 이동 방법을 실제로 구현한다                          |


수정한 코드는 다음과 같다

*Robot*
```Java
public abstract class Robot {

    private String name;
    private MovingStrategy movingStrategy;
    private AttackStrategy attackStrategy;

    public Robot(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void attack() {
        attackStrategy.attack();
    }

    public void move() {
        movingStrategy.move();
    }

    public void setMovingStrategy(MovingStrategy movingStrategy) {
        this.movingStrategy = movingStrategy;
    }

    public void setAttackStrategy(AttackStrategy attackStrategy) {
        this.attackStrategy = attackStrategy;
    }
}
```

*Atom*
```Java
public class Atom extends Robot {

    public Atom(String name) {
        super(name);
    }

}
```

*TaekwonV*
```Java
public class TaekwonV extends Robot {

    public TaekwonV(String name) {
        super(name);
    }

}
```

*MovingStrategy*
```Java
public interface MovingStrategy {
    void move();
}
```


*FlyingStrategy*
```Java
public class FlyingStrategy implements MovingStrategy {

    @Override
    public void move() {
        System.out.println("I can fly");
    }
}
```

*WalkingStrategy*
```Java
public class WalkingStrategy implements MovingStrategy {

    @Override
    public void move() {
        System.out.println("I can only walk");
    }
}
```


*AttackStrategy*
```Java
public interface AttackStrategy {
    void attack();
}
```


*MissileStrategy*
```Java
public class MissileStrategy implements AttackStrategy {

    @Override
    public void attack() {
        System.out.println("I have missile and can attack with it");
    }
}
```


*PunchStrategy*
```Java
public class PunchStrategy implements AttackStrategy {

    @Override
    public void attack() {
        System.out.println("I have strong punch and can attack with it");
    }
}
```


*Client*
```Java
public class Client {

    public static void main(String[] args) {
        Robot t = new TaekwonV("TaekwonV");
        Robot a = new Atom("Atom");

        t.setMovingStrategy(new WalkingStrategy());
        t.setAttackStrategy(new MissileStrategy());

        a.setMovingStrategy(new FlyingStrategy());
        a.setAttackStrategy(new PunchStrategy());

        System.out.println("My name is " + t.getName());
        t.move();
        t.attack();

        System.out.println();

        System.out.println("My name is " + a.getName());
        a.move();
        a.attack();
    }

}
```

스트래티지 패턴을 로봇에 적용하면 다음과 같다

![image](https://github.com/yanJuicy/blog/assets/43159295/ff9c9489-4698-4fcd-92bc-fe8a77292f53)

- Robot, Atom, TaekwonV는 Context 역할을 한다
- MovingStrategy, AttackingStrategy는 Strategy 역할을 한다
- WalkingStrategy, FlyingStrategy, PunchStrategy, MissileStrategy 클래스는 ConcreteStrategy 역할을 한다

