---
title: "연결 리스트"
date: 2022-05-03 02:37:00 +0900
categories: [Data struct, Algorithm]
tags: [list,pointer,C]
---
# 6.3 연결 리스트
연결 리스트는 동적으로 크기가 변할 수 있고 삭제나 삽입 시에 데이터 이동이 필요 없는 연결된 표현(Linked representation)을 사용한다.<br>
연결된 표현은 포인터를 사용하여 데이터들을 연결한다. 연결하는 줄은 포인터로 표현된다.

<center>

<img src="https://user-images.githubusercontent.com/56510688/235987893-5192e4c2-227b-46b2-ae0f-0a2a411a6b64.png" width="700" height=""/>

<b>[그림 1]. 덱의 구조 </b>

</center>

- 배열 : 메모리상에 데이터가 한군데 밀집되어있음.<br>
- 연결 리스트 : 메모리상에 데이터가 흩어져있음. (물리적으로 떨어져 있는 데이터를 하나로 묶는다.)

<center>

<table border="1">
    <th>배열</th>
    <th>리스트</th>
	<tr><!-- 첫번째 줄 시작 -->
	    <td><img src="https://user-images.githubusercontent.com/56510688/235988647-f0fa6913-c4bd-4256-8c43-4f7448761c2e.png" width="300" height=""/></td>
        <td><img src="https://user-images.githubusercontent.com/56510688/235988661-430dd21b-970a-4eef-99d2-69c9304536c1.png" width="300" height=""/></td>
	</tr><!-- 첫번째 줄 끝 -->
	<tr><!-- 두번째 줄 시작 -->
	</tr><!-- 두번째 줄 끝 -->
</table>

<b>[그림 2]. 배열과 리스트의 메모리 할당 </b>

</center>

연결리스트를 사용하면 배열 리스트보다 어떤 장점이 있을까? <br>
배열을 이용한 리스트에서 가장 문제가 되었던 중간에 값 삽입, 삭제 문제에 대해서 생각해보자. <br>
연결리스트에서는 중간에 값 삽입을 저장된 데이터들의 이동 없이 포인터를 바꿔서 구현이 가능하다. (아래 그림은 연결리스트에서의 삽입, 삭제를 보여준다.) <br>
 - 연결리스트 장점
 1. 첫 번째 데이터의 head만 알면 나머지 데이터도 알 수 있음.
 2. 저장할 공간이 필요할 때마다 동적으로 생성 가능.
 - 연결리스트 단점
 1. 배열에 비해 상대적으로 구현이 어려움, 오류 잘남.
 2. Field값으로 Data, Pointer값을 가지기 때문에 메모리를 더 사용함.
 3. i번째 데이터 search시 순차 접근을 사용해야함.

<center>

<table border="1">
	<th>데이터 구조</th>
    <th>삽입</th>
    <th>삭제</th>
	<tr><!-- 첫번째 줄 시작 -->
	    <td>배열</td>
	    <td><img src="https://user-images.githubusercontent.com/56510688/236448504-594adb07-2b92-4de3-9f65-ab0c142b1474.png" width="400" height=""/></td>
        <td><img src="https://user-images.githubusercontent.com/56510688/236448508-2e645b82-d283-40ad-ab31-db77c174bb39.png" width="400" height=""/></td>
	</tr><!-- 첫번째 줄 끝 -->
	<tr><!-- 두번째 줄 시작 -->
	    <td>리스트</td>
	    <td><img src="https://user-images.githubusercontent.com/56510688/236447512-fefafdd7-38b4-4f23-9800-cadda167b5f3.png" width="400" height=""/></td>
        <td><img src="https://user-images.githubusercontent.com/56510688/235991558-eb3d39f8-3877-4685-940f-4c816c68ad13.png" width="400" height=""/></td>
	</tr><!-- 두번째 줄 끝 -->
</table>

<b>[그림 3]. 리스트 삽입과 삭제 </b>

</center>

## 6.3.1 연결리스트의 구조

연결리스트는 노드들의 집합이다. 노드는 메모리의 어떤 위치에나 있을 수 있으며, 다른 노드로 가기 위해선 포인터를 이용한다.
노드는 아래 그림과 같이 Data Field와 Link Field로 구성되어있다.

<center>
<img src="https://user-images.githubusercontent.com/56510688/236449116-819b35e1-c8b3-4669-96cf-db5ac40a2285.png" width="400" height=""/>

<b>[그림 4]. 리스트의 구조 </b>
</center>

