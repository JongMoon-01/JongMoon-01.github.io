---
title: "여러가지 정렬 알고리즘"
date: 2024-04-25 02:23:00 +0900
categories: [Algorithm]
tags: [CodingTest, Algorithm, Sort]
---
# 1. 문제
동아리원 중 한명이 숫자 정렬하는 것을 기반으로 주어진 문제를 해결하는걸 소개해줬다. 

 거기서 이제 궁금증이 든게 4가지 종류의 데이터가 주어졌을 때 어떤 알고리즘이 주어진 데이터에 대해 가장 빠른 처리 시간을 가질지 궁금해서 이 게시글을 작성했습니다.

 데이터의 종류는 다음과 같습니다.

- 데이터 크기 : 10000

 1. Pulse Data : 0과 100의 수만 가지는 데이터입니다.
 2. Random Data : 0~50,000범위의 난수를 가집니다.
 3. Sin Data : Sin파형에 맞는 데이터를 가집니다.(+-32528)
 4. Sparse Data : 일정 구역에만 데이터가 밀집되어있는 형태입니다. 밀집 구역의 갯수 또한 지정됩니다.

# 2. 소스 코드

```R
library(colorspace)
# 정렬 알고리즘 함수들 정의
bubble_sort <- function(arr) {
  n <- length(arr)
  for (i in 1:(n-1)) {
    for (j in 1:(n-i)) {
      if (arr[j] > arr[j+1]) {
        temp <- arr[j]
        arr[j] <- arr[j+1]
        arr[j+1] <- temp
      }
    }
  }
  return(arr)
}

insertion_sort <- function(arr) {
  n <- length(arr)
  for (i in 2:n) {
    key <- arr[i]
    j <- i - 1
    while (j > 0 && arr[j] > key) {
      arr[j + 1] <- arr[j]
      j <- j - 1
    }
    arr[j + 1] <- key
  }
  return(arr)
}

selection_sort <- function(arr) {
  n <- length(arr)
  for (i in 1:(n-1)) {
    min_index <- i
    for (j in (i+1):n) {
      if (arr[j] < arr[min_index]) {
        min_index <- j
      }
    }
    if (min_index != i) {
      temp <- arr[min_index]
      arr[min_index] <- arr[i]
      arr[i] <- temp
    }
  }
  return(arr)
}

quick_sort <- function(arr) {
  if (length(arr) <= 1) return(arr)
  
  pivot <- arr[ceiling(length(arr) / 2)]
  less <- arr[arr < pivot]
  equal <- arr[arr == pivot]
  greater <- arr[arr > pivot]
  
  return(c(quick_sort(less), equal, quick_sort(greater)))
}

merge_sort <- function(arr) {
  if (length(arr) <= 1) return(arr)
  
  merge <- function(left, right) {
    result <- c()
    while (length(left) > 0 || length(right) > 0) {
      if (length(left) > 0 && length(right) > 0) {
        if (left[1] <= right[1]) {
          result <- c(result, left[1])
          left <- left[-1]
        } else {
          result <- c(result, right[1])
          right <- right[-1]
        }
      } else if (length(left) > 0) {
        result <- c(result, left[1])
        left <- left[-1]
      } else if (length(right) > 0) {
        result <- c(result, right[1])
        right <- right[-1]
      }
    }
    return(result)
  }
  
  n <- length(arr)
  middle <- round(n / 2)
  left <- merge_sort(arr[1:middle])
  right <- merge_sort(arr[(middle + 1):n])
  
  return(merge(left, right))
}

heap_sort <- function(arr) {
  build_max_heap <- function(arr) {
    n <- length(arr)
    for (i in floor(n / 2):1) {
      heapify(arr, i, n)
    }
    return(arr)
  }
  
  heapify <- function(arr, i, n) {
    largest <- i
    left <- 2 * i
    right <- 2 * i + 1
    
    if (left <= n && arr[left] > arr[largest]) {
      largest <- left
    }
    if (right <= n && arr[right] > arr[largest]) {
      largest <- right
    }
    if (largest != i) {
      temp <- arr[i]
      arr[i] <- arr[largest]
      arr[largest] <- temp
      heapify(arr, largest, n)
    }
  }
  
  n <- length(arr)
  arr <- c(0, arr) # 0을 넣어서 인덱스 계산을 편하게 함
  arr <- build_max_heap(arr)
  for (i in n:2) {
    temp <- arr[1]
    arr[1] <- arr[i]
    arr[i] <- temp
    heapify(arr, 1, i - 1)
  }
  return(arr[-1])
}

counting_sort <- function(arr) {
  max_val <- max(arr)
  min_val <- min(arr)
  range <- max_val - min_val + 1
  
  counts <- integer(range)
  sorted_arr <- integer(length(arr))
  
  for (i in arr) {
    counts[i - min_val + 1] <- counts[i - min_val + 1] + 1
  }
  
  for (i in 2:length(counts)) {
    counts[i] <- counts[i] + counts[i - 1]
  }
  
  for (i in rev(arr)) {
    sorted_arr[counts[i - min_val + 1]] <- i
    counts[i - min_val + 1] <- counts[i - min_val + 1] - 1
  }
  
  return(sorted_arr)
}

# 정렬 알고리즘 이름
sort_names <- c("Bubble Sort", "Insertion Sort", "Selection Sort", "Quick Sort", "Merge Sort", "Heap Sort", "Counting Sort")
data_size = 10000
# 데이터 생성
data_pulse <- rep(c(0, 100), length.out = data_size)
data_random <- sample(0:50000, data_size, replace = TRUE)
data_sin <- as.integer(32528 * sin(2 * pi * 0.1 * seq(1, data_size)))
data_sparse <- rep(0, data_size)
sparse_indices <- sample(1:data_size, 3000)
data_sparse[sparse_indices] <- sample(-100:100, length(sparse_indices), replace = TRUE)

# 플롯 생성
par(mfrow = c(2, 2))  # 2x2 그리드 생성

# Pulse 데이터
plot(data_pulse, type = "l", main = "Pulse Data", xlab = "Index", ylab = "Value")

# Random 데이터
plot(data_random, type = "l", main = "Random Data", xlab = "Index", ylab = "Value")

# Sine 데이터
plot(data_sin, type = "l", main = "Sine Data", xlab = "Index", ylab = "Value")

# Sparse 데이터
plot(data_sparse, type = "l", main = "Sparse Data", xlab = "Index", ylab = "Value")

# 데이터 유형에 대한 색상 정의
data_colors <- sequential_hcl(4, palette='Sunset')

# 시간 측정 함수 정의
measure_time <- function(sort_function, data, data_color) {
  start_time <- Sys.time()
  sorted_data <- sort_function(data)
  end_time <- Sys.time()
  return(end_time - start_time)
}

# 데이터에 대해 정렬 알고리즘 시간 측정
pulse_times <- sapply(list(bubble_sort, insertion_sort, selection_sort, quick_sort, merge_sort, heap_sort, counting_sort), function(sort_func) measure_time(sort_func, data_pulse))
random_times <- sapply(list(bubble_sort, insertion_sort, selection_sort, quick_sort, merge_sort, heap_sort, counting_sort), function(sort_func) measure_time(sort_func, data_random))
sin_times <- sapply(list(bubble_sort, insertion_sort, selection_sort, quick_sort, merge_sort, heap_sort, counting_sort), function(sort_func) measure_time(sort_func, data_sin))
sparse_times <- sapply(list(bubble_sort, insertion_sort, selection_sort, quick_sort, merge_sort, heap_sort, counting_sort), function(sort_func) measure_time(sort_func, data_sparse))

# 막대 그래프로 시각화
barplot(
  rbind(pulse_times, random_times, sin_times, sparse_times),
  beside = TRUE,
  col = data_colors,
  ylim = c(0, max(c(pulse_times, random_times, sin_times, sparse_times))),
  names.arg = sort_names,
  xlab = "Sorting Algorithms",
  ylab = "Time (seconds)",
  main = "Sorting Time for Different Data Types"
)

# 카테고리와 범주 추가
legend("topright", legend = c("Pulse", "Random", "Sin", "Sparse"), fill = data_colors, bty = "n", title = "Data Types")
```

3. 결과

데이터 시각화는 200size로 진행하고 정렬 알고리즘은 10,000size로 진행했습니다. 

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/fd7761a8-2adb-4fe3-abad-237fabdd5437" width="720" height=""/>
<p><b>[그림12]. 4가지 종류의 데이터 시각화</b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/53f3825d-26d2-4316-9426-09bca53522f2" width="720" height=""/>
<p><b>[그림12]. 7가지 정렬 알고리즘 처리 시간</b></p>
</center>


