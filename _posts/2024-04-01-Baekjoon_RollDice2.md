---
title: "Roll Dice 2_23288번"
date: 2024-03-28 02:23:00 +0900
categories: [Algorithm]
tags: [CodingTest, C, Algorithm, Recursive]
---
# 1. 문제

<div id="problem_description" class="problem-text">
				<p>크기가 N×M인 지도가 존재한다. 지도의 오른쪽은 동쪽, 위쪽은 북쪽이다. 지도의 좌표는 (r, c)로 나타내며, r는 북쪽으로부터 떨어진 칸의 개수, c는 서쪽으로부터 떨어진 칸의 개수이다. 가장 왼쪽 위에 있는 칸의 좌표는 (1, 1)이고, 가장 오른쪽 아래에 있는 칸의 좌표는 (N, M)이다. 이 지도의 위에 주사위가 하나 놓여져 있으며, 주사위의 각 면에는 1보다 크거나 같고, 6보다 작거나 같은 정수가 하나씩 있다. 주사위 한 면의 크기와 지도 한 칸의 크기는 같고, 주사위의 전개도는 아래와 같다.</p>

<pre>  2
4 1 3
  5
  6</pre>

<p>주사위는 지도 위에 윗 면이 1이고, 동쪽을 바라보는 방향이 3인 상태로 놓여져 있으며, 놓여져 있는 곳의 좌표는 (1, 1) 이다. 지도의 각 칸에도 정수가 하나씩 있다. 가장 처음에 주사위의 이동 방향은 동쪽이다. 주사위의 이동 한 번은 다음과 같은 방식으로 이루어진다.</p>

<ol>
	<li>주사위가 이동 방향으로 한 칸 굴러간다. 만약, 이동 방향에 칸이 없다면, 이동 방향을 반대로 한 다음 한 칸 굴러간다.</li>
	<li>주사위가 도착한 칸 (x, y)에 대한 점수를 획득한다.</li>
	<li>주사위의 아랫면에 있는 정수 A와 주사위가 있는 칸 (x, y)에 있는 정수 B를 비교해 이동 방향을 결정한다.
	<ul>
		<li>A &gt;&nbsp;B인 경우 이동 방향을 90도 시계 방향으로 회전시킨다.</li>
		<li>A &lt; B인 경우 이동 방향을 90도 반시계 방향으로 회전시킨다.</li>
		<li>A = B인 경우 이동 방향에 변화는 없다.</li>
	</ul>
	</li>
</ol>

<p>칸 (x, y)에 대한 점수는 다음과 같이 구할 수 있다. (x, y)에 있는 정수를 B라고 했을때, (x, y)에서&nbsp;동서남북 방향으로 연속해서 이동할 수 있는 칸의 수 C를 모두 구한다. 이때 이동할 수 있는 칸에는 모두 정수 B가 있어야 한다. 여기서 점수는 B와 C를 곱한 값이다.</p>

<p>보드의 크기와 각 칸에 있는 정수, 주사위의 이동 횟수 K가 주어졌을때, 각 이동에서 획득하는 점수의 합을 구해보자.</p>

<p>이 문제의 예제 1부터 7은 같은 지도에서 이동하는 횟수만 증가시키는 방식으로 구성되어 있다. 예제 8은 같은 지도에서 이동하는 횟수를 매우 크게 만들었다.</p>

  </div>

## 입력

첫째 줄에 지도의 세로 크기 N, 가로 크기 M (2 ≤ N, M ≤ 20),&nbsp;그리고 이동하는 횟수 K (1 ≤ K ≤ 1,000)가 주어진다.&nbsp;

둘째 줄부터 N개의 줄에 지도에 쓰여 있는 수가 북쪽부터 남쪽으로, 각 줄은 서쪽부터 동쪽 순서대로 주어진다. 지도의 각 칸에 쓰여 있는 수는 10 미만의 자연수이다.

## 출력
첫째 줄에 각 이동에서 획득하는 점수의 합을 출력한다.


# 2. 문제 해석

