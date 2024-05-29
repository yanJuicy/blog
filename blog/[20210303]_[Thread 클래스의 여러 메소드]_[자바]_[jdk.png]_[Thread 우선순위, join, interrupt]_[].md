## Thread 우선순위

`Thread.MIN_PRIORITY = 1` ~ `Thread.MAX_PRIORITY = 10`

디폴트 값은 `Thread.NORMAL_PRIORTY = 5`

우선순위가 높은 스레드가 CPU 배분을 받을 확률이 높다

```Java
public class PriorityThread extends Thread {

    @Override
    public void run() {
        int sum = 0;

        Thread t = Thread.currentThread();
        System.out.println(t + " start");

        for (int i = 0; i < 1000000; i++) {
            sum += i;
        }

        System.out.println(t.getPriority() + " end");   // 스레드의 우선순위 가져옴
    }
}

class PriorityTest {

    public static void main(String[] args) {
        for (int i = Thread.MIN_PRIORITY; i < Thread.MAX_PRIORITY; i++) {
            PriorityThread pt = new PriorityThread();
            pt.setPriority(i);  // 우선순위 할당
            pt.start();
        }
    }
}
```

결과는 다음과 같다

<img width="286" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/88d35589-caa7-42fa-b3ce-9037eaf88250">

반드시 그런건 아니지만 우선순위가 높은 스레드들이 먼저 끝나는 것을 볼 수 있다



## join()

동시에 두 개 이상의 스레드가 실행될 때 다른 스레드의 결과를 참조하여 실행해야 하는 경우 join 메소드를 사용한다

join() 호출한 스레드는 not-runnable 상태가 된다

다른 스레드의 수행이 끝나면 다시 runnable 상태가 된다

![image](https://github.com/yanJuicy/blog/assets/43159295/daa176ea-e24c-46ec-a90a-d14514adb643)

다음은 1 ~ 50, 51 ~ 100 까지의 합을 구하는 두 개의 스레드 예제다

```Java
public class JoinTest extends Thread {

    int start;
    int end;
    int total;

    public JoinTest(int start, int end) {
        this.start = start;
        this.end = end;
    }

    public static void main(String[] args) {
        JoinTest jt1 = new JoinTest(1, 50);
        JoinTest jt2 = new JoinTest(51, 100);

        jt1.start();
        jt2.start();

        try {
            jt1.join();     // main 스레드는 jt1 작업이 끝날 때까지 대기
            jt2.join();     // main 스레드는 jt2 작업이 끝날 때까지 대기
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        int lastTotal = jt1.total + jt2.total;

        System.out.println("jt1.total = " + jt1.total);
        System.out.println("jt2.total = " + jt2.total);

        System.out.println("lastTotal = " + lastTotal);
    }

    @Override
    public void run() {
        for (int i = 0; i <= end; i++) {
            total += i;
        }
    }

}
```

실행 결과는 다음과 같다

<img width="293" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/2f1630d1-366b-497e-81ba-a9b28c5309f1">

main 스레드가 jt1.join(), jt2.join()을 호출해서 다른 스레드의 작업이 끝날 때까지 대기한다


## interrupt()

다른 스레드에 예외(`InterruptedException`)를 발생시키는 interrupt를 보낸다

스레드가 join(), sleep(), wait() 함수에 의해 not-runnable 상태일 때 interrupt() 메소드를 호출하면 다시 runnable 상태가 될 수 있다


