## critical section과 semaphore

critical section은 두 개 이상의 스레드가 동시에 접근하는 경우 문제가 생길 수 있기 때문에 동시에 접근할 수 없는 영역이다

semaphore는 특별한 형태의 시스템 객체이며 get, release 두 개 기능이 있다

한 순간에 하나의 스레드만 semaphore를 얻을 수 있고, 나머지 스레드들은 대기 상태가 된다

semaphore를 얻은 스레드는 critical section에 접근할 수 있다

![](https://gitlab.com/easyspubjava/javacoursework/-/raw/master/Chapter6/6-22/img/semaphore.png)


## 동기화

두 개의 스레드가 같은 객체에 접근할 경우, 동시에 접근해서 오류가 발생한다

동기화는 임계영역에 접근한 경우 공유 자원을 lock 하여 다른 스레드의 접근을 제어한다

동기화를 잘못 구현하면 데드락에 빠질 수 있다


## synchronized

자바에서는 임계영역에 동시 접근을 막기 위해 `synchronized`를 사용한다

### synchronized 블럭

현재 객체 또는 다른 객체를 lock으로 만든다

```Java
public void saveMoney(int save) {
    synchronized(this) {
        int m = this.getMoney();
    
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    
        setMoney(m + save);
    }
}
```

### synchronized 메소드

객체의 메소드에 synchronized 키워드를 사용한다

현재 이 메소드가 속해있는 객체에 lock을 건다

```Java
public synchronized void saveMoney(int save) {
    int m = this.getMoney();

    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    setMoney(m + save);
}
```

## 동기화 예시
```Java
class Bank {
    private int money = 10000;

    public void saveMoney(int save) {
        synchronized (this) {
            int m = this.getMoney();

            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            setMoney(m + save);
        }
    }

    public synchronized void minusMoney(int minus) {
        int m = this.getMoney();

        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        setMoney(m - minus);
    }

    public int getMoney() {
        return money;
    }

    public void setMoney(int money) {
        this.money = money;
    }
}

class Park extends Thread {

    @Override
    public void run() {
        System.out.println("start save");
        Sync.myBank.saveMoney(3000);
        System.out.println("saveMoney(3000): " + Sync.myBank.getMoney());
    }
}

class ParkWife extends Thread {

    @Override
    public void run() {
        System.out.println("start minus");
        Sync.myBank.minusMoney(1000);
        System.out.println("minusMoney(1000): " + Sync.myBank.getMoney());
    }
}

public class Sync {

    public static Bank myBank = new Bank();

    public static void main(String[] args) throws InterruptedException {
        Park p = new Park();
        p.start();

        Thread.sleep(200);

        ParkWife pw = new ParkWife();
        pw.start();
    }

}
```

## 실행 결과

<img width="295" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/72352022-35f5-441e-ab7c-f09e7d8249d9">