연결리스트에서는 리스트의 첫 번째 노드(head pointer)를 알아야만 전체 노드에 접근할 수 있다. 그리고 마지막 노드의 링크 필드는 NULL값을 가지게 된다. <br>
또한 노드들은 필요할때마다 malloc()을 통해서 동적으로 생성된다.

<center>
<img src="https://user-images.githubusercontent.com/56510688/236449469-75a957c8-bdc9-4881-9792-141e8d7aeb35.png" width="700" height=""/>

<b>[그림 5]. 리스트의 구조 </b>
</center>

## 6.3.2 연결리스트의 종류
연결리스트에서는 다음과 같이 3가지 종류의 연결리스트가 존재한다.

<center>

<table border="1">
	<th></th>
	<th>단순 연결 리스트</th>
    <th>원형 연결 리스트</th>
    <th>이중 연결 리스트</th>
	<tr><!-- 첫번째 줄 시작 -->
	    <td>특징</td>
	    <td>단 방향 연결, chain으로도 불림, 마지막 노드의 링크는 NULL값을 가짐.</td>
        <td>마지막 노드의 링크가 첫번째 노드(head)를 가르킨다.</td>
        <td>각 노드마다 2개의 링크가 존재, 양방향 연결</td>
	</tr><!-- 첫번째 줄 끝 -->
	<tr><!-- 두번째 줄 시작 -->
	    <td>구조</td>
	    <td><img src="https://user-images.githubusercontent.com/56510688/236516678-b01e4f6c-b8ef-4d09-9969-b8cbefb1389f.png" width="700" height=""/></td>
        <td><img src="https://user-images.githubusercontent.com/56510688/236516669-63c8ed98-faa0-4022-869c-7da16c94dec8.png" width="700" height=""/></td>
        <td><img src="https://user-images.githubusercontent.com/56510688/236516683-b4f94285-e38f-4474-9c1c-39cb100ef78d.png" width="800" height=""/></td>
	</tr><!-- 두번째 줄 끝 -->
</table>


<b>[그림 6]. 리스트의 종류 </b>
</center>

# 6.4 단순 연결 리스트
 - 노드: 하나의 링크 Field Value를 가진다.
 - 첫번째 노드의 링크 필드 값 : Head Point
 - 마지막 노드의 링크 필드 값 : NULL

 <center>
<img src="https://user-images.githubusercontent.com/56510688/236454150-0e49de55-28f9-49e0-9528-dcc3bb0c846a.png" width="700" height=""/>

<b>[그림 7]. 단순 연결 리스트의 구조 </b>
</center>

C언어로 단순 연결 리스트의 구조를 구현하기 위해선 다음과 같은 질문에 답해야한다.
 - 노드는 어떻게 정의할 것인가? -> 자기 참조 구조체를 이용한다.
 - 노드는 어떻게 생성할 것인가? -> malloc()을 호출하여 동적 메모리로 생성한다.
 - 노드는 어떻게 삭제할 것인가? -> free()를 호출하여 동적 메모리를 해제한다.

 ## 6.4.1 노드의 정의
 노드는 자기 참초 구조체를 이용하여 정의된다. 구조체 안에는 데이터를 저장하는 Data Field와 포인터가 저장된 Link Field가 존재한다. <br>
 1. Data Field : element 타입의 데이터를 저장한다.
 2. Link Field : ListNode를 가리키는 포인터로 정의되며 다음 노드의 주소가 저장된다.
 >* 자기참조 구조체란?
 > <br> 자기자신을 참조하는 포인터를 포함하는 구조체이다.

```c
typdef int element;

typedef struct ListNode{
    element data;
    struct ListNode *link;
} ListNode;
```

## 6.4.2 공백리스트의 생성
단순 연결 리스트는 헤드 포인터만 있으면 모든 노드를 찾을 수 있다. 따라서 다음과 같은 노드를 가르키는 포인터 head를 정의하면 단순 연결리스트가 만들어졌다고 볼 수 있다.<br>
현재 노드는 없으므로 head의 값은 NULL이 된다.

```c
ListNode *head = NULL;
```

> *어떤 리스트가 공백인지 확인하려면 헤드포인터가 NULL인지 검사하면된다.

 ## 6.4.3 노드의 생성
연결 리스트에서는 필요할 떄마다 동적 메모리 할당을 이용하여 노드를 동적 생성한다.
```c
head = (ListNode *)malloc(sizeof(ListNode)); // 노드의 크기만큼 동적메모리를 할당 받는다.
```
위 코드는 동적 메모리의 주소를 헤드 포인터인 head에 저장한다.

