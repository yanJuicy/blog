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


### 예제 1. 1 ~ n까지의 합

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

### 예제 2. Factorial : n!

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



### 예제 3. x^n

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

### 예제 4. Fibonacci Number

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

### 예제 5. 최대 공약수: Euclid Method

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


## Recursion은 수함 함수 계산에만 유용한가?

수함 함수뿐만 아니라 다른 많은 문제들을 recursion으로 해결할 수 있다


### 예제 1. 문자열의 길이 계산

문자열의 길이를 구할 때 순환 함수를 이용할 수 있다

<img width="585" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/02879276-365e-4c5f-b284-7433a972c1ea">

순환 코드는 다음과 같다

```Java
int length(String str) {
   if (str.equals(""))  // base case
      return 0;
   else
      return 1 + length(str.substring(1));
}
```

아래와 같은 순서로 문자열의 길이를 구한다

<img width="530" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/45041625-e126-4af0-949c-f88b5ade465b">


### 예제 2. 문자열의 프린트

문자열을 프린트하는 함수를 순환 함수로 만들어 보자

```Java
void printChars(String str) {
   if (str.length() == 0)
      return;
   else {
      System.out.println(str.charAt(0));  // 첫 문자 출력
      printChars(str.substring(1));    // 나머지 문자 출력
   }
}
```


### 예제 3. 문자열을 뒤집어 프린트

앞 예제와 반대되는 예제다

<img width="865" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/17db8ad1-4e2d-43a0-8140-02c6c9eddf14">

```Java
void printCharsReverse(String str) {
   if (str.length() == 0)
      return;
   else {
      printChars(str.substring(1));    // 첫 문자를 제거한 나머지 문자열 출력
      System.out.println(str.charAt(0));  // 첫 문자 출력
   }
}
```


### 예제 4. 2진수로 변환하여 출력

음이 아닌 정수 n을 이진수로 변환해 프린트하는 예제다

```Java
void printInBinary(int n) {
   if (n < 2)  // 0, 1은 그대로 출력
      System.out.print(n);
   else {
      printInBinary(n / 2);      // n을 2로 나눈 몫을 먼저 2진수로 변환후 출력
      System.out.print(n % 2);   // n을 2로 나눈 나머지를 출력
   }
}
```


### 예제 5. 배열의 합 구하기

data[0]에서 data[n - 1]까지의 합을 구하는 예제다

```Java
int sum(int n, int[] data) {
   if (n <= 0)
      return 0;
   else
      return sum(n - 1, data) + data[n - 1];
}
```

처음 n - 1개까지 합을 구한 후 나머지 데이터를 더한다


## Recursion vs Iteration

모든 순환 함수(Recursion)는 반복문(iteration)으로 변경 가능하다

그 역도 성립한다. 즉 모든 반복문은 순환 함수로 표현 가능하다

순환 함수는 복잡한 알고리즘을 단순하고 알기쉽게 표현하는 것을 가능하게 한다

하지만 함수 호출에 따른 오버헤드가 있어 성능에서 손해를 볼 수 있다


## 순환 알고리즘 설계

### 순환 알고리즘의 조건

- 적어도 하나의 base case, 즉 순환되지 않고 종료되는 case가 있어야 한다
- 모든 case는 결국 base case로 수렴해야 한다

순환 알고리즘을 설계할 때는 암시적(implicit) 매개변수를 명시적(explicit) 매개변수로 바꾸는게 좋다

### 순차 탐색

data[0]에서 data[n-1] 사이에서 target을 검색하는 함수다

검색 구간의 시작 인덱스 0은 보통 생략한다

시작 인덱스 0은 암시적 매개변수다

```Java
int search(int[] data, int n, int target) {
   for (int i = 0; i < n; i++) {
      if (data[i] == target) {
         return i;
      }
   }
   
   return -1;
}
```

### 예제 1. 순차 탐색: 매개변수의 명시화

위 순차 탐색 코드를 순환 함수로 작성하면 다음과 같다

```Java
int serach(int[] data, int begin, int end, int target) {
   if (begin > end) 
      return -1;
   else if (target == data[begin])
      return begin;
   else
      return search(data, begin + 1, end, target);
}
```

이 함수는 data[begin]에서 data[end] 사이에서 target을 검색한다

이전 함수와는 다르게 시작점 begin을 명시적으로 지정한다

일반적으로 순환 함수는 맨 처음에 호출될 때만 생각하고 매개변수를 설계해서는 안된다

순환 함수가 다시 자기 자신을 호출할 때도 생각해서 매개변수를 설계해야한다

그래서 begin 매개변수를 명시적으로 표현한다


#### 순차 탐색: 다른 버전

이번엔 반대로 뒤에서 앞으로 탐색을 진행하는 예제다

```Java
int serach(int[] data, int begin, int end, int target) {
   if (begin > end) 
      return -1;
   else if (target == data[end])
      return end;
   else
      return search(data, begin, end - 1, target);
}
```

또 다른 방법으로 배열의 가운데를 기준으로 양 옆으로 순차 탐색을 진행할 수 있다

```Java
int search(int[] data, int begin, int end, int target) {
   if (begin > end) 
      return -1;
   else {
      int middle = (begin + end) / 2;
      if (data[middle] == target) 
         return middle;
      int index = search(data, begin, middle - 1, target);
      if (index != -1) 
         return index;
      else
         return search(data, middle + 1, end, target);
   }
}
```


### 예제 2. 최대값 찾기: 매개변수의 명시화

다음은 배열의 최대값을 찾는 순환 함수다

```Java
int findMax(int[] data, int begin, int end) {
   if (begin == end)
      return data[begin];
   else
      return Math.max(data[begin], findMax(data, begin + 1, end));
}
```

이 함수는 data[begin]에서 data[end] 사이에서 최대값을 찾아 반환한다

recursion을 호출할 때 base인 begin 값이 계속 바뀌기 때문에 따로 명시적으로 매개변수화 하는게 좋다


#### 최대값 찾기: 다른 버전

가운데를 기준으로 양 옆으로 최대값을 찾는 코드다

```Java
int findMax(int[] data, int begin, int end) {
   if (begin == end)
      return data[begin];
   else {
      int middle = (begin + end) / 2;
      int max1 = findMax(data, begin, middle);
      int max2 = findMax(data, middle + 1, end);
      return Math.max(max1, max2);
   }
}
```

## 코드

https://github.com/yanJuicy/algorithm-for-clever-programming

