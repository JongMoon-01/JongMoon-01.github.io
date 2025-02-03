---
title: "Tetromino 14500번"
date: 2024-04-25 02:23:00 +0900
categories: [Algorithm]
tags: [CodingTest, Algorithm, Graph, Tetris]
---
# 1. 문제

폴리오미노란 크기가 1×1인 정사각형을 여러 개 이어서 붙인 도형이며, 다음과 같은 조건을 만족해야 한다.

- 정사각형은 서로 겹치면 안 된다.
- 도형은 모두 연결되어 있어야 한다.
- 정사각형의 변끼리 연결되어 있어야 한다. 즉, 꼭짓점과 꼭짓점만 맞닿아 있으면 안 된다.

정사각형 4개를 이어 붙인 폴리오미노는 테트로미노라고 하며, 다음과 같은 5가지가 있다.

<center>
<img src="https://upload.wikimedia.org/wikipedia/commons/5/50/All_5_free_tetrominoes.svg" width="720" height=""/>
<p><b>[그림1]. 5가지 테트로미노 </b></p>
</center>

아름이는 크기가 N×M인 종이 위에 테트로미노 하나를 놓으려고 한다. 종이는 1×1 크기의 칸으로 나누어져 있으며, 각각의 칸에는 정수가 하나 쓰여 있다.

테트로미노 하나를 적절히 놓아서 테트로미노가 놓인 칸에 쓰여 있는 수들의 합을 최대로 하는 프로그램을 작성하시오.

테트로미노는 반드시 한 정사각형이 정확히 하나의 칸을 포함하도록 놓아야 하며, 회전이나 대칭을 시켜도 된다.

## 1.1. 입력

첫째 줄에 종이의 세로 크기 N과 가로 크기 M이 주어진다. (4 ≤ N, M ≤ 500)

둘째 줄부터 N개의 줄에 종이에 쓰여 있는 수가 주어진다. i번째 줄의 j번째 수는 위에서부터 i번째 칸, 왼쪽에서부터 j번째 칸에 쓰여 있는 수이다. 입력으로 주어지는 수는 1,000을 넘지 않는 자연수이다.

## 1.2. 출력

첫째 줄에 테트로미노가 놓인 칸에 쓰인 수들의 합의 최댓값을 출력한다.

# 2. 문제 해석

오브젝트 : Tetromino 5가지, Board(NxM)

조건
- 정사각형은 서로 겹치면 안된다.
- 도형은 모두 연결되어 있어야 한다.
- 정사각형의 변끼리 연결되어 있어야 한다.
- 폴리오미노는 총 5가지 종류
- 폴리오미노의 배치는 Rotate와 Decalcomanie가 가능하다.
- 종이(Board)는 N x M 크기를 가진다.
- 종이의 한칸 크기는 1x1 즉, 폴리오미노의 단위 크기이다.
- 종이의 한칸에는 정수가 하나 쓰여있다.
- 정수는 1~1000의 범위를 가진다.
- 2번째 줄의 5번째 수는 위에서부터 2번째 칸, 왼쪽에서부터 5번째 칸의 수이다. 
- (4 ≤ N, M ≤ 500)

# 3. 문제 해결 방안
## 3.1. Board 추상화
테트로미노는 모두 연결되어 있는 도형이다. 즉 배열을 Graph로 만들고 RL탐색을 통해 구현해볼 수 있을 것이다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/59009ea2-86c4-436e-8ade-c08291717951" width="720" height=""/>
<p><b>[그림2]. Board 추상화 </b></p>
</center>

## 3.2. 테트로미노 구현

테트로미노는 블럭마다 정해진 순서의 Node 탐사를 진행한다. 원래는 Down Left, Down Right, Up Left, Up Right다 구현하려고 했는데 아래 아이디어 떠올라서 Down Left를 L, Down Right를 R로 함수를 생성해서 진행할 것이다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/47e8a94f-4ad0-421a-973c-b3f8bfdef1a4" width="720" height=""/>
<p><b>[그림3]. 테트로미노 </b></p>
</center>

## 3.3. 테트로미노 Rotate와 Decalcomanie 구현

조건 중에서 폴리오미노는 Decalcomanie와 Rotate가 가능한데, 나는 귀찮으니까 Board를 90도씩 회전하는 아이디어를 사용할 것이다. 그러면 시계 방향으로 90도 회전한 Board에서는 Block도 90도 Rotate한 효과를 발휘할 것이고 180도 회전한 Board에서는 Decalcomanie가 된 효과를 발휘할 것이다.

* 추가 아이디어로 보드 회전 시 정 중앙값은 고정되므로 해당 값을 기준으로 회전하려 했는데 이거는 정방행렬일때만 가능한 아이디어이므로 기각했었는데, 어차피 Board 초기화 시 500, 500으로 초기화하기 때문에 (250, 250) 좌표를 기준으로 잡아 돌리기로 결정했습니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/c588e71d-42ee-443d-9e47-175bc57dd3b0" width="720" height=""/>
<p><b>[그림4]. Rotating Board </b></p>
</center>

