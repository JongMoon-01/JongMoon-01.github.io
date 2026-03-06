---
layout: post
title: "복소수 신경망"
date: 2025-10-27 10:00:00 +0900
categories: [Complex]
tags: [Complex, Math, NN]
description: "뉴럴 네트워크, 복소수 신경망"
---

# 1. 궁금증
복수전공을 하면서 기존 전공지식과 다른 가장 크게 다른것이, 바로 복소수 공간을 다룬다는 점이였다. 우선 기본적으로 정보통신 분야에서는 전파를 복소수 공간에서 표현하기 때문에 문제해결을 위해 전파를 푸리에 변환으로 공간영역 -> 시간영역 Transformation(변환)을 자주 하게 된다. 이걸 보고 CNN을 공부했을때 인간의 시각 정보처리 과정을 트랜지스터로 구현한다면 현재의 CNN 과정을 따른다는 것을 생각해냈고 만약 인간의 뇌가 RGB 시각 이미지를 처리하는 것보다 푸리에 변환을 통해 주파수 영역으로 전환된 이미지를 더욱 직관적으로 해석할 수 있다면, CNN도 모델링을 np.float64 혹은 np.float32같은 자연수(실수공간) 쓰는 것보다 자연수 + 복소수 형태의 자료 형태(복소수 공간)으로 사상시킨 이미지를 넣을 경우 더 인식율이 높아지지 않을까? 라는 생각을 했다. 그래서 바로 토이프로젝트로 착수하게 됐다.

# 2. 연구자료 찾아보기
우선 숭실대학교쪽에서 복소수 신경망으로 이미지 처리를 했던 연구를 했기에 이걸 참고했고 여기서 PSK를 통해 클래스를 구분하는 아이디어를 생각했다. 그리고 인간의 뇌도 주파수 영역의 이미지를 처리할때 무슨 관련이 있는지 알아야하기 때문에 Hubel과 Wiesel 논문을 참고했다.

# 3. 뇌의 시각 정보 처리 방식(Hubel, Wiesel 논문 참고)
1. 눈의 망막(retina)는 광자 자극을 받아 공간적으로 분포된 빛의 세기 패턴(공간 영역 정보)를 전기 신호로 바꾼다.

2. 해당 신호는 시신경(Optic Nerve)를 거쳐 V1으로 들어간다.

3. V1 뉴런들은 전체 영상 중 일부 영역을 장소야(receptive field)로 옮겨 해당 영역내 특정 공간 주파수(spatial frequency)와 방향(orientation)에 민감하게 반응한다.

    * 즉, 뉴런마다 특정 패턴에 반응하는 뉴런이 있다는 뜻(ex. 어떤 뉴런은 수평으로 반복되는 무늬-저주파에 반응하거나, 다른 뉴런은 세밀한 세로줄 패턴-고주파에 반응한다.)

해당 연구의 결론을 말하자면 시각 피질(V1 Area)이 선과 방향에 민감한 계층적 뉴런 네트워크로 구성되어 있고, 인간의 시각 처리가 공간 주파수 분석(Fourier적 처리)에 기반한 체계적 과정임을 신경생리학적으로 증명했다는걸 찾았다.

## 3.1. 연구 주요 발견 요약

- 단순세포(Simple Cells)
    - 장소야가 명확한 경계와 방향성을 가짐.
    - 특정 선의 방향(orientation)과 위치에 반응
    - 이는 LGN(외측슬상체, lateral geniculate nucleus)의 여러 세포입력이 선형적으로 결합된 결과로 해석된다.
- 복합세포(Complex Cells)
    - 위치는 달라도 같은 방향의 선 자극이면 반응.
    - 움직이는 선에 특히 잘 반응 -> 운동 방향 선택성(direction selectivity) 존재.
    - 여러 단순세포의 출력을 통합해 동작하는 것으로 추정.
- 초복합세포(Hypercomplex cells)
    - 선의 길이가 너무 길면 반응이 약화됨 -> "Ended-stopped" 현상
    - 이는 경계나 모서리, 특정 패턴의 끝 부분을 감지하는 역할로 해석됨.

# 4. 모델링
우선 나는 궁금했던게 복소수 신경망을 통해서 주파수 이미지를 학습하면 해당 이미지의 RGB 이미지가 들어와도 뇌가 처리하는 것처럼 잘 처리할지 궁금 다음과 같이 학습시켰다.

MNIST 2D 이미지(float같은 실수)를 복소수 영역의 주파수 이미지로 변환하고 해당 주파수이미지는 수학적으로 정현파로 표현될 수 있으니 PSK 방식으로 매핑하는 방법을 택했다.
모델 학습은 MNIST를 주파수 이미지로 변환시켜서 학습하고 Test는 MNIST 이미지를 기존 RGB이미지로 넣어서 잘 분류하는지 실험했다.

해당 결과는 
다음과 같다 아웃풋 그래프 1 ~ 4

# 5. 시도해볼만한 것
우선 정확도가 69%쯤 나오니 이를 개선하기 위한 여러 기법을 넣을 수 있을 거 같다. 

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
from tensorflow.keras import layers, Model
from tensorflow.keras.datasets import mnist

