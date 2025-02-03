---
title: "R 기초문법 1"
date: 2022-05-01 18:37:00 +0900
categories: [R, Statistics, PreProcessing]
tags: [DataFrame,matrix]
---
# 1. 할당
```r
#할당
#지정해주는 명칭에 값을 저장
a = 2
print(a)
```

---

```r
[1] 2
```

# 2. 논리문
```r
# == : equal, != : unequal
A == 2
A != 2
```

---

```r
[1] TRUE
[1] FALSE
```

# 3. 벡터의 생성
```r
#c() : combind의 약자로 
#데이터에서의 하나열(column)을 의미
B = c(2,3,4,5)
print(B)
```

---

```r
[1] 2 3 4 5
```

## 3.1 rep(), sep()을 통한 벡터 생성
```r
#sequence의 줄임말
#seq(from, to, by) == range{python}

x1 = c(1:10)
x2 = seq(1, 10, 1)
x3 = seq(1,10,2)

print(x1)
print(x2)
print(x3)

#rep(반복할 값, 반복할 횟수)
y1 = rep(1,10)
y2 = rep(c(1:10), 2)
y3 = rep(c(1,10), c(2,2))

print(y1)
print(y2)
print(y3)
```

---

```r
> print(x1)
 [1]  1  2  3  4  5  6  7  8  9 10
> print(x2)
 [1]  1  2  3  4  5  6  7  8  9 10
> print(x3)
 [1] 1 3 5 7 9

> print(y1)
 [1] 1 1 1 1 1 1 1 1 1 1
> print(y2)
 [1]  1  2  3  4  5  6  7  8  9 10  1  2  3  4  5  6  7  8  9 10
> print(y3)
 [1]  1  1 10 10
```

# 4. matrix 생성
```r
#matrix(data, row, ncol, byrow = 행/열 기준)
matrix_R = matrix(
  data =x1,
  nrow = 5
)
print(matrix_R)

matrix_c = matrix(
  data = x1,
  ncol = 5
)
print(matrix_c)
```

---

```r
> print(matrix_R)
     [,1] [,2]
[1,]    1    6
[2,]    2    7
[3,]    3    8
[4,]    4    9
[5,]    5   10

> print(matrix_c)
     [,1] [,2] [,3] [,4] [,5]
[1,]    1    3    5    7    9
[2,]    2    4    6    8   10
```

# 5. Dataframe 생성
```r
DATA_SET = data.frame(
  X1 = x1,
  X2 = x2,
  X3 = x3,
  y1 = y1
)
#1~5행까지 출력
print(head(DATA_SET, 5))
```

---

```r
> print(head(DATA_SET, 5))
  X1 X2 X3 y1
1  1  1  1  1
2  2  2  3  1
3  3  3  5  1
4  4  4  7  1
5  5  5  9  1
> print(DATA_SET)
   X1 X2 X3 y1
1   1  1  1  1
2   2  2  3  1
3   3  3  5  1
4   4  4  7  1
5   5  5  9  1
6   6  6  1  1
7   7  7  3  1
8   8  8  5  1
9   9  9  7  1
10 10 10  9  1
```

## 5.1 length(), dim()을 활용한 데이터 형태 파악
```r
#length(vector[dims=1])
length(x1)

#dim(matrix or dataframe)
dim(matrix_c)
dim(DATA_SET)
```

---

```r
> dim(matrix_c)
[1] 2 5
> dim(DATA_SET)
[1] 10  4
```

## 5.2 R에서의 (), {}, []의미
### 5.2.1 ( ) 소괄호 : 함수와 함께 element값을 인수로 받음
```r
A = c(1,2,3,4,5)
```

### 5.2.2 { } 중괄호 : for, if문 등 조건식에 사용
```r
for(i in A){
  print(i)
}

B = c()
for(k in seq(1,10,1)){
  B = c(B, k)
}
```

### 5.2.3 [ ] 대괄호 : index 입력할때 사용
```r
A[2]
A[2:4]
#3번째 idx 빼고
A[-3]
#1,2,4,5번째 값
A[c(1,2,4,5)]
```