- 오브젝트 : 주사위, 지도
1. 지도의 크기는 NxM이다. (2 ≤ N, M ≤ 20), 가장 왼쪽 위에 있는 칸의 좌표는 (1,1)이고 가장 오른쪽 아래에있는 좌표는 (N,M)이다.
2. 정육면체 주사위가 지도(1, 1)에 놓여져있으며 윗면이 1, 동쪽을 방향이 3인 상태이다. 주사위는 이동할때 정해진 방향으로 한칸 굴러간다.
3. 주사위는 다음과 같은 조건을 통해 이동 방향을 결정한다. <b>(A : 주사위 아랫면의 값, B : 지도[x,y]에 있는 정수 값)</b>
	- A > B인 경우 이동 방향을 90도 시계 방향으로 회전시킨다.
	- A < B인 경우 이동 방향을 90도 반시계 방향으로 회전시킨다.
	- A = B인 경우 이동 방향에 변화는 없다.
4. 점수 = B * C(인접한 칸이 B와 같은 값인 칸의 개수*B)
5. 주사위의 이동횟수는 K (1 ≤ K ≤ 1,000), 전개도는 처음 정해진 상태로 시작
6. 지도 B의 값의 범위는 10 미만의 자연수



# 3. 코드 구현

## 3.1. 주사위 구현

```c
typedef struct { //value : 전개도의 상태
	int value[4][3];
	int Xpos;
	int Ypos;
} DICE;
```

```c
void rollEast(DICE* d) {
	int temp = d->value[3][1];
	d->value[3][1] = d->value[1][2];
	d->value[1][2] = d->value[1][1];
	d->value[1][1] = d->value[1][0];
	d->value[1][0] = temp;
	d->Xpos++;

}
void rollWest(DICE* d) {
	int temp = d->value[3][1];
	d->value[3][1] = d->value[1][0];
	d->value[1][0] = d->value[1][1];
	d->value[1][1] = d->value[1][2];
	d->value[1][2] = temp;
	d->Xpos--;
}
void rollSouth(DICE* d) {
	int temp = d->value[3][1];
	d->value[3][1] = d->value[2][1];
	d->value[2][1] = d->value[1][1];
	d->value[1][1] = d->value[0][1];
	d->value[0][1] = temp;
	d->Ypos++;
}
void rollNorth(DICE* d) {
	int temp = d->value[0][1];
	d->value[0][1] = d->value[1][1];
	d->value[1][1] = d->value[2][1];
	d->value[2][1] = d->value[3][1];
	d->value[3][1] = temp;
	d->Ypos--;
}

int moveDICE(DICE* d, int v) {
	printf("(%d, %d)에서 %d방향으로 움직였습니다.\n", d->Xpos, d->Ypos, v);
	
	switch (v) {
	case 0:
		if (isValid(d->Ypos, d->Xpos + 1)) {
			rollEast(d);
			return 0;
		}
		else {
			rollWest(d);
			return 2;
		}
	case 2:
		if (isValid(d->Ypos, d->Xpos - 1)) {
			rollWest(d);
			return 2;
		}
		else {
			rollEast(d);
			return 0;
		}
	case 1:
		if (isValid(d->Ypos + 1, d->Xpos)) {
			rollSouth(d);
			return 1;
		}
		else {
			rollNorth(d);
			return 3;
		}
	case 3:
		if (isValid(d->Ypos - 1, d->Xpos)) {
			rollNorth(d);
			return 3;
		}
		else {
			rollSouth(d);
			return 1;
		}
		break;
	default:
		fprintf(stderr, "Error\n");
		break;
	}
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/8b8734ef-12da-4aa4-ba13-ae12b318cb58" width="720" height=""/>
<p><b>[그림1]. 주사위 움직임 구현</b></p>
</center>

## 3.2. 맵 구현

```c
#define R 21
#define C 21
#define MAX_N 21
#define MAX_M 21
typedef struct {
	Coordinate CRD[R][C];
} MAP;