<img src="https://user-images.githubusercontent.com/56510688/236469881-c469cb02-574a-4050-bce3-b08dbf068a20.png" width="200" height=""/>

다음 절차는 새로 만들어진 노드에 데이터를 저장하고 링크필드를 초기화 한다.
```c
head->data = 10;
head->link = NULL;
```

<img src="https://user-images.githubusercontent.com/56510688/236471708-6c2bc596-ae19-4545-a2b5-02051eadf1e4.png" width="200" height=""/>

 ## 6.4.4 노드의 연결
 두번째 노드를 동적으로 생성하고 노드에 20을 저장한다.
 ```c
 ListNode *p;
 p = (ListNode *)malloc(sizeof(ListNode));
 p->data = 20;
 p->link = NULL;
 ```

<img src="https://user-images.githubusercontent.com/56510688/236472480-c8c25eab-b302-4706-b60f-9210ce840d90.png" width="200" height=""/>

생성된 두개의 노드를 서로 연결한다. head->link에 p를 저장하면, 첫번째 노드의 링크가 두번째 노드를 가르킨다.
```c
head->link = p;
```

<img src="https://user-images.githubusercontent.com/56510688/236473966-b2ede63e-5ca2-4cb7-83a1-a7f210fcdbe2.png" width="400" height=""/>

# 6.5 단순 연결 리스트의 연산 구현
```s
- insert_first() : 리스트의 시작 부분에 항목을 삽입하는 함수
- insert() : 리스트의 중간 부분에 항목을 삽입하는 함수
- delete_first() : 리스트의 첫 번째 항목을 삭제하는 함수
- delete() : 리스트의 중간 항목을 삭제하는 함수(도전 문제)
- print_list() : 리스트를 방문하여 모든 항목을 출력하는 함수
```

## 6.5.1 단순 연결리스트 정의
원칙적으로 하나의 헤드 포인터만 있으면 단순 연결리스트가 정의된다.

```c
ListNode* head;
```

## 6.5.2 삽입 연산 insert_first()
리스트의 첫 부분에 새로운 노드를 추가하는 함수이다.<br>
insert_first()는 변경된 헤드포인터를 반환한다.

```c
ListNode* insert_first(ListNode *head, element value); // head는 헤드 포인터 value는 Data Field값
```

<img src="https://user-images.githubusercontent.com/56510688/236626620-97b2b462-aa5a-4ff9-9e22-1b8f23e939dd.png" width="400" height=""/>

알고리즘화 하면 다음과 같다.
```s
insert_first(head, value):
1. p <- malloc() # 동적 메모리를 할당하여 새로운 노드 p 생성
2. p -> data <- value #p -> data에 value를 저장한다.
3. p -> link <- head #p -> link를 현재의 head값으로 변경한다.
4. head <- p #head를 p값으로 변경한다.
5. return head # 변경된 헤드 포인터 반환
```

---

### 코드 구현

```c
ListNode* insert_first(ListNode *head, int value)
{
	ListNode *p = (ListNode*)malloc(sizeof(ListNode));
	p->data = valuie;
	p->link = head;
	head = p;
	return head;
}
```

<center>
<b>[코드 1]. 삽입 연산 insert_first() 함수(C언어)</b>
</center>

## 6.5.3 삽입 연산 insert()
연결 리스트의 중간에 새로운 노드를 추가한다. <br> 
> 이때는 반드시 삽입되는 위치의 선행 노드(pre)를 알아야한다.

<img src="https://user-images.githubusercontent.com/56510688/236626923-b611061f-c94d-4ec8-b61b-5f6964499d91.png" width="400" height=""/>

알고리즘화 하면 다음과 같다.

```s
insert(head, pre, value): # list: 연결 리스트, pre: 선행 노드, value: 추가할 값
1. p <- malloc() # 동적 메모리 할당하여 새로운 노드 p 생성
2. p->data <- value # p데이터 필드에 value 저장
3. p->link <- pre -> link # p의 링크 필드가 pre노드를 가르키도록 변경
4. pre->link <- p # pre의 링크 필드가 p를 가르키도록 변경
5. return head # 변경된 헤드포인터 반환
```

---

### 코드 구현

```c
ListNode* insert(ListNode *head, ListNode *pre, element value)
{
	ListNode *p = (ListNode *)malloc(sizeof(ListNode));
	p->data = value;
	p->link = pre->link;
	pre->link = p;
	return head;
}
```

<center>
<b>[코드 2]. 삽입 연산 insert() 함수(C언어)</b>
</center>

