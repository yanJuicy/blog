## wait(), notify() 메소드를 활용한 동기화 프로그래밍

리소스가 특정 조건에서 더 이상 유효하지 않은 경우 리소스를 기다리기 위해 스레드는 wait 상태가 된다

wait 상태 스레드는 notify()가 호출될 때까지 기다린다

유효한 자원이 생기면 notify()가 호출되고 wait 상태인 스레드들 중 무작위로 하나의 스레드를 재시작한다

notifyAll()을 호출하면 wait 상태인 모든 스레드가 재시작 되고 자원을 갖기 위해 경쟁한다

이 경우 유효한 리소스만큼의 스레드만이 실행되고 자원을 갖지 못한 나머지 스레드는 다시 wait 상태가 된다

## wait(), notify() 사용 예제

도서관에서 책을 빌리고 반납하는 예제 코드다

```Java
import java.util.ArrayList;

class FastLibrary {

    public ArrayList<String> shelf = new ArrayList<>();

    public FastLibrary() {
        shelf.add("태백산맥 1");
        shelf.add("태백산맥 2");
        shelf.add("태백산맥 3");
    }

    public synchronized String lendBook() {
        Thread t = Thread.currentThread();

        String book = shelf.remove(0);
        System.out.println(t.getName() + ": " + book + " lend");

        return book;
    }

    public synchronized void returnBook(String book) {
        Thread t = Thread.currentThread();

        shelf.add(book);

        System.out.println(t.getName() + ": " + book + " return");
    }
}

class Student extends Thread {

    @Override
    public void run() {
        try {
            String title = WaitNotify.library.lendBook();   // 도서 대출
            if (title == null) {
                return;
            }

            sleep(5000);

            WaitNotify.library.returnBook(title);   // 도서 반납
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class WaitNotify {

    public static FastLibrary library = new FastLibrary();

    public static void main(String[] args) {
        Student std1 = new Student();
        Student std2 = new Student();
        Student std3 = new Student();
        Student std4 = new Student();
        Student std5 = new Student();
        Student std6 = new Student();

        std1.start();
        std2.start();
        std3.start();
        std4.start();
        std5.start();
        std6.start();
    }
}
```

실행 결과는 다음과 같이 예외가 발생한다

<img width="910" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/b14cb0f7-2dd5-43df-8d05-935872150017">

현재 자원은 3개인데 스레드 6개가 자원을 경쟁해서 가져가기 때문에 자원을 가져가지 못한 스레드에서 예외를 발생시킨다

```Java
public FastLibrary() {
    shelf.add("태백산맥 1");
    shelf.add("태백산맥 2");
    shelf.add("태백산맥 3");
}
```

다음과 같이 `lendBook()`에서는 자원이 생길때까지 `wait()`하는 코드를 추가한다

또한 `returnBook()` 메소드에서 자원이 유효한 것을 알리기 위해 `notify()` 코드를 추가한다

```Java
public synchronized String lendBook() throws InterruptedException {
    Thread t = Thread.currentThread();
    
    if (shelf.size() == 0) {    // 추가
        System.out.println(t.getName() + " waiting start");
        wait();
        System.out.println(t.getName() + " waiting end");
    }

    String book = shelf.remove(0);
    System.out.println(t.getName() + ": " + book + " lend");

    return book;
}

public synchronized void returnBook(String book) {
    Thread t = Thread.currentThread();

    shelf.add(book);
    notify();   // 추가

    System.out.println(t.getName() + ": " + book + " return");
}
```

정상적으로 실행이 된다

<img width="527" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/d05e83ae-e6da-4329-9df5-88496e8d7b04">


## notifyAll() 사용 예제

lendBook(), returnBook() 메소드를 수정한다

```Java
public synchronized String lendBook() throws InterruptedException{
		
		Thread t = Thread.currentThread();
		
		while( shelf.size() == 0 ){
			System.out.println(t.getName() + " waiting start");
			wait();
			System.out.println(t.getName() + " waiting end");
		}
		String book = shelf.remove(0);
		System.out.println(t.getName() + ": " + book + " lend");
		
		return book;
	}
}

public synchronized void returnBook(String book){

		Thread t = Thread.currentThread();
		
		shelf.add(book);
		notifyAll();
		System.out.println(t.getName() + ": " + book + " return");
}
```

`notifyAll()`을 사용하면 자원을 할당 받지 못하는 스레드가 생길 수 있다

때문에 `lendBook()` 메소드 안 while 문에서 실행 대기 상태로 깨어난 스레드가 자원을 할당 받을 때까지 wait()을 반복해준다



