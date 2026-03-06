---
layout: post
title: "유튜브 콘텐츠와 자연어 처리를 통한 사용자의 이념편향 분석"
date: 2026-03-04 10:00:00 +0900
categories: [NLP]
tags: [YouTube, NLP, Bias, Recommendation]
description: "유튜브 추천 콘텐츠와 자연어 처리 기반 분석을 통해 사용자 정보 소비 패턴과 이념 편향을 분석한 연구"
---

# 0. 주제 선정
4학년때 교양수업으로 심리학 수업을 들으면서 중학생때 심리학자가 되고 싶었던 꿈도 살짝 떠오르고 논문 쓸 날도 가까워지니 그냥 일반적인 졸업논문 보다는 뭔가 사회적으로 기여할 수 있고 현재 사회적으로 크게 문제가 되고 있는 유튜브와 인문적으로 연계해서 논문을 한편 써보고 싶었다. 물론 인문과 공학간의 상관관계를 설명하기 위해서 정말 많은 시도를 해봐야해서 굉장히 고민을 많이 했다.

우선 사회 문제로 선택한 것은 Youtube의 문제이다. 나는 독서를 참 좋아했고 초중고 모두 점심시간마다 도서관에 박혀 있을 만큼 다독가 였다. 군대에서도 운동하면서 자기전에 틈틈히 책을 읽었고 근무 쉬는 시간에도 자주가서 읽었다. 물론 대학 복학하고 나서는 1년에 1~2번 읽을 만큼 적게 읽었다. 그리고나서 4학년에 논문을 쓰려니까 글을 쓰려고해도 뭘로 문단을 시작해야할지 어떤 논리 흐름을 잡아야할지 막막해 이게 책을 덜 읽고 Youtubue를 많이 시청했기 때문이라 생각해 Youtube 서비스 소비 습관을 집중적으로 조사했다.

# 1. 유튜브 추천 알고리즘과 이념 편향 분석 프로젝트 개요
Youtube 소비 습관을 어떻게 가공해야 Youtube 사용자에게 변화를 줄 수 있을까? 고민을 하다 피실험자의 유튜브 계정을 가져와 실험을 하려했지만 아무리 생각해도 자원자를 표본만큼 모으기 어려울거 같아 실험 계정을 2개 만들어서 이 계정을 이용해 유튜브의 추천 알고리즘이 사용자의 서비스 소비 습관을 편향성 있게 만들 수 있음을 증명해 보았다.

핵심 분석요소는 총 6개이다.
1. 추천 영상 데이터 수집
2. 텍스트 전처리
3. BERT 기반 주제 분류
4. SBERT 기반 의미 중심 단어 분석
5. WordCloud 시각화
6. 추천 알고리즘 편향 분석

전체 분석 흐름은 다음과 같다.
```
YouTube Main Feed
        ↓
영상 메타데이터 + 댓글 수집
        ↓
텍스트 전처리
        ↓
BERT Topic Classification
        ↓
Sentence-BERT Semantic Analysis
        ↓
WordCloud Visualization
        ↓
Algorithm Bias Analysis
```

실효성 검증은 다음과 같은 흐름을 가진다.
1. 사용자가 소비하는 and 노출되는 영상의 메타데이터 수집
2. 메타데이터를 분석해 중요 키워드, 카테고리 분석
3. 해당 키워드와 카테고리를 시각화해 사용자에게 자신이 무얼 주로 소비하고 어떤 콘텐츠에 자주 노출되는지 보고서 제공
4. 해당 보고서를 읽은 후 적당한 시간이 지난 후 사용자의 서비스 소비 습관이 변했는지 설문조사 및 다시 실험

# 2. 실험 설계
계정이 소비하는 영상의 메타데이터 수집은 두 계정을 이용해 수집했다.

- 실험 계정 구성

| 계정 | 시청 방식 |
|-----|----------|
| Account 1 | 다양한 카테고리 시청 |
| Account 2 | 정치 콘텐츠만 반복 시청 |

- 데이터 수집 방식
  - 매일 21:00
  - 유튜브 메인 추천 영상 250개 수집
  - 실험 기간 3일
- 수집 데이터
  - 영상 제목
  - 영상 설명
  - 조회수
  - 업로드 날짜
  - 상위 노출 댓글 50개
  - URL

## 2.1. 데이터 수집 시스템
추천 영상 수집은 다음 라이브러리를 사용했다

```python
Selenium
yt-dlp
YoutubeCommentDownloader
```

- 데이터 수집 파이프라인

