---
title: "재밌는 레이싱 경기장 설계하기"
date: 2024-05-30 02:23:00 +0900
categories: [Algorithm]
tags: [Algorithm, C, Sequence, Codingtest, ]
---
# 1. 문제 설명
현대모비스에서 개발한 자율주행을 이용한 레이싱 대회가 열렸습니다. 당신은 관람객들이 레이싱 대회를 재밌게 즐길 수 있도록 레이싱 경기장을 설계하려고 합니다.

레이싱 경기장은 <code>n</code>개의 지점으로 구성됩니다. 각 지점의 높이는 정해져 있지만, 그 순서는 자유롭게 바꿀 수 있습니다. 당신의 목표는 이웃한 지점끼리의 높이 차이의 최솟값이 최대가 되도록 높이 순서를 정하는 것입니다.

예를 들어 <code>n</code>이 3이고 세 지점의 높이가 각각 1, 5, 8인 경우를 생각해 봅시다.

높이를 [1, 5, 8] 순서로 배치하면 1번과 2번 지점의 높이 차이는 4, 2번과 3번 지점의 높이 차이는 3이므로 높이 차이의 최솟값은 3입니다.
높이를 [5, 1, 8] 순서로 배치하면 1번과 2번 지점의 높이 차이는 4, 2번과 3번 지점의 높이 차이는 7이므로 높이 차이의 최솟값은 4입니다.
높이 차이의 최솟값을 4보다 크게 만드는 방법은 없습니다.

지점 <code>n</code>개의 높이를 담은 1차원 정수 배열 <code>heights</code>가 매개변수로 주어집니다. 높이 차이의 최솟값이 최대가 되도록 레이싱 경기장을 설계했을 때의 높이 차이의 최솟값을 return 하도록 solution 함수를 완성해 주세요.

#### 제한 사항
- 2 ≤ <code>heights</code>의 길이 = <code>n</code> ≤ 200,000
- 1 ≤ <code>heights</code>의 원소 ≤ 109

### 입출력 예시

<table class="table">
        <thead><tr>
<th>heights</th>
<th>result</th>
</tr>
</thead>
        <tbody><tr>
<td>[1, 8, 5]</td>
<td>4</td>
</tr>
<tr>
<td>[11, 6, 4, 11]</td>
<td>5</td>
</tr>
<tr>
<td>[9, 9, 9, 9, 30]</td>
<td>0</td>
</tr>
</tbody>
</table>

# 2. 문제 분석
 해당 문제는 Lv5인것 치고는 1차원 배열만을 다룬다. 즉 수열에 관한 문제라고 생각하고 풀었다. 우리가 구해야하는건 [그림1]에서 나타내는<code>w</code>값 중 max값을 구해야한다.

 <center>
<img src="https://github.com/cisco/openh264/assets/56510688/d6be6f1b-4335-4bee-b491-cc7442f372ae" width="720" height=""/>
<p><b>[그림1]. 경기장 구조 </b></p>
</center>

# 3. 문제해결
## 3.1. 아이디어1
1. 원본 집합에서 Average 구한다.
2. Aver와 가장 가까운 차이 값 선택한다.
3. 부분 집합에서 Aver 구한다.
4. Aver와 가장 가까운 차이 값 선택한다.
집합의 원소 갯수가 1이 될때까지 반복한다.

그냥 일반 10개짜리 배열 가지고 해봤는데 틀렸다.

```
결과 배열 :  21,7,1,11,2,9,7,9,8,41

1). 0,2,4,5,6,2,14,1,34
2). 0,2,4,5,8,1,1,34
3). 6,8,10,1,8,7,40
4). 4,2,9,2,3,30
5). 5,7,7,6,39
6). 2,0,1,32
7). 2,1,34
8). 1,32
```

## 3.2. 아이디어2
전체 탐사 방법을 사용해보려고했다.
1. 집합에서 사용할 수 있는 모든 경우의 수 표시
2. 해당 경우의 수 마다 차이의 최솟값 산출
3. 그 중 최솟값이 가장 높은 배열 선택

배열의 N이 10만 되도 10! 값을 가지기 때문에 굉장히 오래걸린다.
시간 복잡도가 지수증가이므로 기각했다.

## 3.3. 아이디어3
해당 문제의 예제 입력을 유심히 살펴보고 진행했다.
예제입력은 총 3개로 나뉘어져 있는데, 홀수갯수와 짝수갯수를 입력했을 때 패턴을 분석해 그래프로 만들었다.

1) 짝수 일 때

- 원본 배열의 i%2번째 인덱스의 값을 오름차순으로 정렬 후 배열에 넣는다.

- 원본 배열의 i%2+1번째 인덱스의 값을 오름차순으로 정렬 후 배열에 넣는다.

정답은 그래프 끝부분을 M이라 할때 [M]-[M-1]이므로 원본 배열에서 다시 생각해보면 [M/2] - [M-1/2]로 생각할 수 있다. 즉 정렬된 원본 배열의 배열 중앙에 위치한 두 값만을 이용해 답을 생각해낼 수 있다.
 <center>