근데 500, 500을 기준으로 반복문 돌려서 90도 구현하려면 보드 크기때문에 잡아먹는 메모리양도 때문인지 292x292 초과하면 먹통이 되더군요

```c
#include <stdio.h>
#include <stdlib.h>
#define ROWS 292
#define COLS 292

// 그래프의 노드 구조체
typedef struct Node {
    int data;
    struct Node* right;
    struct Node* down;
} Node;

// 그래프의 노드 생성 함수
Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = data;
    newNode->right = NULL;
    newNode->down = NULL;
    return newNode;
}

// 배열을 그래프로 변환하는 함수
Node* arrayToGraph(int arr[][COLS]) {
    Node* graph[ROWS][COLS];

    // 각 배열 요소를 그래프 노드로 변환
    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            graph[i][j] = createNode(arr[i][j]);
        }
    }

    // 이웃한 노드들 간의 연결 설정
    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            if (i < ROWS - 1)
                graph[i][j]->down = graph[i + 1][j];
            if (j < COLS - 1)
                graph[i][j]->right = graph[i][j + 1];
        }
    }

    // 그래프의 시작 노드 반환
    return graph[0][0];
}

Node* rotateGraph(Node* graph) {
    Node* newGraph[COLS][ROWS];

    // 새로운 그래프 생성
    for (int i = 0; i < COLS; i++) {
        for (int j = 0; j < ROWS; j++) {
            newGraph[i][j] = createNode(0); // 초기화
        }
    }

    // 그래프의 노드를 회전하여 새로운 그래프에 설정
    Node* currentNode = graph;
    for (int i = 0; i < ROWS; i++) {
        Node* temp = currentNode; // 현재 행의 시작 노드를 임시로 저장
        for (int j = 0; j < COLS; j++) {
            newGraph[j][ROWS - 1 - i]->data = temp->data;
            temp = temp->down; // 다음 행으로 이동
        }
        currentNode = currentNode->right; // 다음 열의 첫 번째 노드로 이동
    }

    // 이웃한 노드들 간의 연결 설정
    for (int i = 0; i < COLS; i++) {
        for (int j = 0; j < ROWS; j++) {
            if (i < COLS - 1)
                newGraph[i][j]->right = newGraph[i + 1][j];
            if (j < ROWS - 1)
                newGraph[i][j]->down = newGraph[i][j + 1];
        }
    }

    // 새로운 그래프의 시작 노드 반환
    return newGraph[0][0];
}

// 그래프를 배열로 변환하여 출력하는 함수
void printGraph(Node* graph) {
    Node* currentRow = graph;

    while (currentRow != NULL) {
        Node* currentNode = currentRow;
        while (currentNode != NULL) {
            printf("%d ", currentNode->data);
            currentNode = currentNode->right;
        }
        printf("\n");
        currentRow = currentRow->down;
    }
}

int main() {
    // 예시로 5x5 배열 생성
    int arr[ROWS][COLS] = {
        {1, 2, 3, 4, 5},
        {6, 7, 8, 9, 10},
        {11, 12, 13, 14, 15},
        {16, 17, 18, 19, 20},
        {21, 22, 23, 24, 25}
    };

    // 배열을 그래프로 변환
    Node* graph = arrayToGraph(arr);

    printf("Original graph:\n");
    printGraph(graph);

    // 그래프를 90도 회전
    Node* rotatedGraph = rotateGraph(graph);

    printf("\nRotated graph:\n");
    printGraph(rotatedGraph);

    return 0;
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/ce12a6ac-5fe2-463d-9f7f-49b952db2932" width="720" height=""/>
<p><b>[그림5]. Rotating Board </b></p>
</center>

## 3.4. 시간복잡도를 위한 최적화

만약 보드를 회전시키면서 한배열씩 블럭을 두고 해당 값이 최대값인지 확인하는 알고리즘을 사용할 시 시간복잡도는 최악일 경우O(N^2)이 될 가능성이 크다.
그래서 나는 단위 칸당 가지고 있는 정수의 최대 최소 평균값 이상의 칸에 대해서만 블럭을 놓는 최적화를 진행할 것이다.

- ex. 보드에 정수가 1~5의 범위를 가지고 있을 시 3~5에 해당하는 칸에만 블럭 배치 시도

#### 1번케이스

```
시간복잡도 = N*M*5*5
N*M은 보드의 크기
5는 Block Type이 5개므로 상수
다른 5는 Board, CW90Board, RCW90Board, CW180Board, RCW180Board 총 5가지 Board Type이 있으므로 상수
```

#### 2번케이스

```
시간복잡도 = C*5*5
C는 평균값 이상의 칸의 갯수
5는 Block Type이 5개므로 상수
다른 5는 Board, CW90Board, RCW90Board, CW180Board, RCW180Board 총 5가지 Board Type이 있으므로 상수
```

# 4. 소스코드

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#define ROWS 5
#define COLS 5
//(4 ≤ N, M ≤ 500)
#define N 500
#define M 500
#define TETROTYPE 5
int n = 0, m = 0;


typedef struct {
    int value;
    int isUpperThanAver;
}TILE;

typedef struct{
	TILE tile[N][M];
}BOARD;

BOARD* B;

void initBoard()
{
    for (int i = 0; i < N; i++)
    {
        for (int j = 0; j < M; j++)
        {
            B->tile[j][i].isUpperThanAver = 0;
            B->tile[j][i].value = 0;
        }
    }
}

void reverse(int block[])
{
    for (int i=0; i < 6; i++)
        if (block[i] == 1)
            block[i] = 0;
        else
            block[i] = 1;
}

int setBlock(int block[], int x, int y)
{
    int sum;
    int max = 0;
    int tmpX;
    int tmpY;
    for (int i = 0; i < 2; i++) // Left, Right (Decalcomaine)
    {
        sum = 0;
        //printf("시작 위치 X: %d, Y: %d\n", x, y);
        tmpX = x;
        tmpY = y;
        //for (int j = 0; j < 4; j++) // 0degree, 90degree, 180degree, 270degree
        //{
        //sum += B->tile[tmpX][tmpY].value;
        for (int k = 0; k < 3; k++)
        {
            if (block[i] == 1 && block[i+3] == 1 && tmpX+1 < m && tmpY+1 < n)
            {
                sum += B->tile[tmpX+1][tmpY].value;
                printf("X: %d, Y: %d 좌표, %d을 더했습니다.\n", tmpX, tmpY, B->tile[tmpX+1][tmpY].value);
                sum += B->tile[tmpX][tmpY+1].value;
                printf("X: %d, Y: %d 좌표, %d을 더했습니다.\n", tmpX, tmpY, B->tile[tmpX][tmpY+1].value);
                tmpX++;
                tmpY++;
            }
            else
            {
                if (block[i] == 1 && tmpY+1 < n)
                {
                    sum += B->tile[tmpX][tmpY++].value;
                    printf("X: %d, Y: %d 좌표, %d을 더했습니다.\n",tmpX, tmpY, B->tile[tmpX][tmpY].value);
                }
                if (block[i + 3] == 1 && tmpX+1 < m)
                {
                    sum += B->tile[tmpX++][tmpY].value;
                    printf("X: %d, Y: %d 좌표, %d을 더했습니다.\n", tmpX, tmpY, B->tile[tmpX][tmpY].value);
                }
            }
            
        }
        if (sum > max)
            max = sum;
        //rotate(*block);
        //}
        reverse(block);
    }
    return max;
}

int searchBoard(int blocks[][TETROTYPE])
{
    int xPos;
    int yPos;
    int max = 0;
    int result;
    for (int i = 0; i < n; i++) // 세로 행 반복
    {
        for (int j = 0; j < m; j++) // 가로 열 반복
        {
            if (B->tile[j][i].isUpperThanAver == 1)
            {
                xPos = j;
                yPos = i;
                for (int k = 0; k < 5; k++) // TETROTYPE 5가지
                {
                    result = setBlock(blocks[k],xPos,yPos);
                }
                if (result > max)
                    max = result;
            }
            
        }
    }
    return max;
}

int main()
{
    //init phase
    B = (BOARD*)malloc(sizeof(BOARD));
    int TETRO[TETROTYPE][6] = 
                      { {1,1,1,0,0,0}, //3 index 기준으로 왼쪽은 down, 오른쪽은 right
                        {1,1,0,1,0,0},
                        {1,1,0,0,0,1},
                        {1,0,1,0,1,0},
                        {0,1,0,1,1,0}, };
    void initBoard();
    int minValue = 0, maxValue = 0; // tile의 최소, 최대값
    int max = 0; //블럭이 놓인 tile value의 최대값
    //input phase
	scanf("%d %d", &n, &m);
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			scanf("%d", &B->tile[j][i].value);
            // 최소값과 최대값 갱신
            if (i == 0 && j == 0) // 첫 번째 값으로 초기화
            { 
                minValue = maxValue = B->tile[j][i].value;
            }
            else 
            {
                if (B->tile[j][i].value < minValue)
                    minValue = B->tile[j][i].value;
                if (B->tile[j][i].value > maxValue)
                    maxValue = B->tile[j][i].value;
            }
		}
	}
    int averageValue = (maxValue + minValue) / 2;
    //평균값보다 높은 정수 타일에 표시
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            if (B->tile[j][i].value > averageValue)
                B->tile[j][i].isUpperThanAver = 1;
        }
    }
    //Compare phase
    max = searchBoard(TETRO);
    printf("%d", max);

	free(B);
	return 0;
}

//문제 왜인지 모르겠으나 1,1을 두 번가진 TETROTYPE이 존재함.
//rotate만 구현하면 끝
```