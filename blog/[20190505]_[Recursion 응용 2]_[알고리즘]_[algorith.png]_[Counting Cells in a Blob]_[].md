> - 해당 게시물은 인프런 - "영리한 프로그래밍을 위한 알고리즘 강좌" 강의를 참고하여 작성한 글 입니다
> - [강의 링크](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/dashboard)


## 문제

그림에서 특정 좌표의 `blob`의 크기를 구하는 문제다

![image](https://github.com/yanJuicy/blog/assets/43159295/e2c345cd-7c6f-498d-a92c-71cd3bf06167)

- Binary 이미지다
- 각 픽셀은 `image pixel` 또는 `background pixel`이다
- 서로 연결된 `image pixel`들을 `blob`이라고 부른다
- 상하좌우 및 대각 방향으로도 연결된 것으로 간주한다


위 그림에서는 총 4개의 `blob`이 있다

![image](https://github.com/yanJuicy/blog/assets/43159295/8db85028-fe3f-4043-8347-628618499515)


문제를 정의하면 다음과 같다
- 입력:
  - N x N 크기의 2차원 그리드
  - 하나의 좌표 (x, y)
- 출력:
  - 픽셀 (x, y)가 포함된 blob의 크기
  - (x, y)가 어떤 blob에도 속하지 않는 경우에는 0


## Recursive Thinking
이 문제를 Recursion을 이용해 해결해 본다

수도 코드는 다음과 같다

```Java
현재 픽셀이 image color가 아니라면
    0을 반환
현재 픽셀이 image color라면
    현재 픽셀을 카운트한다 (count = 1)
    현재 픽셀을 다른 색으로 칠한다 (중복 카운트 방지)
    현재 픽셀과 이웃한(상하좌우, 대각선) 모든 픽셀들에 대해서
        그 픽셀이 속한 blob의 크기를 계산하여 count에 더한다
    count 반환
```


## 순환적 알고리즘

![image](https://github.com/yanJuicy/blog/assets/43159295/806f290a-a711-4f4b-a769-ead6e562103a)

시작점은 (5, 3)이라고 가정, 즉 이 픽셀이 포함된 `blob`의 크기를 계산한다

현재 count = 0이다

![image](https://github.com/yanJuicy/blog/assets/43159295/d289d26f-b0a3-4cb2-ba03-62db2315a7a2)

현재 픽셀을 다른 색으로 칠하고 count를 1 증가한다

다른 색으로 칠하면 픽셀이 중복 count 되는 것을 방지한다

현재 count = 1이다

![image](https://github.com/yanJuicy/blog/assets/43159295/3776d3f7-745a-4768-85f1-2b8f290c48cc)

인접한 8개 픽셀에 대해서 순서대로 그 픽셀이 포함된 blob 크기를 구한다

순서는 북 -> 북동 -> 동 -> 동남 -> 남 -> 남서 -> 서 -> 북서 순서다

북쪽 픽셀이 포함된 blob의 크기는 0이다

count 값은 변함 없이 1이다

![image](https://github.com/yanJuicy/blog/assets/43159295/3dde5ff9-f364-4a68-a69c-908e798e1718)

북동쪽 픽셀이 속한 blob을 count하고, count된 픽셀들을 색칠한다

![image](https://github.com/yanJuicy/blog/assets/43159295/c4c99250-a614-44d6-b6f9-de64ff8bb407)

3개의 픽셀이 북동쪽 픽셀의 속한 blob이다

count 값은 `1 + 3 = 4`다

![image](https://github.com/yanJuicy/blog/assets/43159295/ccd5c28e-6e54-4ef0-aeff-8700583c55aa)

동쪽과 남동쪽 픽셀이 포함된 blob 크기는 0이다

count 값은 변함없이 4다

![image](https://github.com/yanJuicy/blog/assets/43159295/54fc8a1d-a143-4949-a975-5cf537d6b32e)

남쪽 픽셀이 속한 blob을 카운트 한다

![image](https://github.com/yanJuicy/blog/assets/43159295/f3c53f70-1958-4785-85e3-63e5825e9a71)

남쪽 픽셀이 속한 blob 크기는 9다

현재 count 값은 `4 + 9 = 13`이다

![image](https://github.com/yanJuicy/blog/assets/43159295/c5c737f8-e842-41e4-aa7c-63d2b1f3a4c5)

남서, 서 방향의 픽셀이 속한 blob은 없다

북서 방향의 픽셀은 이미 계산되었다

모든 방향 검사가 끝났다

현재 count 값은 변함없이 13이다


## Counting Cells in a Blob

알고리즘은 다음과 같다

```Java
if the pixel (x, y) is outside the grid
  the result is 0;
else if pixel (x, y) is not an image pixel or already counted
  the result is 0;
else
  set the color of the pixel (x, y) to a red color; // 이미 count 됨 표시
  the result is 1 plus the number of cells in each piece of 
    the blob that includes a nearest neighbor;
```

## 자바 코드

자바로 만든 코드는 다음과 같다

```Java
public class CountingCells {

    private static int BACKGROUND_COLOR = 0;
    private static int IMAGE_COLOR = 1;
    private static int ALREADY_COUNTED = 2;

    private static int N = 8;
    private static int[][] grid = {
            {1, 0, 0, 0, 0, 0, 0, 1},
            {0, 1, 1, 0, 0, 1, 0, 0},
            {1, 1, 0, 0, 1, 0, 1, 0},
            {0, 0, 0, 0, 0, 1, 0, 0},
            {0, 1, 0, 1, 0, 1, 0, 0},
            {0, 1, 0, 1, 0, 1, 0, 0},
            {1, 0, 0, 0, 1, 0, 0, 1},
            {0, 1, 1, 0, 0, 1, 1, 1},
    };

    public int countCells(int x, int y) {
        int result = 0;

        if (x < 0 || x >= N || y < 0 || y >= N) {
            result = 0;
        } else if (grid[x][y] != IMAGE_COLOR) {
            result = 0;
        } else {
            grid[x][y] = ALREADY_COUNTED;
            result = 1 +
                    countCells(x - 1, y + 1) +
                    countCells(x, y + 1) +
                    countCells(x + 1, y + 1) +
                    countCells(x - 1, y) +
                    countCells(x + 1, y) +
                    countCells(x - 1, y - 1) +
                    countCells(x, y - 1) +
                    countCells(x + 1, y - 1);
        }

        return result;
    }
    
    public static void main(String[] args) {
        int count = new CountingCells().countCells(5, 3);
        System.out.println("count = " + count);
    }
}
```


## 코드

https://github.com/yanJuicy/algorithm-for-clever-programming

