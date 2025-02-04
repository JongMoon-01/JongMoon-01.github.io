---
title: "원형 큐"
date: 2022-04-26 01:23:00 +0900
categories: [Algorithm]
tags: [queue,stack,C]
---
# 5.3. 원형 큐
선형 큐는 이해하기 쉽지만 한계가 존재한다. 선형 큐에서는 값의 삽입과 삭제가 반복될 경우 front와 rear값은 감소하지 않아 큐의 빈공간이 많아도 QUEUE IS FULL이라는 에러를 발생시킬 것이다. 이를 해결하기 위해 [그림 1]과 같은 방식으로 해결할 수 있지만, 코드가 복잡해진다.

<center>
<img src="https://user-images.githubusercontent.com/56510688/234330329-a4c8eb2e-7e94-4063-83d6-bb515830952f.png" width="600" height=""/>
<p><b>[그림 1]. 선형 큐의 이동 </b></p>
</center>

이를 쉽게 해결할 수 있는 방법이 또 하나 있는데, 바로 배열을 <b>선형</b>으로 생각하지 않고 <b>원형</b>으로 생각하는 것이다.
즉 front와 rear값이 배열의 끝인 MAX_QUEUE_SIZE - 1에 도달할 경우 다음 front와 rear가 가지는 값이 0이 되면 된다.

<center>
<img src="https://user-images.githubusercontent.com/56510688/234331751-f08b802e-114f-4879-89df-ceb6e17da1a0.png" width="600" height=""/>
<p><b>[그림 2]. 원형 큐의 개념 </b></p>


</center>

| 특징        | 선형 큐   | 원형 큐   |
|:----------------------|:---------------------------------------------------|:-------------------------------------------------|
| 초기 값               | front, rear = -1                                                             | front, rear = 0                                                              |
| 변수가 가르키는 위치   | front : 큐의 첫번째 요소 앞 <br> rear : 마지막요소                             | front : 큐의 첫번째 요소 앞 <br> rear : 마지막요소                              |
| 작동 방식             | enqueue : ++rear 후 그 위치에 값 대입 <br> dequeue : ++front 후 그 위치 값 삭제 | enqueue : ++rear 후 그 위치에 값 대입 <br> dequeue : ++front 후 그 위치 값 삭제 | 

<center>
<img src="https://user-images.githubusercontent.com/56510688/234333911-4c470dbf-70ed-4652-a5c0-f1856189f789.png" width="600" height=""/>
<p><b>[그림 3]. 원형 큐의 상태 </b></p>


</center>

| 변수        | 공백 상태  | 포화 상태 | 오류 상태    |
|:------------|:----------|:---------|:------------ |
| 초기 값      | front == -1 && rear == -1   | (rear + 1)%MAX_SIZE_QUEUE == front  | front == rear |

> 원형 큐에서는 항상 한자리를 비워둔다. 왜냐면 포화 상태와 공백 상태를 구분하기 위함이다. <br> * 만약 요소들의 개수를 저장하는 count 변수 추가시 비워두지 않아도 된다.

## 5.3.1 원형 큐의 삽입, 삭제 알고리즘
---
i. 원형 큐에서의 삽입 알고리즘
```c
- enqueue(Q, x):
    rear = (rear+1) % MAX_QUEUE_SIZE;
    Q[rear]<-x;
```
ii. 원형 큐에서의 삭제 알고리즘
```c
- dequeue(Q):
    front = (front+1) % MAX_QUEUE_SIZE;
    return Q[front];
```

## 5.3.2 원형 큐의 구현
---
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#define MAX_QUEUE_SIZE 5

typedef int element;
typedef struct {
	int front, rear;
	element data[MAX_QUEUE_SIZE];
}Cir_Queue;

void error(char* message)
{
	fprintf(stderr, "%s\n", message);
}

void init_queue(Cir_Queue* q)
{
	q->rear = 0;
	q->front = 0;
}
int is_empty(Cir_Queue* q)
{
	if (q->front == q->rear)
		return 1;
	else
		return 0;
}
int is_full(Cir_Queue* q)
{
	if ((q->rear + 1) % MAX_QUEUE_SIZE == q->front)
		return 1;
	else
		return 0;
}
void queue_print(Cir_Queue* q)
{
	printf("QUEUE(front=%d, rear=%d) = ", q->front, q->rear);
	if (!is_empty(q))
	{
		int i = q->front;
		do
		{
			i = (i + 1) % MAX_QUEUE_SIZE;
			printf("%d | ", q->data[i]);
			if (i == q->rear) // front to rear도달하면 종료
				break;
		} while (i != q->front); // 한바퀴 더 돌기전에 종료
	}
	printf("\n");
}
void enqueue(Cir_Queue* q, element val)
{
	if (!is_full(q))
	{
		q->rear = (q->rear + 1) % MAX_QUEUE_SIZE; // 원형 순환 개념 구현
		q->data[q->rear] = val;
	}
	else
		error("QUEUE IS FULL\n");
}
element dequeue(Cir_Queue* q)
{
	if (!is_empty(q))
	{
		q->front = (q->front + 1) % MAX_QUEUE_SIZE; // 원형 순환 개념 구현
		return q->data[q->front];
	}
	else
		error("QUEUE IS EMPTY\n");
}
int main()
{
	Cir_Queue q;
	int element;

	init_queue(&q);
	printf("--데이터 추가 단계--\n");
	while (!is_full(&q))
	{
		printf("Enter the number\n");
		scanf("%d", &element);
		enqueue(&q, element);
		queue_print(&q);
	}
	
	printf("--Data remove sequence--\n");
	while (!is_empty(&q))
	{
		element = dequeue(&q);
		printf("poped number : %d\n", element);
		queue_print(&q);
	}

	return 0;
}
```
<center>
<b>[코드 1]. 원형 큐의 구현(C언어)</b>
</center>

---
```c
--Data enqueue sequence--
Enter the number
10
QUEUE(front=0, rear=1) = 10 |
Enter the number
20
QUEUE(front=0, rear=2) = 10 | 20 |
Enter the number
30
QUEUE(front=0, rear=3) = 10 | 20 | 30 |
Enter the number
40
QUEUE(front=0, rear=4) = 10 | 20 | 30 | 40 |
--Data remove sequence--
poped number : 10
QUEUE(front=1, rear=4) = 20 | 30 | 40 |
poped number : 20
QUEUE(front=2, rear=4) = 30 | 40 |
poped number : 30
QUEUE(front=3, rear=4) = 40 |
poped number : 40
QUEUE(front=4, rear=4) =
```

<center>
<b>[결과 1]. 원형 큐의 구현(C언어)</b>
</center>