## 6.5.4 삭제연산 delete_first()
첫번쨰 노드를 삭제하는 함수 delete_first()는 다음과 같은 원형을 가진다.
```c
ListNode* delete_first(ListNode *head);
```
<img src="https://user-images.githubusercontent.com/56510688/236627570-e7bd76ab-e218-4eb6-a82c-f5867ab2001a.png" width="400" height=""/>

알고리즘화 하면 다음과 같다.

```s
delete_first(head):
1. removed <- head # 헤드 포인터의 값을 removed에 복사한다.
2. head <- head -> link # 헤드 포인터의 값을 head->link로 변경한다.
3. free(removed) # removed가 가르키는 동적 메모리를 반환한다.
4. return head # 변경된 헤드포인터를 반환한다.
```

### 코드 구현

---

```c
ListNode* delete_first(ListNode* head)
{
	ListNode *removed;
	if(head == NULL) return NULL;
	removed = head;
	head = removed->link;
	free(removed);
	return head;
}
```

<center>
<b>[코드 3]. 삭제연산 delete_first() 함수(C언어)</b>
</center>

## 6.5.5 삭제 연산 delete()
연결 리스트 중간의 값을 삭제한다. <br> 
> 이때는 반드시 삽입되는 위치의 선행 노드(pre)를 알아야한다.

<img src="https://user-images.githubusercontent.com/56510688/236627796-9861f2e9-d64b-434b-801b-bce8577763dd.png" width="400" height=""/>

알고리즘화 하면 다음과 같다.

```s
delete(head, pre):
1. removed <- pre->link # pre의 링크 필드 값을 removed에 복사한다.
2. pre->link <- removed->link # pre의 링크 값을 removed->link로 변경한다.
3. free(removed) # removed가 가르키는 동적 메모리를 반환한다.
4. return head # 변경된 헤드포인터를 반환한다.
```

### 코드 구현

---

```c
ListNode* delete(ListNode *head, ListNode *pre)
{
	ListNode *removed;
	removed = pre->link;
	pre->link = removed->link;
	free(removed);
	return head;
}
```

<center>
<b>[코드 4]. 삭제 연산 delete() 함수(C언어)</b>
</center>

## 6.5.6 print_list() 함수
노드의 링크값이 NULL이 아니면 계속 링크를 따라가면서 노드를 방문한다.

```c
void print_list(ListNode* head)
{
	for(ListNode *p = head; p!=NULL; p=p->link)
	{
		printf("%d->", p->data);
	}
	printf("NULL \n");
}
```

<center>
<b>[코드 5]. print_list() 함수(C언어)</b>
</center>

### 전체 프로그램 테스트
```c
#include <stdio.h>
#include <stdlib.h>

typedef int element;
typedef struct ListNode {
	element data;
	struct ListNode *link;
}ListNode;

ListNode* insert_first(ListNode *head, int value)
{
	ListNode *p = (ListNode*)malloc(sizeof(ListNode));
	p->data = valuie;
	p->link = head;
	head = p;
	return head;
}
ListNode* insert(ListNode *head, ListNode *pre, element value)
{
	ListNode *p = (ListNode *)malloc(sizeof(ListNode));
	p->data = value;
	p->link = pre->link;
	pre->link = p;
	return head;
}
ListNode* delete_first(ListNode* head)
{
	ListNode *removed;
	if(head == NULL) return NULL;
	removed = head;
	head = removed->link;
	free(removed);
	return head;
}
ListNode* delete(ListNode *head, ListNode *pre)
{
	ListNode *removed;
	removed = pre->link;
	pre->link = removed->link;
	free(removed);
	return head;
}
void print_list(ListNode* head)
{
	for(ListNode *p = head; p!=NULL; p=p->link)
	{
		printf("%d->", p->data);
	}
	printf("NULL \n");
}

int main(void)
{
	ListNode *head = NULL;

	for(int i=0; i<5; i++)
	{
		head = insert_first(head, i);
		print_list(head);
	}
	for(int i=0; i<5; i++)
	{
		head = delete_first(head);
		print_list(head);
	}
	return 0;
}
```

<center>
<b>[코드 6]. 단순 연결 리스트 구현(C언어)</b>
</center>

---

```c
0->NULL
1->0->NULL
2->1->0->NULL
3->2->1->0->NULL
4->3->2->1->0->NULL
3->2->1->0->NULL
2->1->0->NULL
1->0->NULL
0->NULL
NULL
```

<center>
<b>[결과 1]. 단순 연결 리스트 구현(C언어)</b>
</center>