```
YouTube 로그인
   ↓
메인 페이지 스크롤
   ↓
영상 링크 추출
   ↓
메타데이터 수집
   ↓
댓글 수집
   ↓
CSV 저장
```

- 핵심 코드

```python
video_links = scroll_down_to_count(driver, min_count=250)

writer.writerow([
    meta["upload_date"],
    meta["title"],
    meta["view_count"],
    meta["description"],
    comments_str,
    href
])
```

최종 데이터셋
- upload_date
- title
- view_count
- description
- comments
- url

## 2.2. 텍스트 전처리
수집한 메타데이터를 기반으로 해당 동영상이 어떤 카테고리에 속하는지 판별하기 위해 빈도 기반으로 분석을 진행하려다 빈도 기반으로 분석할 경우 정확도가 많이 떨어질거 같아 교수님과 상담을 진행한 후 우선 BERT 모델을 이용해 카테고리를 학습한 후 Sentence-BERT 기반 의미 분석을 이용해 문맥 기반 의미 분석을 진행했다.

영상 제목과 설명을 결합한뒤 형태소 분석을 수행했다.

- 사용 라이브러리

```python
KoNLPy
Okt
```

전처리 과정
1. 형태소 분석 및 불용어 제거
   - 영상의 제목과 설명을 결합한 텍스트에 대해 KoNLPy의 Okt 형태소 분석기를 사용해 형태소 단위로 분해하였다.
   - 커스텀 불용어 사전(custom_stopwords)을 구축하여 플랫폼 일반어, 포맷어, filler 어휘 등을 제거함으로써 주제 분류와 의미 분석의 정확도를 향상시켰다.
2. 텍스트 필드 구성
   - 최종적으로 텍스트 컬럼은 [제목] + [설명] 형태로 구성되었으며, 이후 분류 모델 및 임베딩 분석의 입력값으로 사용되었다.


```python
def clean_text(text):
    tokens = okt.morphs(text, stem=True)
    return " ".join([t for t in tokens if t not in custom_stopwords])
```

## 2.3. BERT 기반 주제분류
영상 콘텐츠를 자동 분류하기 위해 hugging Face 둘러보다 KLUE BERT 모델이 있어 해당 모델을 파인 튜닝해 12개의 주제를 학습 시켰다.

주제분류는 다음과 같은 과정을 거친다

1. 문장 임베딩 및 중심 문장 추출
   - 실험 계정 데이터에 대해 snunlp/KR-SBERT-V40K-klueNLI-augSTS를 사용하여 설명 텍스트의 문장 임베딩을 수행하였다.
   - 각 주제 클러스터의 평균 임베딩 벡터(center vector)를 계산하고, 해당 벡터와 가장 유사한 문장을 중심 문장으로 선정하였다.
2. 중심 단어 추출 및 가중치 부여
   - 중심 문장에서 명사만 추출한 후, 각 단어의 SBERT 임베딩을 계산하고 중심 벡터와의 코사인 유사도를 통해 중요도를 산출하였다.
3. WordCloud 시각화
   - 가중치가 부여된 단어들을 기반으로 WordCloud를 생성하였으며, 이를 통해 각 주제의 의미 중심 단어 변화 양상과 콘텐츠 다양성 축소 여부를 시각적으로 분석하였다.
4. 시계열적 패턴 해석 예시
   - 실험 1계정: 정치 주제 비중의 점진적 증가, 게임·음악 등 비정치 주제의 존재감 감소
   - 실험 2계정: 다양한 주제를 시청했음에도, 일부 토픽에서 언어 표현의 수렴 현상 확인

- 카테고리
  - 정치
  - 사회
  - 국제
  - 과학
  - IT
  - 문화
  - 스포츠
  - 건강
  - 환경
  - 게임
  - 생활
  - 음악

- 데이터셋
  - 3일동안 모은 약 1200개 유튜브 영상

- 모델
  - klue/bert-base

- 학습 코드