# ----------------------------
# 0. 유틸: PSK 타깃, 평가 함수
# ----------------------------
def generate_psk_targets(labels, M=10):
    theta = 2 * np.pi * labels / M
    return np.stack([np.cos(theta), np.sin(theta)], axis=1).astype(np.float32)

def psk_predict(pred_vectors, M=10):
    theta = 2 * np.pi * np.arange(M) / M
    refs = np.stack([np.cos(theta), np.sin(theta)], axis=1)  # (M,2)
    sim = np.dot(pred_vectors, refs.T)                       # (N,M)
    return np.argmax(sim, axis=1)

# ----------------------------
# 1. 데이터
# ----------------------------
(x_train, y_train), (x_test, y_test) = mnist.load_data()
# 실험 빨리 돌리려면 샘플 축소, 필요시 늘리기
x_train, x_test = x_train[:10000] / 255.0, x_test[:2000] / 255.0
y_train, y_test = y_train[:10000], y_test[:2000]

y_train_psk = generate_psk_targets(y_train, M=10)
y_test_psk  = generate_psk_targets(y_test,  M=10)

# 채널 차원 추가 (공간 뷰)
x_train_spa = x_train[..., None].astype(np.float32)  # (N,28,28,1)
x_test_spa  = x_test[..., None].astype(np.float32)

# ----------------------------
# 2. 주파수 뷰 생성 함수
#    입력: (H,W) 또는 (H,W,1) 실수
#    출력: (H,W,3)  = (log|F|, cos(phi), sin(phi))
# ----------------------------
def to_freq_channels_tf(x):
    # x: (B,28,28[,1])
    if len(x.shape) == 4 and x.shape[-1] == 1:  # ← 단순 len으로 판단
        x = tf.squeeze(x, axis=-1)

    x_c = tf.cast(x, tf.complex64)
    F = tf.signal.fft2d(x_c)
    amp = tf.math.log1p(tf.abs(F))
    phase = tf.math.angle(F)
    cos_p, sin_p = tf.math.cos(phase), tf.math.sin(phase)
    freq = tf.stack([amp, cos_p, sin_p], axis=-1)
    return tf.cast(freq, tf.float32)


# 사전 변환(메모리 충분하면 권장; 아니라면 tf.data map으로 on-the-fly)
x_train_freq = to_freq_channels_tf(x_train_spa).numpy()
x_test_freq  = to_freq_channels_tf(x_test_spa).numpy()

# ----------------------------
# 3. 데이터셋
#    ((spa, freq), y_psk)
# ----------------------------
train_ds = tf.data.Dataset.from_tensor_slices(((x_train_spa, x_train_freq), y_train_psk)) \
    .shuffle(8192).batch(128).prefetch(tf.data.AUTOTUNE)
test_ds  = tf.data.Dataset.from_tensor_slices(((x_test_spa,  x_test_freq),  y_test_psk)) \
    .batch(256).prefetch(tf.data.AUTOTUNE)

# ----------------------------
# 4. 공유 인코더(가중치 공유)
#    입력 채널만 다르고 구조 동일 → 두 개의 입력 레이어에 같은 Encoder 모델을 적용
#    출력: L2-normalized 2D 임베딩 (PSK용)
# ----------------------------
def build_encoder(input_channels):
    inp = layers.Input(shape=(28,28,input_channels))
    x = layers.Conv2D(32, 3, padding='same', activation='relu')(inp)
    x = layers.MaxPool2D()(x)
    x = layers.Conv2D(64, 3, padding='same', activation='relu')(x)
    x = layers.MaxPool2D()(x)
    x = layers.Conv2D(128, 3, padding='same', activation='relu')(x)
    x = layers.GlobalAveragePooling2D()(x)
    x = layers.Dense(128, activation='relu')(x)
    x = layers.Dense(2)(x)
    out = layers.UnitNormalization(axis=-1)(x)  # L2 정규화 (유닛 서클)
    return Model(inp, out, name=f"encoder_{input_channels}ch")

# 하나만 만들고 두 입력에 같은 모델을 재사용하면 진짜 가중치 공유
shared_encoder = build_encoder(input_channels=3)  # 기준은 3채널(주파수 뷰)
# 공간 입력은 1채널 → 3채널로 올리기 위한 간단한 전처리 레이어(Conv 1x1 대체 가능)
spa_adapter = tf.keras.Sequential([
    layers.Input(shape=(28,28,1)),
    layers.Conv2D(3, kernel_size=1, padding='same', use_bias=False)  # 1ch -> 3ch
], name="spa_adapter")

# ----------------------------
# 5. 커스텀 모델: 일관성 + PSK 앵커 손실
# ----------------------------
class PhaseAttractionLoss(tf.keras.losses.Loss):
    def call(self, y_true, y_pred):
        # y_true, y_pred: (B,2), 이미 L2 normalize 상태 권장
        y_true = tf.linalg.l2_normalize(y_true, axis=-1)
        y_pred = tf.linalg.l2_normalize(y_pred, axis=-1)
        cos_sim = tf.reduce_sum(y_true * y_pred, axis=-1)
        return 1.0 - cos_sim

