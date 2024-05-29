## Thread

### 프로세스
실행 중인 프로그램이다.

프로그램이 실행되면 운영체제로부터 메모리를 할당받아 프로세스가 된다

### 스레드
하나의 프로세스는 하나 이상의 스레드를 가진다.

실제 작업을 수행하는 단위는 스레드다

![image](https://github.com/yanJuicy/blog/assets/43159295/3e0b37fc-c5e5-4179-8c88-c4e7e115b8f5)

## multi-thread

여러 스레드가 동시에 수행되는 프로그래밍, 여러 작업이 동시에 실행되는 효과

스레드는 자신만의 작업 공간을 가진다 (context)

스레드 사이에서 공유하는 자원이 있다 (자바에서는 static instance)

여러 스레드가 자원을 공유하는 경우, 서로 자원을 차지하려는 race condition이 발생할 수 있다

여러 스레드가 경쟁하는 자원을 critical section이라고 한다

critical section에 대한 동기화를 구현하지 않으면 오류가 발생할 수 있다

![image](https://github.com/yanJuicy/blog/assets/43159295/c6355912-0811-4d96-9fc3-39effba5c2fd)

## Thread 만들기

### Thread 클래스 상속

```Java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println(Thread.currentThread());
        
        for (int i = 0; i < 200; i++) {
            System.out.print(i + " ");
        }
    }
}
```

### Runnable 인터페이스 구현
```Java
class MyThread2 implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread());

        for (int i = 0; i < 200; i++) {
            System.out.print(i + " ");
        }
    }
}
```

실행 클래스를 다음과 같이 만들어 실행해본다
```Java
class ThreadTest2 {

    public static void main(String[] args) {
        System.out.println("main start");

        MyThread2 mth = new MyThread2();
        Thread th1 = new Thread(mth);
        th1.start();

        Thread th2 = new Thread(mth);
        th2.start();
    }
}
```

<img width="997" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/59549e30-5254-4453-b950-f012d2cbd4af">

2개의 스레드가 번갈아가면서 실행된다

## Thread Status

![image](https://github.com/yanJuicy/blog/assets/43159295/f0afacdd-381d-4de3-a5a8-253b8dc5e02c)

### Runnable
스레드가 시작되면 스레드풀에 들어간다

스레드풀에 있는 스레드들은 CPU 할당 받기를 기다린다

이 스레드들은 Runnable 상태이다

### Dead
스레드가 다 수행이되서 끝나면 Dead 상태가 된다

### Not Runnable
CPU를 점유할 수 없는 상태다

영원히 이 상태면 좀비가 된다

Runnable에서 Not Runnable로 가는 경우가 3가지 있다

#### sleep
시간이 지나면 다시 Runnable 상태가 된다

#### wait, notify
리소스가 한정되있고 여러 스레드가 있을때, 리소스가 사용가능할 때까지 스레드를 대기시킨다

리소스가 유효한상태면 프로그램에서 notify를 통해 Not Runnable 스레드들 중 하나가 다시 Runnable 상태가 된다

#### join
A 스레드가 B 스레드에 대해 join을 걸면 A 스레드는 Not Runnable 상태가 된다

B 스레드가 작업을 종료하면 A 스레드가 다시 Runnable 상태가 된다