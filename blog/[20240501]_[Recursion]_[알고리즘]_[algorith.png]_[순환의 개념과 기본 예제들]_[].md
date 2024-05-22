> - 해당 게시물은 인프런 - "영리한 프로그래밍을 위한 알고리즘 강좌" 강의를 참고하여 작성한 글 입니다
> - [강의 링크](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/dashboard)


## Recursion

Recursion은 자기 자신을 호출하는 함수다

```Java
void func(...) {
    ...
    func(...);
    ...
}
```

다음 Recursion 코드를 실행하면 무한루프에 빠진다

```Java
public class Code01 {

    public static void main(String[] args) {
        func();
    }

    public static void func() {
        System.out.println("Hello...");
        func();
    }
}
```

### 무한루프에 빠지지 않으려면?
recursion을 무한 루프에 빠지지 않으려면 적절한 구조를 가져야 한다

```Java
public class Code02 {

    public static void main(String[] args) {
        int n = 4;
        func(n);
    }

    public static void func(int k) {
        if (k <= 0) {  // Base case: 적어도 하나의 recursion의 빠지지 않는 경우가 존재해야 한다
            return;
        }

        System.out.println("Hello...");
        func(k - 1); // Recursive case: recursion을 반복하다보면 결국 base case로 수렴해야 한다
    }
}
```

적절한 구조는 Base case와 Recursive case를 가져야 한다


### 1 ~ n까지의 합

다음 코드의 결과는 `1 ~ n`까지의 합을 구한다

```Java
public class Code03 {

    public static void main(String[] args) {
        int result = func(4);
        System.out.println(result);
    }

    public static int func(int n) {
        if (n == 0) {
            return 0;
        }

        return n + func(n - 1);
    }
}
```

순서는 다음과 같다
<img width="523" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/b4ed2f12-5c78-4f42-9d52-82560d827bd3">

#### recursion 해석
```Java
public static int func(int n) { // 이 함수의 mission은 0~n까지 합을 구하는 것이다

    if (n == 0) { // n = 0이라면 합은 0이다
        return 0;
    }

    return n + func(n - 1); // n이 0보다 크면 0에서 n까지의 합은 0에서 n-1까지의 합에 n을 더한 것이다
}
```

#### 수학적 귀납법
앞 코드를 수학적 귀납법으로 알아보자

정리: func(int n)은 음이 아닌 정수 n에 대해서 0에서 n까지의 합을 올바로 계산한다
증명:
1. n = 0인 경우 
    - n = 0인 경우 0을 반환한다. 올바르다
2. 임의의 양의 정수 k에 대해 `n < k`인 경우 0에서 n까지의 합을 올바르게 계산하여 반환한다고 가정한다
3. n = k인 경우를 고려한다
   - `func`은 먼저 `func(k-1)`을 호출한다
   - 2번의 가정에 의해서 0에서 k-1까지의 합이 올바로 계산되어 반환된다
   - 메소드 `func`은 그 값에 n을 더해서 반환한다
   - 따라서 메소드 func은 0에서 k까지의 합을 올바로 계산하여 반환한다


순환함수 만날 때 머릿속으로 위처럼 생각하자

### Factorial : n!

팩토리얼 정의

<img width="468" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/c33be1b9-fa08-4cbc-af26-0910a7351bcd"> 0


팩토리얼 정의를 이용해 다음처럼 코드를 만들 수 있다

```Java
int factorial(int n) {
    if (n == 0)
        return 1;
    else
        return n * factorial(n-1);
}
```

factorial(4)를 한다면 순서는 다음과 같다

<img width="547" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/70b882bf-d17d-4ee7-945d-895e25feedf3">



### x^n

x의 n승을 계산하는 함수도 본질적으로 순환함수다

<img width="343" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/305ad0f0-4a07-4bde-8544-6c5660227d33">

```Java
double power(double x, int n) {
    if (n == 0)
        return 1;
    else
        return x * power(x, n-1);
}
```

### Fibonacci Number

피보나치도 대표적인 순환의 예다

<img width="480" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/7c71e4a0-d7ac-4027-822c-e4825c23271f">

n 번째 피보나치 수도 구하는 순환 함수를 만들 수 있다

```Java
int fibonacci(int n) {
    if (n < 2)
        return n;
    else
        return fibonacci(n - 1) + finbonacci(n - 2);
}
```

### 최대 공약수: Euclid Method

최대 공약수를 구하는 함수도 순환으로 만들 수 있다

최대 공약수 정리는 다음과 같다
> m >= n인 두 양의 정수 m과 n에 대해서 m이 n의 배수이면 gcd(m, n) = n이다
> 그렇지 않으면 gcd(m, n) = gcd(n, m%n)이다

코드는 다음과 같다

```Java
double gcd(int m, int n) {
    if (m < n) {
        int tmp = m; m = n; n = tmp;    // swap m and n
    }
    
    if (m % n == 0)
        return n;
    else
        return gcd(n, m % n);    
}
```

#### Euclid Method: 좀 더 단순한 버전

위 최대 공약수 정리를 다듬으면 다음과 같아진다

<img width="585" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/74ecc356-a74d-4472-8f5f-1991a4383309">

> 두 양의 정수 p, q 중 하나가 0이라면(q = 0) 나머지 정수(p)가 최대 공약수다 
> 그렇지 않다면 q와 (p를 q로 나눈 나머지)의 최대 공약수와 같다
> 이 정리에서는 p가 반드시 q 보다 클 필요는 없다

위 정리의 코드는 다음과 같이 더 간단해진다

```Java
int gcd(int p, int q) {
    if (q == 0)
        return p;
    else
        return gcd(q, p % q);
}
```