def cosine_distance(a, b):
    a = tf.linalg.l2_normalize(a, axis=-1)
    b = tf.linalg.l2_normalize(b, axis=-1)
    return 1.0 - tf.reduce_sum(a*b, axis=-1)

class DualViewPSKModel(tf.keras.Model):
    def __init__(self, spa_adapter, shared_encoder, lambda_consistency=0.5, **kwargs):
        super().__init__(**kwargs)
        self.spa_adapter = spa_adapter
        self.encoder = shared_encoder
        self.ph_loss = PhaseAttractionLoss()
        self.lambda_c = lambda_consistency
        self.loss_tracker = tf.keras.metrics.Mean(name="loss")
        self.loss_psk_spa = tf.keras.metrics.Mean(name="loss_psk_spa")
        self.loss_psk_freq = tf.keras.metrics.Mean(name="loss_psk_freq")
        self.loss_cons = tf.keras.metrics.Mean(name="loss_cons")

    def train_step(self, data):
        (x_spa, x_freq), y_psk = data
        with tf.GradientTape() as tape:
            # 공간 -> 3채널 어댑트 후 공유 인코더
            spa_3 = self.spa_adapter(x_spa)
            z_spa = self.encoder(spa_3, training=True)    # (B,2)

            # 주파수 뷰는 그대로 공유 인코더
            z_freq = self.encoder(x_freq, training=True)  # (B,2)

            # 손실들
            L_spa  = self.ph_loss(y_psk, z_spa)           # (B,)
            L_freq = self.ph_loss(y_psk, z_freq)          # (B,)
            L_cons = cosine_distance(z_spa, z_freq)       # (B,)

            L = tf.reduce_mean(L_spa + L_freq + self.lambda_c * L_cons)

        vars_ = self.trainable_variables
        grads = tape.gradient(L, vars_)
        self.optimizer.apply_gradients(zip(grads, vars_))

        # 로그용
        self.loss_tracker.update_state(L)
        self.loss_psk_spa.update_state(tf.reduce_mean(L_spa))
        self.loss_psk_freq.update_state(tf.reduce_mean(L_freq))
        self.loss_cons.update_state(tf.reduce_mean(L_cons))
        return {
            "loss": self.loss_tracker.result(),
            "psk_spa": self.loss_psk_spa.result(),
            "psk_freq": self.loss_psk_freq.result(),
            "cons": self.loss_cons.result(),
        }

    def test_step(self, data):
        (x_spa, x_freq), y_psk = data
        spa_3 = self.spa_adapter(x_spa)
        z_spa = self.encoder(spa_3, training=False)
        z_freq = self.encoder(x_freq, training=False)

        L_spa  = self.ph_loss(y_psk, z_spa)
        L_freq = self.ph_loss(y_psk, z_freq)
        L_cons = cosine_distance(z_spa, z_freq)
        L = tf.reduce_mean(L_spa + L_freq + self.lambda_c * L_cons)

        self.loss_tracker.update_state(L)
        self.loss_psk_spa.update_state(tf.reduce_mean(L_spa))
        self.loss_psk_freq.update_state(tf.reduce_mean(L_freq))
        self.loss_cons.update_state(tf.reduce_mean(L_cons))
        return {
            "loss": self.loss_tracker.result(),
            "psk_spa": self.loss_psk_spa.result(),
            "psk_freq": self.loss_psk_freq.result(),
            "cons": self.loss_cons.result(),
        }

    @property
    def metrics(self):
        return [self.loss_tracker, self.loss_psk_spa, self.loss_psk_freq, self.loss_cons]

# ----------------------------
# 6. 학습
# ----------------------------
model = DualViewPSKModel(spa_adapter, shared_encoder, lambda_consistency=0.5)
model.compile(optimizer=tf.keras.optimizers.Adam(1e-3))
history = model.fit(train_ds, validation_data=test_ds, epochs=10)

# ----------------------------
# 7. 평가: 테스트는 공간 뷰만으로 분류
# ----------------------------
# 공간 이미지를 어댑트 -> 공유 인코더 -> 2D 임베딩 -> 10PSK argmax
spa_3_test = model.spa_adapter(x_test_spa, training=False)
z_spa_test = model.encoder(spa_3_test, training=False).numpy()
y_pred = psk_predict(z_spa_test, M=10)
acc = np.mean(y_pred == y_test)
print(f"[TEST] Spatial-only inference Accuracy (10-PSK argmax): {acc:.4f}")

# ----------------------------
# 8. 임베딩 시각화
# ----------------------------
plt.figure(figsize=(6,6))
# 10-PSK 기준점
for i in range(10):
    plt.plot(np.cos(2*np.pi*i/10), np.sin(2*np.pi*i/10), 'ro')
plt.scatter(z_spa_test[:,0], z_spa_test[:,1], s=6, alpha=0.5)
plt.title("Spatial View Embedding on Unit Circle (Dual-View Consistency)")
plt.xlabel("Real"); plt.ylabel("Imag")
plt.gca().set_aspect('equal'); plt.grid(True)
plt.show()
```