```python
import pandas as pd
from sklearn.preprocessing import LabelEncoder
from datasets import Dataset
from transformers import AutoTokenizer, AutoModelForSequenceClassification, Trainer, TrainingArguments
from konlpy.tag import Okt
 
custom_stopwords = set([
    # 일반 불용어
    "있습니다", "합니다", "입니다", "그리고", "하게", "하는", "같은", "정말", "이번", "오늘", "여러분", "저희", "여기", "때문", "그래서", "소개", "자세", "영상", "내용", "수록",
 
    # 포맷성 불용어
    "http", "https", "com", "www", "watch", "utm", "v", "feature", "channel", "subscribe", "click", "info", "link",
 
    # 플랫폼 일반
    "유튜브", "채널", "구독", "좋아요", "댓글", "공유", "링크", "영상", "시청", "공지", "업로드", "재생", "소개", "기록", "참고", "바로가기",
 
    # 중복 대명사
    "저", "저희", "우리", "너희", "당신", "그", "그녀", "그들", "누구", "무엇", "어디", "이것", "저것", "그것", "여기", "저기", "거기",
 
    # 뉴스 자주 등장 filler
    "제공", "예정", "상황", "중", "위해", "대해", "대한", "통해", "동안", "다시", "이후", "현재"
])
 
 
okt = Okt()
 
def clean_text(text):
    tokens = okt.morphs(text, stem=True)
    return " ".join([t for t in tokens if t not in custom_stopwords])
    
# 1. 데이터 로드
df = pd.read_csv("youtube_category_50_each.csv")
df["텍스트"] = (df["제목"].fillna("") + " " + df["설명"].fillna("")).apply(clean_text)
 
# 2. 라벨 인코딩
le = LabelEncoder()
df["label"] = le.fit_transform(df["주제"])
 
# 3. Dataset 변환
dataset = Dataset.from_pandas(df[["텍스트", "label"]])
dataset = dataset.train_test_split(test_size=0.2)
 
# 4. 토크나이저 적용
tokenizer = AutoTokenizer.from_pretrained("klue/bert-base")
 
def tokenize(example):
    return tokenizer(example["텍스트"], padding="max_length", truncation=True, max_length=256)
 
dataset = dataset.map(tokenize, batched=True)
dataset.set_format(type="torch", columns=["input_ids", "attention_mask", "label"])
 
# 5. 모델 정의
model = AutoModelForSequenceClassification.from_pretrained("klue/bert-base", num_labels=len(le.classes_))
 
# 6. 학습 설정
training_args = TrainingArguments(
    output_dir="./results",
    evaluation_strategy="epoch",
    save_strategy="epoch",
    logging_dir="./logs",
    num_train_epochs=10,
    per_device_train_batch_size=8,
    per_device_eval_batch_size=16,
    load_best_model_at_end=True
)
 
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=dataset["train"],
    eval_dataset=dataset["test"]
)
 
# 7. 학습 실행
trainer.train()
 
# 8. 저장
model.save_pretrained("./results")
tokenizer.save_pretrained("./results")
```

<center>
<img src="(/assets/img/GraduationPaper_1" width="720" height=""/>
<p><b>[그림1]. KLUE-BERT 학습 </b></p>
</center>

# 3. 실험 계정 수집 데이터 분석

- 실험 계정 구성
| 계정 | 시청 방식 |
|-----|----------|
| Account 1 | 다양한 카테고리 시청 |
| Account 2 | 정치 콘텐츠만 반복 시청 |

여기서 가장 중요한 점은 Account 2의 노출 동영상의 다른 카테고리도 어느정도 정치 편향에 오염이 되는지 확인하는 것이다.

- 실험 계정1 시청 주기 변화
  - 여러 카테고리를 포함한 분포를 지님 시간이 지나도 어느정도 노출이 많이된 카테고리의 빈도가 증가함을 확인.

<center>
<img src="/assets/img/GraduationPaper_2" width="720" height=""/>
<p><b>[그림2]. 실험 계정1 시청 주기 1일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_3" width="720" height=""/>
<p><b>[그림3]. 실험 계정1 시청 주기 2일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_4" width="720" height=""/>
<p><b>[그림4]. 실험 계정1 시청 주기 3일차 주제분포 결과 </b></p>
</center>

- 실험 계정2 시청 주기 변화
  - 시간이 지날수록 정치 카테고리만 급증하는 모습을 확인 가능

<center>
<img src="/assets/img/GraduationPaper_5" width="720" height=""/>
<p><b>[그림5]. 실험 계정2 시청 주기 1일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_6" width="720" height=""/>
<p><b>[그림6]. 실험 계정2 시청 주기 2일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_7" width="720" height=""/>
<p><b>[그림7]. 실험 계정2 시청 주기 2일차 주제분포 결과 </b></p>
</center>

## 3.1. 실험 계정 별 주제 중심 단어 시각화 (WordCloud)

문장 단위 의미 분석을 위해 snunlp/KR-SBERT-V40K-klueNLI-augSTS 모델을 사용하였다.
각 주제 클러스터에서 문장 임베딩을 계산한 뒤, 평균 벡터와 가장 유사한 중심 문장(center sentence)을 선택하고 해당 문장에서 핵심 명사를 추출하였다.