<img src="https://github.com/cisco/openh264/assets/56510688/874130f4-d1d8-48ff-b922-b1d5216ffea4" width="720" height=""/>
<p><b>[그림1]. 짝수 경우 </b></p>
</center>

2) 홀수 일 때

- 우선 배열의 Aver 값을 구하고 해당 값과 가장 가까운 인덱스의 값을 배열의 맨마지막으로 배치하고 마지막 값을 제외한 후 배열을 정렬한다. 그리고 짝수인 경우와 같은 방법을 사용한다.

```
#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>
#define N 200000


int partition(int arr[], int low, int high) {
    int pivot = arr[high]; // 피벗을 배열의 마지막 요소로 설정
    int i = (low - 1); // 작은 요소의 인덱스

    for (int j = low; j <= high - 1; j++) {
        // 현재 요소가 피벗보다 작거나 같으면
        if (arr[j] <= pivot) {
            i++; // 작은 요소의 인덱스를 증가
            // arr[i]와 arr[j]를 교환
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    // 피벗을 올바른 위치에 놓기 위해 arr[i+1]와 arr[high]를 교환
    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;
    return (i + 1);
}

// 퀵소트 함수
void quickSort(int arr[], int low, int high) {
    if (low < high) {
        // 파티션 인덱스 pi는 arr[pi]가 올바른 위치에 있는 인덱스
        int pi = partition(arr, low, high);

        // 피벗을 기준으로 나누어진 두 부분을 각각 정렬
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

// 배열을 출력하는 함수
void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++)
        printf("%d ", arr[i]);
    printf("\n");
}

int findClosestIndex(int arr[], int size, int target) {
    // 초기값으로 첫 번째 요소의 인덱스를 저장
    int closestIndex = 0;
    // 가장 가까운 거리를 초기화
    int minDiff = abs(target - arr[0]);

    // 배열을 순회하면서 가장 가까운 요소를 찾음
    for (int i = 1; i < size; i++) {
        // 현재 요소와 target 사이의 차이를 구함
        int diff = abs(target - arr[i]);
        // 현재 요소와 target 사이의 차이가 최소값보다 작으면 closestIndex와 minDiff를 업데이트
        if (diff < minDiff) {
            minDiff = diff;
            closestIndex = i;
        }
    }

    return closestIndex;
}

int calculateAverage(int arr[], size_t size) {
    int sum = 0;

    // 배열 요소의 합을 계산
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }

    // 평균을 계산
    return sum / size;
}

// heights_len은 배열 heights의 길이입니다.
int solution(int heights[], size_t heights_len) {
    int answer = 0;
    int first;
    printf("길이 : %d\n", heights_len);
    printf("초기 배열: \n");
    printArray(heights, heights_len);
    quickSort(heights, 0, heights_len - 1);

    printf("정렬된 배열: \n");
    printArray(heights, heights_len);
    int average = calculateAverage(heights, heights_len);
    printf("평균 : %d\n", average);
    int index = findClosestIndex(heights, heights_len, average);
    printf("가장가까운 인덱스 : %d\n", index);
    int tmp;
    if (heights_len % 2 == 0) // 짝수 일 때
    {
        answer = heights[((heights_len-1) / 2) + 1] - heights[(heights_len - 1)/ 2];
    }
    else // 홀수 일 때
    {
        tmp = heights[index];
        heights[index] = heights[heights_len-1];
        heights[heights_len-1] = tmp;
        quickSort(heights, 0, heights_len - 2);
        first = heights[heights_len - 1] - heights[0];
        answer = heights[((heights_len - 2) / 2) + 1] - heights[(heights_len - 2) / 2];
        if (first > answer)
            answer = first;
    }
    return answer;
}


int main() {
    int set[5] = { 11,6,4,11,7 };
    int result = 0;
    /*
    int heightLength = rand() % 199999 + 2;
    heightLength++;
    int result = 0;
    for (int i = 2; i < heightLength; i++)
    {
        set[i] = (rand() % (1000000000 - 1) + 1);
    }
    */

    result=solution(set, 5);
    printf("정답 : %d\n", result);
    return 0;
}
```

## 3.4. 아이디어4

1) 짝수 일 때

    위의 아이디어와 거의 동일하지만 홀수번째 인덱스 값을 내림차순으로 정렬한다는 차이점이 있다.

- 원본 배열의 i%2번째 인덱스의 값을 오름차순으로 정렬 후 배열에 넣는다.

- 원본 배열의 i%2+1번째 인덱스의 값을 내림차순으로 정렬 후 배열에 넣는다.


 <center>
<img src="https://github.com/cisco/openh264/assets/56510688/56d07252-1c43-4e6f-956b-31dd49b34289" width="720" height=""/>
<p><b>[그림1]. 짝수 경우 </b></p>
</center>

아직 코드를 다 못짰다.

2) 홀수 일 때

    이것도 홀수번째 인덱스 값을 내림차순으로 정렬하는것 빼고는 동일하다.