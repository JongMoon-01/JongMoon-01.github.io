---
title: "Dining Philosophers"
date: 2024-04-15 02:23:00 +0900
categories: [Algorithm]
tags: [CodingTest, Algorithm, mutex, semaphore]
---
# 1. 문제

The Dining Philosophers Problem is an illustrative example of a common computing problem in concurrency.
The dining philosophers problem describes a group of philosophers sitting at a table doing one of two things
- eating or thinking. While eating, they are not thinking, and while thinking, they are not eating. The
philosophers sit at a circular table each with a bowl of spaghetti. A chopstick is placed in between each
philosopher, thus each philosopher has one chopstick to his or her left and one chopstick to his or her right.
As spaghetti is difficult to serve and eat with a single chopstick, it is assumed that a philosopher must eat
with two chopsticks. The philosopher can only use the chopstick on his or her immediate left or right.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/9ffec174-9c94-4aff-8457-93e26d5d3a0c" width="720" height=""/>
<p><b>[그림1]. 식사하는 철학자들 </b></p>
</center>


# 2. 문제 해석
스파게티 = Task, 식기 = Resource, 철학자 = Process or Thread라고 생각하면 편합니다.

- 오브젝트 : 스파게티, 포크(=젓가락), 철학자

- 철학자가 가질 수 있는 상태
	1. Eating
	2. Thinking 

* deadlock : it is a situation wherein two or more competing actions are waiting for the other to finish
* starvation : This refers to a situation where a specific process or thread continuously fails to access the necessary resources. This typically occurs when a lower-priority process or thread is consistently overlooked by a higher-priority one.

## 2.1. Semaphore
동기화 기법 중 하나로, Process나 thread간의 shared resource 에 대한 접근 제어를 위해 사용됩니다.

세마포어는 카운터와 대기 큐로 구성되어 있습니다. 카운터 값은 세마포어가 동시에 허용하는 접근 가능한 자원의 수를 나타냅니다.
```
P Operation(wait) 
(if semaphore > 0) semaphore--
(if semaphore == 0) this process or thread has wait status(This status can be waked up by another process or thread that acts V operation )

V Operation(Signal)
semaphore++
If there is wating process or thread, waking up it
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/fd3cdb88-ab24-4957-9bb1-bcebeed59cf3" width="720" height=""/>
<p><b>[그림 2]. Semaphore Operations </b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/a976e552-00d4-45b7-a078-b71205b13255" width="720" height=""/>
<p><b>[그림 3]. Semaphore Operations Pesudo-Code </b></p>
</center>


## 2.2. Mutex
상호 배제(mutual exclusion)를 달성하기 위한 동기화 기법 중 하나입니다. 이는 여러 프로세스나 스레드가 동시에 공유 자원에 접근하지 못하도록 제어하는 메커니즘입니다.

```
Lock(잠금) 또는 Acquire(획득): 이 연산은 뮤텍스를 획득하고 잠그는 것을 의미합니다. 만약 다른 프로세스나 스레드가 이미 뮤텍스를 획득한 경우, 해당 프로세스나 스레드는 뮤텍스가 해제될 때까지 대기 상태로 들어갑니다.

Unlock(해제) 또는 Release(해제): 이 연산은 뮤텍스를 해제하여 다른 프로세스나 스레드가 뮤텍스를 획득하고 사용할 수 있도록 합니다.
```



<center>
<img src="https://github.com/cisco/openh264/assets/56510688/3f29fd07-ec44-4c5a-b369-fb1e8b4016c9" width="720" height=""/>
<p><b>[그림 4]. Mutex Example </b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/2bc3bd93-a9fc-4341-be28-6d783eacb198" width="720" height=""/>
<p><b>[그림 5]. thread와 Limited-Buffer Communication program  </b></p>
</center>

## 2.3. Deadlock 조건
- 상호배제(Mutual exclusion) : 프로세스들이 필요로 하는 자원에 대해 배타적인 통제권을 요구한다.

- 점유대기(Hold and wait) : 프로세스가 할당된 자원을 가진 상태에서 다른 자원을 기다린다.

- 비선점(No preemption) : 프로세스가 어떤 자원의 사용을 끝낼 때까지 그 자원을 뺏을 수 없다.

- 순환대기(Circular wait) : 각 프로세스는 순환적으로 다음 프로세스가 요구하는 자원을 가지고 있다.

# 3. 문제 해결 방안

## 3.1. 해결 방안 1

```
1. 철학자가 왼쪽 포크를 든다.
2. 오른쪽 포크를 든다.
3. 식사를 한다.
4. 왼쪽 포크를 내려놓는다.
5. 오른쪽 포크를 내려놓는다.

-> 1번을 수행하고 2번을 수행하려 하면 남아있는 포크가 없기 때문에 데드락이 발생한다.
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/00bb6f14-d899-426f-868d-30955729a709" width="720" height=""/>
<p><b>[그림 6]. DeadLock 유발  </b></p>
</center>

## 3.2. 해결 방안 2

```
1. 철학자가 왼쪽 포크를 든다.
2. 오른쪽 포크를 들 수 있다면 든다. 안되면 왼쪽 포크를 내려놓는다.
3. 식사를 한다.
4. 왼쪽 포크를 내려놓는다.
5. 오른쪽 포크를 내려놓는다.

-> 1번을 수행하고 2번을 수행하려 하면 남아있는 포크가 없기 때문에 다시 왼쪽 포크를 내려놓고 무한루프에 빠져 기아문제가 발생한다.
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/ae631be2-5ea0-4c2b-a4c9-927eece71b39" width="720" height=""/>
<p><b>[그림 6]. Starvation 유발  </b></p>
</center>


## 3.3. 해결 방안 3

```
1. 철학자가 왼쪽 포크를 든다.
2. 오른쪽 포크를 들 수 있다면 든다. 안되면 왼쪽 포크를 내려놓고 랜덤 시간 동안 기다린다.
3. 식사를 한다.
4. 왼쪽 포크를 내려놓는다.
5. 오른쪽 포크를 내려놓는다.

-> 1번을 수행하고 2번 수행 때 서로 각기 다른 Clock Cycle을 가지게 되므로 어떤 철학자는 식사할 가능성이 있다. 하지만 기아 문제가 발생할 확률 또한 존재한다.
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/3c4e03bc-46e1-4c88-9cc7-4f20dc2835cf" width="720" height=""/>
<p><b>[그림 7]. 확률 Starvation 유발  </b></p>
</center>

## 3.4. 해결 방안 4

```
1. 왼쪽 포크와 오른쪽 포크를 드는 행위를 세마포어로 감싼다.
2. 각 프로세스 별로 포크의 상태(mutex)를 확인하고 식사를 진행 한다.
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/3d8fc3dd-9502-4736-a4ff-5780d482dd06" width="720" height=""/>
<p><b>[그림 8]. Mutex와 세마포어 이용 </b></p>
</center>

