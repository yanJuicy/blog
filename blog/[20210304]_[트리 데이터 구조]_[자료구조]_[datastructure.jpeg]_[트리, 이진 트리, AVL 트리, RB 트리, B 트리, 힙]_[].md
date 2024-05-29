## 트리

트리 데이터 구조는 데이터를 계층으로 정렬한다

컴퓨터 과학 분야에서 트리는 뒤집혀서 맨 위에 뿌리가 있는 나무처럼 보인다

<img width="567" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/0e50b80d-5278-4e03-bc9f-8b6232e8f5d3">

맨 위에 해당하는 노드를 *루트 노드*라고 한다

트리의 나머지 요소는 루트 노드를 기준으로 구성한다

<img width="564" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/23a1f1fe-d61b-4fdc-ba51-1585ae1b57bc">

루트 노드에서 멀어지는 방향으로 다른 노드가 연결되면, 그 노드를 *하위 노드* 또는 *자식 노드*라고 한다

루트 노드를 향하는 방향으로 다른 노드가 연결 되면, 그 노드를 *상위 노드* 또는 *부모 노드*라고 한다

<img width="575" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/743d8584-8bb5-45d5-8698-8934cf86b9b6">


*리프 노드*는 더 이상 자식 노드를 갖지 않는 트리의 마지막 노드다

<img width="594" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/1d5cabf9-8b24-4ec5-a143-5adb6d6a47f6">


트리에서 노드를 연결하는 선을 *에지*라고 한다

노드 하나와 그 자식 노드들로 구성된 트리를 *서브트리*라고 한다

<img width="575" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/8fbf3837-b5da-40b4-b915-b53419997606">

노드에는 데이터를 저장하며, 저장된 데이터를 식별하는 데 사용되는 키와 저장된 데이터인 값을 포함할 수 있다


## 이진 트리(Binary Tree)

*이진 트리*는 가장 많이 사용되는 데이터 구조라고 할 수 있다

각 부모 노드가 항상 2개의 자식 노드와 연결되어 있어서 붙여진 이름이다

<img width="594" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/ce95f564-18e9-45cc-9dab-60bdf9444d5a">

이진 트리의  일반적인 유형은 *이진 탐색 트리*(binary search tree)다

이진 탐색 트리는 노드의 키를 기준으로 정렬한 트리다

이진 탐색 트리에서 모든 노드의 키는 왼쪽 서브트리보다 크고 오른쪽 서브트리보다 작다

<img width="601" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/00f8742a-a304-4005-9ca8-2ece996068e8">

이진 탐색 트리는 주로 세 가지 동작을 한다

1. 트리에 노드를 추가하는 동작
2. 트리에서 노드를 삭제하는 동작
3. 노드를 선택해 탐색하고자 하는 키가 존재하는지 확인하는 동작


## AVL 트리(Adeison-Velsky and Landis)

불균형 이진 트리가 있을 수 있다

이는 일반적으로 많은 노드가 단 하나의 자식 노드를 갖는 구조다

<img width="399" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/7ab8b64b-4385-4ed3-ac67-e93f6a60e83d">

이 문제를 해결하려면 트리의 균형을 조정해야 한다

트리의 균형을 조정하는 과정은 트리의 역할을 유지하고 가능한 한 최소 높이로 만드는 것이다

트리의 불균형을 해결하는 것은 메모리 관리를 위해 중요하다

<img width="392" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/18849abc-2533-4d80-a2b3-ec2c1225029e">

이렇게 균형을 맞춘 트리를 AVL 이진 트리라고 한다

서브트리 2개 사이에서 높이 차이를 감지하면 *트리 회전*이라는 균형 조정 과정을 수행한다


## RB 트리 (Red-Black)

*RB* 이진 탐색 트리는 자체적으로 균형을 조정한다

이 점에서 AVL 이진 탐색 트리와 비슷하고, 트리의 구조 때문에 균형을 조정하는 과정에서 트리 회전수가 적어 AVL 트리보다 효율적이다

RB 트리는 노드마다 red 또는 black으로 해석되는 비트를 포함한다

일반적으로 루트 노드는 black이고, red 노드는 black 노드를 자식 노드로 갖는다

<img width="616" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/8aa64cd7-9261-4561-916d-e1b00f6e7913">



## B 트리

*B 트리*는 데이터베이스를 설계할 때 사용하는 데이터 구조로 자체적인 균형 조정 기능을 갖춘 트리다

이진 탐색 트리와 달리 B 트리는 자식 노드 3개 이상을 갖는 부모 노드가 있다

<img width="647" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/06742005-8ac5-4997-a50a-0033ea76e30a">

많은 파일 시스템에서 데이터 계층 구조로 B 트리를 사용한다

파일 시스템에는 폴더가 있고, 노드의 키-값 구조를 통해 폴더 각각의 이름을 파일 시스템의 객체와 연결할 수 있다

그리고 폴더 각각에는 여러 파일이 들어 있는 다른 폴더가 존재할 수 있다


## 힙

*힙*은 트리 기반 데이터 구조이며 프로그래밍에서 자주 사용한다

힙은 이진 트리 데이터 구조의 한 종류다

값이 최대 혹은 최소인 노드에 빠르게 접근해야 하는 응용 프로그램에 적합하다

우선순위 큐는 힙을 사용해서 구현할 수 있다

힙의 구조를 설계하는 방법은 두 가지가 있다

1. 루트 노드가 힙에서 가장 큰 값이고 노드 각각의 값이 부모 노드의 값보다 작거나 같도록 구성된 힙을 *최대 힙*이라 한다

<img width="609" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/053515d8-4bbc-44a2-a500-215e7b52d9e5">

2. 루트 노드가 힙에서 가장 작은 값이고 노드 각각의 값이 부모 노드의 값보다 크거나 같다록 구성된 힙을 *최소 힙*이라 한다

<img width="609" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/d9e60e32-f1ba-4e86-85ae-761a49359903">

최소 힙이나 최대 힙을 적용한 응용 프로그램에서 이들의 성능을 대체할 수 있는 다른 데이터 구조는 없다

힙 데이터 구조는 *힙 메모리*와 전혀 다른 개념이다

힙 메모리는 프로그래머가 직접 관리해야 하는 메모리 영역이다

힙 메모리는 프로그래머가 작성한 코드에 따라 메모리 공간을 동적으로 할당하거나 해제하는 부분이다