# 6. 변수 형태
```r
Numeric_vettor = c(1:20)
chr_Vector = c("A","B","C")

str(Numeric_vettor)
str(chr_Vector)
```

- 변수 종류

```s
 - Factor : 명목형 변수
 - Posixct : 시간 변수(년/월/일 시:분:초)
 - Tseries : 시계열 변수
 - Discrete(이산형) : 1,2,3,4 등 셀 수 있는 명목형 변수(서열 정보 X)
 - 서열형 변수 : 명목현 변수에서 서열 정보가 추가된 것
 - Continuous(연속형) : 셀수 없고 구간으로 정의됨, 변수 정보량이 가장 많음
```

## 6.1 시간(날짜) 형태 변수 다루기

```s
1. as.Date() : 년-월-일
2. as.POSIXct() : 년-월-일 시:분:초
3. lubridate패키지 사용
```

```r
#as.Date(para, format="날짜형식")
DATE_O = "2018-01-02"
DATE_C = as.Date(DATE_O, format="%Y-%m-%d")

str(DATE_O)
str(DATE_C)

#as.POSIXct(날짜, format = "날짜형식")
DATE_O2 = "2015-02-04 23:13:23"
DATE_P = as.POSIXct(DATE_O2, format="%Y-%m-%d %H:%M:%S")

#format(날짜변수, "형식")
format(DATE_P, "%A")
format(DATE_P, "%S")
format(DATE_P, "%M")
```

---

```r
> str(DATE_O)
 chr "2018-01-02"
> str(DATE_C)
 Date[1:1], format: "2018-01-02"

> str(DATE_O2)
 chr "2015-02-04 23:13:23"
> str(DATE_P)
 POSIXct[1:1], format: "2015-02-04 23:13:23"

> format(DATE_P, "%A")
[1] "수요일"
> format(DATE_P, "%S")
[1] "23"
> format(DATE_P, "%M")
[1] "13"
```

## 6.2 as & is로 strings 확인 및 변경
```r
x = c(1:10)
x1 = as.integer(x)
x2 = as.numeric(x)
x3 = as.factor(x)
x4 = as.character(x)

summary(x1)
summary(x2)
summary(x3)
summary(x4)

#is() 논리문으로써 para가 x인지 판단한다.

y = c("str", "str2", "str3", "str4")
is.integer(x)
is.numeric(x)
is.factor(y)
is.character(y)
```

---

```r
> summary(x1)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   1.00    3.25    5.50    5.50    7.75   10.00 
> summary(x2)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   1.00    3.25    5.50    5.50    7.75   10.00 
> summary(x3)
 1  2  3  4  5  6  7  8  9 10 
 1  1  1  1  1  1  1  1  1  1 
> summary(x4)
   Length     Class      Mode 
       10 character character

> is.integer(x)
[1] TRUE
> is.numeric(x)
[1] TRUE
> is.factor(y)
[1] FALSE
> is.character(y)
[1] TRUE
```

# 7. sample()을 통한 데이터 무작위 추출
```r
#sample(데이터 추출 범위, n, replace=False or True) / False는 비복원 추출 의미
S1 = sample(1:45, 6, replace=FALSE)
print(S1)
set.seed(123) # 추출한 값을 고정해야할때 사용
S2 = sample(1:45, 6, replace=FALSE)
print(S2)
```

---

```r
> print(S1)
[1] 34 12  3 30 45  2
> print(S2)
[1] 31 15 14  3 37 43
```

# 8. 조건(if)
```r
A = c(1:5)
if(7 %in% A)  # %in% : A에 7이 속해있는지 확인
{
  print("TRUE")
} else
{
  print("FALSE")
}
```

---

```r
[1] "FALSE"
```

# 9. 사용자 함수 생성
```r
Pluse_One = function(x){
  y= x+1
  return(y)
}
Pluse_One(3)
```

---

```r
> Pluse_One(3)
[1] 4
```