int main() {
	for (int i = 0; i < R; i++) {
		for (int j = 0; j < C; j++) {
			m.CRD[i][j].check = 0;
			m.CRD[i][j].value = 0;
		}
	}
	scanf("%d %d %d", &N, &M, &movement); // 조건. 2<=N, M<=20
	for (int i = 1; i <= N; i++) { // 세로
		for (int j = 1; j <= M; j++) { // 가로
			scanf("%d", &m.CRD[i][j].value);
		}
	}
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/4d947b80-a450-4085-abb8-19622ded56a4" width="720" height=""/>
<p><b>[그림2]. 지도 구현</b></p>
</center>

## 3.3. 벡터 구현

추가로 Roll방향 함수를 불러올시 자동으로 그 방향으로 설정됩니다.

```c
if (d->value[3][1] > m.CRD[d->Ypos][d->Xpos].value)
			vector = (vector + 1) % MAX_VECTOR_SIZE; //Clockwise 90 degrees
		else if (d->value[3][1] < m.CRD[d->Ypos][d->Xpos].value)
			vector = (vector - 1 + MAX_VECTOR_SIZE) % MAX_VECTOR_SIZE; //Counter-Clockwise 90 degrees
		else
      vector = vector
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/65da4a00-ca43-44cb-b0f8-bf1c0d5eb1da" width="720" height=""/>
<p><b>[그림3]. 벡터 구현</b></p>
</center>

## 3.4. 점수 계산

### 3.4.1. 점수 계산 Call-by-value

재귀문으로 동서남북 4방향에 대해 조건을 확인합니다.

```c
int checkTile(MAP m, int x, int y, int value) {
	int total = 1;
	int dr[] = { -1,0,1,0 };
	int dc[] = { 0,1,0,-1 };
	m.CRD[y][x].check = 1;
	printf("checktile(Dice:%d) x: %d, y: %d\n",value,x,y);
	printf("MAP value : %d\n",m.CRD[y][x].value);
	for (int i = 0; i < 4; i++) {
		int newRow = y + dr[i];
		int newCol = x + dc[i];
		printf("newX : %d newY: %d MAP value : %d\n",newCol,newRow,m.CRD[newRow][newCol].value);
		printf("condition 1 : %d\n",isValid(newRow, newCol));
		printf("condition 2 : %d\n",(m.CRD[newRow][newCol].value == value));
		printf("condition 3 : %d\n",(m.CRD[newRow][newCol].check==0));
		if (isValid(newRow, newCol) && m.CRD[newRow][newCol].value == value && m.CRD[newRow][newCol].check == 0) {
			printf("checkTile\n");
			total += checkTile(m, newCol, newRow, value);
		}
	}
	return total;
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/c8c19b4a-2275-4b2e-855d-713792972c63" width="720" height=""/>
<p><b>[그림4]. 체크타일 함수</b></p>
</center>



### 3.4.2 Call-by-value 문제점

체크타일 함수는 MAP에 멤버 변수 중 check만 변경하고 다른 변수는 참조만 하는 형태이고 체크타일 함수를 Call 하기전 MAP에 m.CRD[r][c].check를 모두 초기화 시켜줘야해서 Call-by-value기법으로 넘겨주었다. 하지만 위와같이 여러 계층에서 값을 복사해 넘겨주게 되면서 정보가 누락되는 과정이 생겨 이미 체크한 타일을 반복체크하는 오류가 발생했다. 

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/ddc90667-be12-4082-9f7e-2fcf564f0b01" width="720" height=""/>
<p><b>[그림5]. 7번의 이동횟수를 가질 때 중복 체크</b></p>
</center>

### 3.4.3. Call-by-refer 변경

함수 한번 콜하면 check값을 다시 초기화 시켜줘야해서 메인에서 함수호출할때마다 초기화 함수도 같이 불러줬습니다.

```c

int checkTile(MAP* m, int x, int y, int value) {
	int total = 1;
	int dr[] = { -1,0,1,0 };
	int dc[] = { 0,1,0,-1 };
	m->CRD[y][x].check = 1;
	//printf("checktile(Dice:%d) x: %d, y: %d\n",value,x,y);
	//printf("MAP value : %d\n",m->CRD[y][x].value);
	for (int i = 0; i < 4; i++) {
		int newRow = y + dr[i];
		int newCol = x + dc[i];
		//printf("newX : %d newY: %d MAP value : %d\n",newCol,newRow,m->CRD[newRow][newCol].value);
		//printf("condition 1 : %d\n",isValid(newRow, newCol));
		//printf("condition 2 : %d\n",(m->CRD[newRow][newCol].value == value));
		//printf("condition 3 : %d\n",(m->CRD[newRow][newCol].check==0));
		if (isValid(newRow, newCol) && m->CRD[newRow][newCol].value == value && m->CRD[newRow][newCol].check == 0) {
			//printf("checkTile\n");
			total += checkTile(m, newCol, newRow, value);
		}
	}
	return total;
}
```

```c
for(int i = 0; i<R; i++)
{ 
	for (int j = 0; j < C; j++)
	{
		m.CRD[i][j].check = 0;
	}
}
```


## 4. 전체 소스코드

근데 결과는 틀렸습니다. 예제에 대해서도 모두 같은 출력을 내고 시간제한에 걸리지도 않는데 어디서 틀렸는지 모르겠네요.

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#define R 21
#define C 21
typedef struct {
	int value;
	int check;
} Coordinate;

typedef struct {
	Coordinate CRD[R][C];
} MAP;

typedef struct {
	int value[4][3];
	int Xpos;
	int Ypos;
} DICE;

int MAX_N = R;
int MAX_M = C;

int isValid(int row, int col) {
	return (row >= 1 && row < MAX_N && col >= 1 && col < MAX_M);
}

int checkTile(MAP* m, int x, int y, int value) {
	int total = 1;
	int dr[] = { -1,0,1,0 };
	int dc[] = { 0,1,0,-1 };
	m->CRD[y][x].check = 1;
	//printf("checktile(Dice:%d) x: %d, y: %d\n",value,x,y);
	//printf("MAP value : %d\n",m->CRD[y][x].value);
	for (int i = 0; i < 4; i++) {
		int newRow = y + dr[i];
		int newCol = x + dc[i];
		//printf("newX : %d newY: %d MAP value : %d\n",newCol,newRow,m->CRD[newRow][newCol].value);
		//printf("condition 1 : %d\n",isValid(newRow, newCol));
		//printf("condition 2 : %d\n",(m->CRD[newRow][newCol].value == value));
		//printf("condition 3 : %d\n",(m->CRD[newRow][newCol].check==0));
		if (isValid(newRow, newCol) && m->CRD[newRow][newCol].value == value && m->CRD[newRow][newCol].check == 0) {
			//printf("checkTile\n");
			total += checkTile(m, newCol, newRow, value);
		}
	}
	return total;
}

int generateRandom(int range1, int range2) {
	int random = 0;
	random = rand() % ((range2 - range1) + 1) + range1;
	return random;
}

void rollEast(DICE* d) {
	int temp = d->value[3][1];
	d->value[3][1] = d->value[1][2];
	d->value[1][2] = d->value[1][1];
	d->value[1][1] = d->value[1][0];
	d->value[1][0] = temp;
	d->Xpos++;

}
void rollWest(DICE* d) {
	int temp = d->value[3][1];
	d->value[3][1] = d->value[1][0];
	d->value[1][0] = d->value[1][1];
	d->value[1][1] = d->value[1][2];
	d->value[1][2] = temp;
	d->Xpos--;
}
void rollSouth(DICE* d) {
	int temp = d->value[3][1];
	d->value[3][1] = d->value[2][1];
	d->value[2][1] = d->value[1][1];
	d->value[1][1] = d->value[0][1];
	d->value[0][1] = temp;
	d->Ypos++;
}
void rollNorth(DICE* d) {
	int temp = d->value[0][1];
	d->value[0][1] = d->value[1][1];
	d->value[1][1] = d->value[2][1];
	d->value[2][1] = d->value[3][1];
	d->value[3][1] = temp;
	d->Ypos--;
}

int moveDICE(DICE* d, int v) {
	//printf("(%d, %d)에서 %d방향으로 움직였습니다.\n", d->Xpos, d->Ypos, v);
	
	switch (v) {
	case 0:
		if (isValid(d->Ypos, d->Xpos + 1)) {
			rollEast(d);
			return 0;
		}
		else {
			rollWest(d);
			return 2;
		}
	case 2:
		if (isValid(d->Ypos, d->Xpos - 1)) {
			rollWest(d);
			return 2;
		}
		else {
			rollEast(d);
			return 0;
		}
	case 1:
		if (isValid(d->Ypos + 1, d->Xpos)) {
			rollSouth(d);
			return 1;
		}
		else {
			rollNorth(d);
			return 3;
		}
	case 3:
		if (isValid(d->Ypos - 1, d->Xpos)) {
			rollNorth(d);
			return 3;
		}
		else {
			rollSouth(d);
			return 1;
		}
		break;
	default:
		fprintf(stderr, "Error\n");
		break;
	}

}

#define MAX_VECTOR_SIZE 4
int main() {

	clock_t start, end;
	double cpu_time_used;

	start = clock(); // 프로그램 실행 시작 시간 기록

	// Parameter Set phase
	int N, M; //N 세로, M 가로
	MAP m;
	DICE* d; // 조건. default Dice Set, 초기 위치 (1,1), 주사위의 밑면은 [3][1]
	d = (DICE*)malloc(sizeof(DICE));
	d->Ypos = 1;
	d->Xpos = 1;
	int unfolded_value[4][3] = { {0,2,0},{4,1,3},{0,5,0},{0,6,0} };
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 3; j++) {
			d->value[i][j] = unfolded_value[i][j];
		}
	}
	for (int i = 0; i < R; i++) {
		for (int j = 0; j < C; j++) {
			m.CRD[i][j].check = 0;
			m.CRD[i][j].value = 0;
		}
	}

	int movement = 0;
	int vector = 0; //동쪽 방향 
	int score = 0;
	// input phase
	scanf("%d %d %d", &N, &M, &movement); // 조건. 2<=N, M<=20
	for (int i = 1; i <= N; i++) { // 세로
		for (int j = 1; j <= M; j++) { // 가로
			scanf("%d", &m.CRD[i][j].value);
		}
	}
	MAX_N = N+1;
	MAX_M = M+1;
	// move phase
	moveDICE(d, vector);
	//printf("X : %d, Y : %d\n", d->Xpos, d->Ypos);
	score += checkTile(&m, d->Xpos, d->Ypos, m.CRD[d->Ypos][d->Xpos].value);
	//initialize m.CRD.check value
	for(int i = 0; i<R; i++)
	{ 
		for (int j = 0; j < C; j++)
		{
			m.CRD[i][j].check = 0;
		}
	}
	for (int k = 1; k < movement; k++) {
		//set vector
		if (d->value[3][1] > m.CRD[d->Ypos][d->Xpos].value)
			vector = (vector + 1) % MAX_VECTOR_SIZE; //Clockwise 90 degrees
		else if (d->value[3][1] < m.CRD[d->Ypos][d->Xpos].value)
			vector = (vector - 1 + MAX_VECTOR_SIZE) % MAX_VECTOR_SIZE; //Counter-Clockwise 90 degrees
		else
			vector = vector; // no change
		//move
		vector = moveDICE(d, vector);
		//Calculate Score
		score += checkTile(&m, d->Xpos, d->Ypos, m.CRD[d->Ypos][d->Xpos].value) * m.CRD[d->Ypos][d->Xpos].value;
		for (int i = 0; i < R; i++)
		{
			for (int j = 0; j < C; j++)
			{
				m.CRD[i][j].check = 0;
			}
		}
		//printf("%d번째 점수 : %d\n", k, score);
	}
	//printf("Vector : %d\n", vector);
	//printf("주사위 전개도\n%d\n%d %d %d\n%d\n%d\n", d->value[0][1], d->value[1][0], d->value[1][1], d->value[1][2], d->value[2][1], d->value[3][1]);
	printf("%d", score);
	end = clock(); // 프로그램 실행 종료 시간 기록

	cpu_time_used = ((double)(end - start)) / CLOCKS_PER_SEC; // 실행 시간 계산

	printf("프로그램 실행 시간: %f 초\n", cpu_time_used);

	return 0;
}
```