이후 추출된 명사 벡터와 중심 벡터 간 코사인 유사도를 계산하여 단어 중요도를 부여하고, 이를 기반으로 WordCloud를 생성하였다.
이를 통해 각 시점에서 추천 콘텐츠의 주제 중심 단어가 어떻게 변화하는지를 시각적으로 분석하였다.

실험 계정 1
- 실험 계정 1은 다양한 카테고리의 콘텐츠를 시청하도록 설계된 계정이다.
  - 1일차에는 음악과 게임 주제에서도 비교적 뚜렷한 중심 단어가 형성되었으며, 시간이 지남에 따라 주제별 단어 분포가 비교적 균형 있게 변화하였다.
  - 정치 및 사회 주제 WordCloud에서는 강한 정치적 키워드보다는 정치 인물 중심의 단어들이 주로 등장하였다.
  - 문화 주제 WordCloud에서는 실험 계정 2와 달리 영화 관련 키워드가 다수 등장하며 콘텐츠 주제가 비교적 다양하게 분포하였다.

<center>
<img src="(/assets/img/GraduationPaper_8" width="720" height=""/>
<p><b>[그림8]. 실험 계정2 시청 주기 1일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_9" width="720" height=""/>
<p><b>[그림9]. 실험 계정2 시청 주기 2일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_10" width="720" height=""/>
<p><b>[그림10]. 실험 계정2 시청 주기 2일차 주제분포 결과 </b></p>
</center>

실험 계정 2
- 실험 계정 2는 정치 관련 콘텐츠만 반복적으로 시청하도록 설계된 계정이다.
  - 초기부터 추천 영상의 상당수가 정치 관련 콘텐츠로 구성되었다.
  - 특히 사회 및 문화 주제 WordCloud에서도 특정 정당이나 정치 세력을 직접적으로 지칭하는 강한 정치적 키워드가 등장하였다.
  - 이는 추천 알고리즘이 정치 콘텐츠 시청 패턴을 학습하면서 다른 주제 영역에서도 정치적 프레이밍을 강화하는 방향으로 작동했음을 보여준다.

<center>
<img src="/assets/img/GraduationPaper_11" width="720" height=""/>
<p><b>[그림11]. 실험 계정2 시청 주기 1일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_12" width="720" height=""/>
<p><b>[그림12]. 실험 계정2 시청 주기 2일차 주제분포 결과 </b></p>
</center>

<center>
<img src="/assets/img/GraduationPaper_13" width="720" height=""/>
<p><b>[그림13]. 실험 계정2 시청 주기 2일차 주제분포 결과 </b></p>
</center>

# 4. 더 해보고 싶었던 것들
이번 연구에서는 유튜브 추천 알고리즘이 사용자에게 어떤 콘텐츠를 노출시키는지에 대한 구조적 분석에 집중하였다.
하지만 분석을 진행하면서 단순히 추천 패턴을 확인하는 것에서 더 나아가 사용자의 인식 변화까지 측정할 수 있는 실험 구조도 설계해볼 수 있겠다는 생각이 들었다.

그래서 후속 연구로 다음과 같은 방향을 구상하였다.

## 4.1 정보 소비 성향 진단 시스템 (디지털 소비 MBTI)

연구 과정에서 사용자 정보 소비 패턴을 정량적으로 설명하기 위해 4가지 축 기반 지표 모델을 설계하였다.
이 모델은 사용자가 어떤 방식으로 정보를 소비하는지를 다음 네 가지 관점에서 분석한다.

| 기준 축 | 양극 개념 | 설명 |
|---|---|---|
| 인식 다양성 | D (Diverse) vs R (Repetitive) | 다양한 주제 콘텐츠를 탐색하는지, 특정 이슈만 반복 소비하는지 |
| 정서 반응 경향 | N (Neutral) vs E (Emotional) | 감정적 언어 반응이 적은지, 강한 감정 반응 중심인지 |
| 이념 선택성 | B (Balanced) vs P (Partisan) | 콘텐츠 소비가 이념적으로 균형적인지, 특정 진영 중심인지 |
| 메타인지 성향 | S (Self-aware) vs A (Algorithm-accepting) | 알고리즘 영향에 대한 자각이 있는지, 추천을 그대로 수용하는지 |

