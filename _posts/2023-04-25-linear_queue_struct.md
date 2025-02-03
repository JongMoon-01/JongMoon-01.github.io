---
title: "선형 큐"
date: 2022-04-25 02:23:00 +0900
categories: [Data struct, Algorithm]
tags: [queue,stack,C]
---
# 05. 큐(Queue)
## 5.1. 큐 추상 데이터 타입(ADT)
- 객체 : 0개 이상의 요소들로 구성된 선형 리스트
```c
---
- 연산 :
    - create(max_size) :: = 최대 크기가 max_size인 공백 큐 생성.

    - init(q) ::= 큐 초기화.

    - is_empty(q) ::=
        if(size == 0) return TRUE;
        else return FALSE;
    
    - is_full(q) ::=
        if(size == max_size -1) return TRUE;
        else return FALSE;
    
    - enqueue(q, e) ::=
        if(is_full(q)) queue_full 오류;
        else q의 끝에 e를 추가한다.

    - dequeue(q) ::=
        if( is_empty(q) ) queue_empty 오류;
        else q의 맨 앞에있는 e를 제거하여 반환한다.

    - peek(q) ::=
        if( is_empty(q) ) queue_empty 오류;
        else q의 맨 앞에있는 e를 읽어서 반환한다.
---
```

### * 큐와 스택 차이

|  특징        | 스택      | 큐       |
|:------------|:----------|:---------|
| 순서        | 선입후출   | 선입선출  |
| 예시        | 쌓인 책    | 대기 행렬, 데이터패킷 모델링, 공항에서 이륙하는 비행기 등.. |
|구조|<img src="https://user-images.githubusercontent.com/56510688/234062625-6a21cd3d-3a86-4456-ad6e-692e2f3f25df.png" width="" height="400"/>|<img src="https://user-images.githubusercontent.com/56510688/234063246-0c782255-38b8-4ab0-904e-bf0710f490dd.png" width="450" height=""/>|

## 5.2. 선형 큐
----------------------
삽입과 삭제를 위한 변수 front와 rear그리고 1차원 배열을 선언해준다.
front는 큐의 첫번째 요소를 가르키고 rear는 큐의 마지막 요소를 가르킨다.
- front와 rear의 default value는 -1이다.
- 데이터가 삽입되면 rear를 하나 증가하고 rear+1위치에 데이터가 저장된다.
- 데이터가 삭제되면 front를 하나 증가하고 front+1위치에 데이터를 삭제한다.
<center>
<img src="https://user-images.githubusercontent.com/56510688/234067380-f56762b5-d96e-4b7b-bca0-b9b4ad5a2883.png" width="" height="750"/>
<p><b>[그림1]. 일차원 배열 이용한 큐의 구현</b></p>
</center>

## 5.2.1 선형 큐의 구현
--------------------
```c
---
#include <stdio.h>
#include <stdlib.h>
#define MAX_QUEUE_SIZE 5
typedef int element;
typedef struct {
	int front;
	int rear;
	element data[MAX_QUEUE_SIZE];
} QueueType;

void error(char* message) //에러 메세지 출력
{
	fprintf(stderr, "%s\n", message);
}
void init(QueueType* q) // 선형 큐 front, rear default값으로 초기화 함수
{
	q->front = -1;
	q->rear = -1;
}
int is_empty(QueueType* q) // 선형 큐가 비어있는지 확인하는 함수
{
	if (q->front == q->rear)
		return 1;
	else
		return 0;
}
int is_full(QueueType* q) // 선형 큐가 가득 차 있는지 확인하는 함수
{
	if (q->rear == MAX_QUEUE_SIZE - 1)
		return 1;
	else
		return 0;
}
void enqueue(QueueType* q, int val) // 선형 큐에 값을 집어넣는 함수
{
	if (!is_full(q))
	{
		q->rear++; // rear값 증가 후
		q->data[q->rear] = val; // rear위치에 값 대입
	}
	else
		error("QUEUE IS FULL\n");
}
element dequeue(QueueType* q) // 선형 큐에서 값을 제거하는 함수
{
	if (!is_empty(q))
	{
		int item;
		q->front++; // front값 증가 후
		item = q->data[q->front]; // front위치에 값 제거
		return item;
	}
	else
	{
		error("QUEUE IS EMPTY\n");
		return -1;
	}
}
void print_queue(QueueType* q)
{
	if (!is_empty(q))
	{
		for (int i = 0; i < MAX_QUEUE_SIZE; i++)
		{
			if (i<=q->front || i > q->rear) // i | front | rear |   i   |   i  |   i, i에 위치한 값들은 -1(EMPTY)로 출력
				printf("%2d | ", -1);
			else
				printf("%2d | ", q->data[i]); // front와 rear사이의 i값이 존재할 경우 값 출력
		}
	}
	else
	{
		error("QUEUE IS EMPTY\n");
	}
	printf("\n");
}
int main()
{
	int item = 0;
	QueueType q; // QueueType* q로 정의할 경우 포인터를 초기화 하여 사용해야하므로 참조 변수 정의

	init(&q);

	enqueue(&q, 10); print_queue(&q);
	enqueue(&q, 20); print_queue(&q);
	enqueue(&q, 30); print_queue(&q);

	dequeue(&q); print_queue(&q);
	dequeue(&q); print_queue(&q);

	return 0;
}
---
```
<center>
<b>[코드 1]. 선형 큐의 구현(C언어)</b>
</center>

---
```
10 | -1 | -1 | -1 | -1 |
10 | 20 | -1 | -1 | -1 |
10 | 20 | 30 | -1 | -1 |
-1 | 20 | 30 | -1 | -1 |
-1 | -1 | 30 | -1 | -1 |
```

<center>
<b>[결과 1]. 선형 큐의 구현(C언어)</b>
</center>

### 추가사항 
> 선형큐의 응용 작업 : 작업 스케줄링<br> -  만약 CPU가 하나뿐이고 OS에서 모든 작업들이 우선순위를 가지지 않는다고 가정하면 작업들은 운영체제에 들어간 순서대로(QUEUE) 처리된다.

> <b>선형 큐의 문제점</b> <br> - 삭제와 삽입을 반복할 경우 front와 rear값은 항상 증가하기 때문에 빈공간이 있어도 사용할 수 없다.