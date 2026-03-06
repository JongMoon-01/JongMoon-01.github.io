---
title: "V2X통신시스템 10주차 10-4"
date: 2025-05-13 02:23:00 +0900
categories: [V2X통신시스템]
tags: [Network, V2X, AutoMobile]
---

학습 내용[10-3] 동기화 오차의 추정 및 보상 (part 4) 샘플링 클럭 오차 영향 및 보상동영상
- Estimation techniques for STO
- Estimation techniques for CFO
- Effect of sampling clock offset and compensation method
    - <code>Effect of Phase OFfset in Sampling Clocks</code>
    - Effects of Frequency Offset in Sampling Clocks
    - Compensation for Sampling Clock Offset

# 1. Effect of Phase Offset in Sampling Clocks

## 1.1. Illustration of sampling clock phase offset
<img width="392" alt="Image" src="https://github.com/user-attachments/assets/7b28d0d1-62d9-4c10-aba4-eada8979c65a" />

## 1.2. STO can incorporate the effect of sampling clock phase offset
- Can be compensated as a part of STO

학습내용
- Estimation techniques for STO
- Estimation techniques for CFO
- Effect of sampling clock offset and compensation method
    - Effect of Phase OFfset in Sampling Clocks
    - <code>Effects of Frequency Offset in Sampling Clocks</code>
    - Compensation for Sampling Clock Offset

# 2. Effect of Frequencyt Offset in Sampling Clocks
## 2.1. Illustration of the frequency offset in sampling clock(SFO)

<img width="445" alt="Image" src="https://github.com/user-attachments/assets/76c08178-f0e3-4db6-8440-11f2c0414d45" />

- Sample loss or insertion of OFDM symbol may occur periodcally -> looks like OFDM symbol boundary is moving continuously(advanced or delayed)

## 2.2. Impact of SFO
- Frequency domain received signal
<img width="367" alt="Image" src="https://github.com/user-attachments/assets/a57bd2a9-cce1-4f79-b3f4-0306b4a96a1e" />

## 2.3. Practical considerations for the SFO
- With Tx/Rx oscillator accuracy of 10ppm, the D in above equation has value 20 * 10-6
- Therefore, the ICI caused by the SFO would be negligible
- But the moving OFDM boundary due to the SFO causes STO continuously -> FFT boundary and the phase rotation in the FD must be tracked

학습내용
- Estimation techniques for STO
- Estimation techniques for CFO
- Effect of sampling clock offset and compensation method
    - Effect of Phase OFfset in Sampling Clocks
    - Effects of Frequency Offset in Sampling Clocks
    - <code>Compensation for Sampling Clock Offset</code>

# 3. Compensation for Sampling Clock Offset

<img width="469" alt="Image" src="https://github.com/user-attachments/assets/04d06171-f5b9-4d53-9273-f04859783c09" />

- Comparison

<img width="425" alt="Image" src="https://github.com/user-attachments/assets/b8177be8-613e-4123-aebd-90a8c8d69d48" />