예를 들어 다음과 같은 지표를 계산할 수 있다.
- 최근 추천 영상 중 정치 콘텐츠 비율
- 댓글의 감성 극성 점수
- 추천 콘텐츠의 이념 분포
- 알고리즘 영향에 대한 사용자 인식

이러한 데이터를 시각화하면 사용자는 자신의 정보 소비 패턴을 하나의 유형으로 확인할 수 있다.

예시

```
DRBA
REPA
DNBS
```

이처럼 개인의 정보 소비 패턴을 “디지털 소비 MBTI” 형태로 표현하면
사용자가 자신의 정보 편향 가능성을 인식하고 스스로 점검할 수 있는 도구로 확장할 수 있다.

## 4.2 사용자 인식 변화 실험 (Experiment B)

본 논문에서는 추천 알고리즘 구조 분석을 중심으로 연구를 진행했지만,
실제로는 사용자의 인식 변화까지 측정하는 실험도 설계하였다.

이 실험은 다음 질문을 검증하기 위한 것이다.

```
추천 알고리즘에 장기간 노출되면 사용자의 언어 표현도 변화할까?
```

### <b>실험 설계</b>
- 참가자에게 두 시점에서 동일한 과제를 수행하도록 한다.

| 시점 | 실험 내용 |
|---|---|
| A 시점 (실험 전) | 사용자의 정보 소비 패턴을 알려주지 않은 상태에서 특정 사회 이슈를 묘사하도록 한다. |
| B 시점 (실험 후) | 자신의 정보 소비 성향 지표를 확인한 뒤 1주일 동안 유튜브 콘텐츠를 시청한 후 동일한 이슈를 다시 묘사하도록 한다. |

- 분석 항목
두 시점의 텍스트를 비교하여 다음 요소를 분석한다.

1. 사용 단어 변화

예시 키워드

```
자유
편향
선동
공정
```

특정 정치적 프레이밍 단어 사용 여부 변화

2. 감성 표현 변화(BERT 기반 감성 분석)
   - 긍정 / 부정 감정 비율
   - 감정 강도

3. 문장 구조 변화

예시

```
단정형 문장 → 의문형 문장 증가
```

또는

```
강한 주장 → 완화된 표현
```

### 기대 효과
이 실험을 통해 다음을 확인할 수 있다.

- 알고리즘 노출이 언어 표현에 미치는 영향

- 정보 소비 패턴과 인식 구조의 관계

- 메타인지 피드백이 편향 인식을 줄이는지 여부

즉 단순히 추천 알고리즘을 분석하는 연구에서 더 나아가
알고리즘이 인간의 인식 구조에 미치는 영향까지 탐구할 수 있는 연구 방향이 된다.

# 5. 참고문헌

- 강정한, 김선희, 오세욱. (2023). 한국인의 유튜브 뉴스 이용과 확증편향성. 서울대학교 아시아연구소.

-  김동규. (2021). 텍스트 마이닝 기법을 이용한 유튜브 추천 알고리즘의 필터버블 현상 분석. 한국콘텐츠학회논문지, 21(5), 1–9. https://doi.org/10.5392/JKCA.2021.21.05.001

- 김선희 외. (2018). 사회통합 실태 진단 및 대응 방안 연구(V) (연구보고서 2018-30). 한국보건사회연구원.

-  김선희 외. (2023). 통합 실태 진단 및 대응 방안(Ⅹ): 공정성과 갈등 인식 (연구보고서 2023-37). 한국보건사회연구원.

-  박동균. (2015). 소셜미디어 시대, 사회갈등의 재탐색: ‘온라인 사회갈등’ 사례를 중심으로. 21세기정치학회보, 25(1), 271–303.

-  사회통합연구팀. (2024). 사회갈등에 대한 한국인의 인식 변화와 시사점 (보건복지 Issue & Focus 제452호). 한국보건사회연구원.

-  언론진흥재단. (2024). 2024 소셜미디어 이용자 조사 주요 결과 발표. 한국언론진흥재단.

-   인사이트코리아·동아일보·리서치앤리서치. (2024). [2024 세대인식조사] 세대갈등 및 다른 세대에 대한 인식. 공동조사보고서.

-  Pariser, E. (2011). The filter bubble: What the Internet is hiding from you. Penguin Press.

-  Tufekci, Z. (2018, March 10). YouTube, the great radicalizer. The New York Times. https://www.nytimes.com/2018/03/10/opinion/sunday/youtube-politics-radical.html

-  [YouTube, The Great Radicalizer? (2022, Haroon et al.)] arXiv:2203.10666 [cs.CY]
