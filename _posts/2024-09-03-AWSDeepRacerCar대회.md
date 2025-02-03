---
title: "AWS DeepRacerCar 해커톤"
date: 2024-07-25 02:23:00 +0900
categories: [AWS]
tags: [Cloud, Car, AI, Embedded-system]
---
# 1. 강화 학습이란?

<code>정답 환경</code>이 주어지지 않은 상황에서 <code>최적 행동의 시퀀스</code>를 찾아가는 방법을 말합니다.

강화 학습의 구성요소는 다음과 같습니다.

1. Agent : 환경안에서 행동을 취하고 보상을 획득하는 주체입니다.
2. Env : Agent의 정책을 결정하는 계 입니다.
3. Reward : 계 안에서 특정 행동에 대한 긍정적 또는 부정적 판단을 점수화 한 것입니다.
4. Action : 계 안에서 Agent가 취할 수 있는 작용 방식입니다. Action을 취할 떄마다 State of Env가 달라집니다.
5. Policy : 계 안에서 특정 상황을 마주쳤을 때 Agent가 어떤 행동을 취해야하는지 알려주는 집합입니다.
6. State : t 시점에서의 Env를 뜻합니다.

* Reward는 대게 한 순간의 즉각적인 reward뿐만 아니라 향후 선택에 대한 reward를 함꼐 고려합니다.(미래 상황에 대한 예측을 시도합니다.)

## 1.1. discount factor와 누적 보상(Return)

보상에 대한 값을 행동을 취하는 turn마다 discount factor를 적용시켜 최종적인 보상 값을 낮추는 방법입니다.

## 1.2. 가치 함수

가치 함수의 정의 방법은 두 가지가 있습니다.

### 1.2.1. 상태 가치 함수

V파이(s) = E^under/파이[G^under/t | Sunder/t = s]

### 1.2.2. 행동 가치 함수

## 1.2. Value-based와 Policy-based

가치 함수를 편향되지 않고 분산이 낮으며 빠르게 수렴하도록 만드는 방법을 소개합니다.

* 단 편향성과 분산성은 trade-off 관계이기 때문에 두 개 다 챙길 수는 없습니다.

value-based 방식에서는 Env안에서 같은 상황으로 판별되는 케이스가 여럿 존재하면 여러 케이스에 대해서 한 가지의 행동으로 편향될 수 밖에 없는 문제가 발생합니다.

### 1.2.1. Actor-cirtic Algorithm


# 2. AWS Simulation Prompt
## 2.1. 아이디어

아래 그림과 같이 Track mid point를 모두 알고 있고 track width또한 알고 있을때 track outline point와 track inline point를 구하는것이 목적이였습니다. 이를 구하기 위해서 간단하게
아래와 같은 과정을 거쳤습니다.

1. 직선의 수직선 구하기
2. 주어진 거리 d = width/2에서 점 찾기
3. 방향 벡터 및 정규화 및 위치 계산

<center>
<img src="https://github.com/user-attachments/assets/17935212-c9d1-4bda-84fc-fda5b835aae3" width="720" height=""/>
<p><b>[그림1]. track </b></p>
</center>

3과정을 거쳐 outline과 inline 좌표값을 모두 구하게 되었고 가장빠르게 트랙을 돌파하기 위해서는 아래 그림과 같은 코스를 타도록 강화학습 시켜주면 된다고 생각했습니다. 이를 구현하기 위해서

distance_from_center = params['distance_from_center']
all_wheels_on_track = params['all_wheels_on_track']
    is_left_of_center = params['is_left_of_center']

<center>
<img src="https://github.com/user-attachments/assets/2106e331-6ff3-4580-badc-a24891466ba9" width="720" height=""/>
<p><b>[그림2]. 최적화 코스 </b></p>
</center>




## 2.2. Source Code분석
import math
import numpy as np

def find_outline_inline(params):
    closest_waypoints = params['waypoints']
    width = params['track_width']
   
    outline = []
    inline = []

    # Iterate through each segment between consecutive waypoints
    for i in range(len(closest_waypoints) - 1):
        p1 = np.array(closest_waypoints[i])
        p2 = np.array(closest_waypoints[i + 1])
       
        # Calculate the midpoint between p1 and p2
        midpoint = (p1 + p2) / 2
       
        # Calculate the slope of the line between p1 and p2
        if (p2[0] - p1[0]) != 0:  # Avoid division by zero
            slope = (p2[1] - p1[1]) / (p2[0] - p1[0])
        else:
            slope = float('inf')  # Vertical line
       
        # Perpendicular slope calculation
        if slope != 0 and slope != float('inf'):
            perp_slope = -1 / slope
        elif slope == 0:
            perp_slope = float('inf')  # Horizontal line
        else:
            perp_slope = 0  # Vertical line
       
        # Calculate the direction vector for the perpendicular line
        if perp_slope == float('inf'):
            direction_vector = np.array([0, 1])  # Vertical line
        elif perp_slope == 0:
            direction_vector = np.array([1, 0])  # Horizontal line
        else:
            direction_vector = np.array([1, perp_slope])
       
        # Normalize the direction vector and scale it by the half width
        length = np.sqrt(direction_vector[0]**2 + direction_vector[1]**2)
        unit_vector = direction_vector / length
        offset_vector = unit_vector * (width / 2)
       
        # Calculate the two points that are width/2 away from the midpoint
        outline_point = midpoint + offset_vector
        inline_point = midpoint - offset_vector
       
        # Store the calculated points
        outline.append(outline_point)
        inline.append(inline_point)
   
    return np.array(outline), np.array(inline)

