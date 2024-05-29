> - 해당 게시물은 인프런 - "영리한 프로그래밍을 위한 알고리즘 강좌" 강의를 참고하여 작성한 글 입니다
> - [강의 링크](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/dashboard)


## Maze 미로찾기

![image](https://github.com/yanJuicy/blog/assets/43159295/ae766b0b-dee6-455b-ae23-4012059bac61)

2차원 배열 입구(0,0)에서 출구(n-1,n-1)까지 찾아가는 문제

흰색 칸은 이동할 수 있는 통로, 파란색 칸은 이동할 수 없는 벽이다


## Recursive Thinking

순환적으로 생각해 문제를 해결한다

```Java
현재 위치에서 출구까지 가는 경로가 있으려면
   1) 현재 위치가 출구이거나 혹은
   2) 이웃한 셀들 중 하나에서 현재 위치를 지나지 않고 출구까지 가는 경로가 있거나
```

## Decision Problem
답이 yes or no인 문제로 recursion을 생각해본다

다음은 위 해결법의 수도코드다

```Java
boolean findPath(x, y)
   if (x, y) is the exit      // 현재 위치가 출구일 때
      return true
   else
      for each neighboring cell (x', y') of (x, y) do    // 이웃 셀 중에서 출구까지 경로 찾기
         if (x', y') is on the pathway
            if findPath(x', y')
               return true
      return false
```

![image](https://github.com/yanJuicy/blog/assets/43159295/308aed69-f3ab-4333-92b8-510cecf4ecc2)


### 무한 루프
Recursion을 생각할 때 무한 루프에 빠지는지 확인해야 한다

위 코드는 무한 루프에 빠질 가능성이 있다

(x', y')에서 이웃 셀로 이동할 때 다시 (x, y)로 이동할 수 있다

이렇게 두 셀을 무한히 반복해서 이동할 수 있다


## 무한 루프 개선
이미 가본 위치와 가지 않은 위치를 구분한다

```Java
boolean findPath(x, y)
   if (x, y) is the exit      // 현재 위치가 출구일 때
      return true
   else
      mark (x, y) as a visited cell    // 현재 위치 표시
      for each neighboring cell (x', y') of (x, y) do    // 이웃 셀 중에서 출구까지 경로 찾기
         if (x', y') is on the pathway and not visited // 조건 추가
            if findPath(x', y')
               return true
      return false
```

이렇게 하면 무한루프에 빠지지 않는다


## 무한 루프 개선 2

recursion을 호출한 후 맨 위에서 무한 루프를 방지하도록 할 수도 있다

코드를 더 간결하게 만들 수 있다

```Java
boolean findPath(x, y)
   if (x, y) is either on the wall or a visited cell     // 벽이거나 이미 방문한 셀
      return false
   else if (x, y) is the exit    // 출구일 경우
      return true
   else
      mark (x, y) as a visited cell    // 방문한 셀로 표시
      for each neighboring cell (x', y') of (x, y) do    // 인접한 셀에서 출구 찾기
         if findPath(x', y')
            return true
      return false
```

### 자바 코드

다음은 자바로 구현한 코드 예시다

```Java
package recursion;

public class Maze {

    private static final int PATHWAY_COLOR = 0;
    private static final int WALL_COLOR = 1;
    private static final int BLOCKED_COLOR = 2;
    private static final int PATH_COLOR = 3;

    private static int N = 8;
    private static int[][] maze = {
            {0, 0, 0, 0, 0, 0, 0, 1},
            {0, 1, 1, 0, 1, 1, 0, 1},
            {0, 0, 0, 1, 0, 0, 0, 1},
            {0, 1, 0, 0, 1, 1, 0, 0},
            {0, 1, 1, 1, 0, 0, 1, 1},
            {0, 1, 0, 0, 0, 1, 0, 1},
            {0, 0, 0, 1, 0, 0, 0, 1},
            {0, 1, 1, 0, 0, 1, 0, 0},
    };

    public static boolean findMazePath(int x, int y) {
        if (x < 0 || y < 0 || x >= N || y >= N) {
            return false;
        } else if (maze[x][y] != PATHWAY_COLOR) {
            return false;
        } else if (x == N - 1 && y == N - 1) {
            maze[x][y] = PATH_COLOR;
            return true;
        } else {
            maze[x][y] = PATH_COLOR;
            if (findMazePath(x - 1, y) || findMazePath(x + 1, y) ||
                    findMazePath(x, y - 1) || findMazePath(x, y + 1)) {
                return true;
            }
            maze[x][y] = BLOCKED_COLOR;
            return false;
        }
    }

    public static void printMaze() {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                System.out.print(maze[i][j]);
            }
            System.out.println();
        }
        System.out.println();
    }

    public static void main(String[] args) {
        printMaze();
        findMazePath(0, 0);
        printMaze();
    }

}
```

