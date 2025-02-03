---
title: "삼각형과 세 변  5073번"
date: 2024-03-28 02:23:00 +0900
categories: [Algorithm]
tags: [CodingTest, C, Algorithm, mathmatics]
---
# 1. 문제

삼각형의 세 변의 길이가 주어질 때 변의 길이에 따라 다음과 같이 정의한다.

- Equilateral(정삼각형) : &nbsp;세 변의 길이가 모두 같은 경우
- Isosceles(이등변삼각형) : 두 변의 길이만 같은 경우
- Scalene(일반삼각형) : 세 변의 길이가 모두 다른 경우

단 주어진 세 변의 길이가 삼각형의 조건을 만족하지 못하는 경우에는 "Invalid" 를 출력한다. 예를 들어 6, 3, 2가 이 경우에 해당한다. 가장 긴 변의 길이보다 나머지 두 변의 길이의 합이 길지 않으면 삼각형의 조건을 만족하지 못한다.

세 변의 길이가 주어질 때 위 정의에 따른 결과를 출력하시오.


## 입력

각 줄에는 1,000을 넘지 않는 양의 정수 3개가 입력된다. 마지막 줄은 0 0 0이며 이 줄은 계산하지 않는다.

```
7 7 7
6 5 4
3 2 5
6 2 6
0 0 0
```

## 출력
각 입력에 맞는 결과 (Equilateral, Isosceles, Scalene, Invalid) 를 출력하시오.

```
Equilateral
Scalene
Invalid
Isosceles
```


# 2. 문제 해석

- 오브젝트 : 삼각형
조건
1. a = b = c -> 정삼각형
2. a = b || b = c || a = c-> 이등변 삼각형
3. a != b && b != c && isValid = 1 -> 일반 삼각형
4. 0 0 0입력받으면 종료

 - 접근 아이디어

 우선 일반삼각형 <- 정삼각형 <- 이등변 삼각형의 계층 구조를 이루고 있어 상으로 조건 탐사를 진행할지 하로 조건 탐사를 진행할지 고민했다, 왜냐면 이런 계층구조를 띄고 있을때 순서대로 조건탐사를 진행하면 생략되는 부분이 있을 때가 있다(집합론 xRy^yRz 추이성 개념). 결론은 일반 삼각형이 가장 긴 조건을 포함하기 때문에 상으로 조건 탐사를 진행하기로 하였다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/0362e744-c2d1-4230-a4ea-599dba2ecb4a" width="" height=""/>
<p><b>[그림1]. 삼각형 관계 </b></p>
</center>

## 4. 전체 소스코드

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

typedef struct {
	int line[3];
} TRI;

void swap(int* a, int* b) {
	int temp = *a;
	*a = *b;
	*b = temp;
}

void selectionSort(TRI* t, int n) {
	int i, j, min_index;

	for (i = 0; i < n - 1; i++) {
		min_index = i;
		for (j = i + 1; j < n; j++) {
			if (t->line[j] < t->line[min_index]) {
				min_index = j;
			}
		}
		swap(&t->line[min_index], &t->line[i]);
	}
}

int isValid(TRI tri)
{
	if (tri.line[2] < tri.line[1] + tri.line[0])
		return 1;
	else
		return 0;
}

void checkTriangle(TRI tri)
{
	if (tri.line[0] == tri.line[1] || tri.line[0] == tri.line[2] || tri.line[1] == tri.line[2])
		printf("Isosceles\n");
	else if (tri.line[0] == tri.line[1] && tri.line[1] == tri.line[2])
		printf("Equilateral\n");
	else
		printf("Scalene\n");
}

int main()
{
	//init phase
	TRI t = { 0,0,0 };
	//input phase
	do
	{
		scanf("%d %d %d", &t.line[0], &t.line[1], &t.line[2]);
		if (t.line[0] + t.line[1] + t.line[2] == 0)
			break;

		selectionSort(&t, 3);
		
		if (isValid(t) == 1)
			checkTriangle(t);
		else
			printf("Invalid\n");
		
	} while (1);
	return 0;
}
```