def reward_function(params):
    # Read input variables
    waypoints = params['waypoints']
    closest_waypoints = params['closest_waypoints']
    heading = params['heading']
    all_wheels_on_track = params['all_wheels_on_track']
    is_left_of_center = params['is_left_of_center']
    outline, inline = find_outline_inline(params)
    track_width = params['track_width']
    distance_from_center = params['distance_from_center']
    # Initialize the reward with a typical value
    reward = 1.0
   
    # Ensure the closest_waypoints indices are within bounds using modulo
    wp_count = len(waypoints)
    if wp_count == 0:
        return 1e-3  # minimal reward if no waypoints are present
   
    next_point_idx = closest_waypoints[1] % wp_count
    prev_point_idx = closest_waypoints[0] % wp_count

    # 'find_outline_inline' returns points for segments, so limit checks accordingly
    next_point_inline_idx = min(next_point_idx, len(inline) - 1)
    prev_point_inline_idx = min(prev_point_idx, len(inline) - 1)
    next_point_outline_idx = min(next_point_idx, len(outline) - 1)
    prev_point_outline_idx = min(prev_point_idx, len(outline) - 1)
   
    # Set Interval
    closest_midpoints = int((prev_point_idx + next_point_idx) / 2) % wp_count
   
    # Define a threshold for direction difference
    DIRECTION_THRESHOLD_1 = 10.0
    DIRECTION_THRESHOLD_2 = 40.0
    DIRECTION_THRESHOLD_3 = 40.0
    DIRECTION_THRESHOLD_4 = 30.0
   
    marker_1 = 0.11 * track_width
   
    # Give higher reward if the car is closer to center line and vice versa
    if distance_from_center <= marker_1 and all_wheels_on_track:
        if 0 <= closest_midpoints < 25:  # right
            if is_left_of_center:
                reward = 1e-1
            else:
                track_direction = math.atan2(
                    outline[next_point_inline_idx][1] - outline[prev_point_inline_idx][1],
                    outline[next_point_inline_idx][0] - outline[prev_point_inline_idx][0])
                track_direction = math.degrees(track_direction)
           
                direction_diff = abs(track_direction - heading)
                if direction_diff > 180:
                    direction_diff = 360 - direction_diff
               
                if direction_diff > DIRECTION_THRESHOLD_1:
                    reward *= 0.55
               
        elif 25 <= closest_midpoints < 82:  # left
            if is_left_of_center:
                track_direction = math.atan2(
                    inline[next_point_outline_idx][1] - inline[prev_point_outline_idx][1],
                    inline[next_point_outline_idx][0] - inline[prev_point_outline_idx][0])
                track_direction = math.degrees(track_direction)
           
                direction_diff = abs(track_direction - heading)
                if direction_diff > 180:
                    direction_diff = 360 - direction_diff
               
                if direction_diff > DIRECTION_THRESHOLD_2:
                    reward *= 0.55
            else:
                reward = 1e-1
               
        elif 82 <= closest_midpoints < 107:  # right
            if is_left_of_center:
                reward = 1e-1
            else:
                track_direction = math.atan2(
                    outline[next_point_inline_idx][1] - outline[prev_point_inline_idx][1],
                    outline[next_point_inline_idx][0] - outline[prev_point_inline_idx][0])
                track_direction = math.degrees(track_direction)
           
                direction_diff = abs(track_direction - heading)
                if direction_diff > 180:
                    direction_diff = 360 - direction_diff
               
                if direction_diff > DIRECTION_THRESHOLD_3:
                    reward *= 0.55
               
        elif 102 <= closest_midpoints < 120:  # left
            if is_left_of_center:
                track_direction = math.atan2(
                    inline[next_point_outline_idx][1] - inline[prev_point_outline_idx][1],
                    inline[next_point_outline_idx][0] - inline[prev_point_outline_idx][0])
                track_direction = math.degrees(track_direction)
           
                direction_diff = abs(track_direction - heading)
                if direction_diff > 180:
                    direction_diff = 360 - direction_diff
               
                if direction_diff > DIRECTION_THRESHOLD_4:
                    reward *= 0.55
            else:
                reward = 1e-1
               
        else:
            reward = 1e-4  # likely crashed/ close to off track
   
    return float(reward)