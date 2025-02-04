---
title: "R 기초문법 2"
date: 2022-05-09 18:37:00 +0900
categories: [R]
tags: [DataFrame,matrix]
---
# 1. Data review
```s
- 패키지 : ggplot2, ggthemes, plyr

- satisfaction_level : 직무 만족도
- last_evaluation : 마지막 평가점수
- number_project : 진행 프로젝트 수
- average_monthly_hours : 월평균 근무시간
- time_spend_company : 근속년수
- work_accident : 사건사고 여부(0: 없음, 1: 있음, 명목형)
- left : 이직 여부(0: 잔류, 1: 이직, 명목형)
- promotion_last_5years: 최근 5년간 승진여부(0: 승진 x, 1: 승진, 명목형)
- sales : 부서
- salary : 임금 수준
```

# 2. Data load
```r
HR = read.csv("C:/Users/Park/Data/HR_comma_sep.csv")
```
## 2.1 head 출력
```r
head(HR, 3)
```

## 2.2 check data strings(dtype)
```r
str(HR)
summary(HR)
```

## 2.3 convert data strings(데이터의 형태에 맞춰서 꼭 분석을 진행해야함)
```r
summary(HR$left)
HR$Work_accident=as.factor(HR$Work_accident)
HR$left=as.factor(HR$left)
HR$promotion_last_5years=as.factor(HR$promotion_last_5years)
```

# 3. Data preprocessing
## 3.1. satisfaction_level_group
```r
#para가 0.5보다 크다면 High아니면 Low
HR$satisfaction_level_group_1 = ifelse(HR$satisfaction_level > 0.5, 'High', 'Low')
HR$satisfaction_level_group_1 = as.factor(HR$satisfaction_level_group_1)
summary(HR$satisfaction_level_group_1)

HR$satisfaction_level_group_2 = ifelse(HR$satisfaction_level > 0.8, 'High',
                                       ifelse(HR$satisfaction_level > 0.5, 'Mid', 'Low'))
HR$satisfaction_level_group_2 = as.factor(HR$satisfaction_level_group_2)
summary(HR$satisfaction_level_group_2)
```

## 3.2 Salary && sales
```r
#salary가 high면서 sales가 IT인 교집합 생성
HR_High_IT = subset(HR, salary == 'high' & sales == 'IT')
print(xtabs(~ HR_High_IT$sales + HR_High_IT$salary))

HR_High_IT2 = subset(HR, salary == 'high' | sales == 'IT')
print(xtabs(~ HR_High_IT2$sales + HR_High_IT2$salary))
```

## 3.3 분석에 활용할 집계 데이터 생성(plyr 패키지 사용)
```r
SS=ddply(HR, #분석할 Data set 설정
         c("sales", "salary"), summarise, #집계 기준 변수 설정
         M_SF = mean(satisfaction_level), #컬럼명 및 계산 함수 설정
         COUNT = length(sales),
         M_WH = round(mean(average_montly_hours), 2))
```

## 3.4 ggplot 막대 그래프
```r
#(이산형 변수 하나를 집계 내는 그래프)
HR$salary =factor(HR$salary, levels = c("low", "medium", "high"))
ggplot(HR)
ggplot(HR, aes(x = salary))
ggplot(HR, aes(x=salary)) + geom_bar()
ggplot(HR, aes(x=salary)) + geom_bar(fill = 'royalblue')
ggplot(HR, aes(x=salary)) + geom_bar(aes(fill = salary))
ggplot(HR, aes(x=salary)) + geom_bar(aes(fill=left))
```

## 3.5 gglot histogram(연속형 변수 하나를 )
```r
ggplot(HR, aes(x=satisfaction_level)) + geom_histogram()
#col은 테두리 색을, fill은 채워지는 색을 바꿔줍니다.
ggplot(HR, aes(x=satisfaction_level)) + geom_histogram(binwidth = 0.01, col='red', fill = 'royalblue')
#ggplot Density plot(연속형 변수 하나를 집계 내는 그래프)
ggplot(HR, aes(x=satisfaction_level))+ geom_density()
#col은 테두리 색을, fill은 채워지는 색을 바꿔줍니다.
ggplot(HR, aes(x=satisfaction_level)) + geom_density(col='red', fill='royalblue')
```

## 3.6 Boxplot
```r
#박스플롯 : 이산형 변수에 따라 연속형 변수의 분포 차이를 표현해주는 2차원 그래프
#데이터 탐색과정(EDA)에서 매우 중요한 플롯(이산형 변수와 연속형 변수 한번에 표현)
ggplot(HR,aes(x=left, y = satisfaction_level)) + 
              geom_boxplot(aes(fill = left)) +
              xlab("이직여부") + ylab("만족도") + ggtitle("Boxplot") +
              labs(fill = "이직 여부")
ggplot(HR, aes(x=left, y=satisfaction_level)) + 
  geom_boxplot(aes(fill = left), alpha = I(0.4)) +
  geom_jitter(aes(col = left), alpha = I(0.4)) +
  xlab("이직여부") + ylab("만족도") + ggtitle("Boxplot2") +
  labs(fill = "이직 여부", col = "이직 여부")

ggplot(HR, aes(x=left, y = satisfaction_level)) + 
  geom_boxplot(aes(fill = salary), alpha = I(0.4), outlier.colour = 'red') +
  xlab("이직여부") + ylab("만족도") + ggtitle("Boxplot3") +
  labs(fill = "임금 수준")
```

## 3.7 Scatter
```r
#산점도 : 두연속형 변수의 상관관계를 표현해주는 2차원 그래프
#분석에서 가장 활발하게 사용됨, 모델링 전에 변수 들의 관계 파악에 효과적
ggplot(HR, aes(x=average_montly_hours, y=satisfaction_level)) +
  geom_point()
# 간단한 색칠로 인사이트 발굴하기
ggplot(HR, aes(x=average_montly_hours, y=satisfaction_level)) +
  geom_point(aes(col = left)) +
  labs(col = '이직 여부') + xlab("평균 근무시간") +ylab("만족도")

```
