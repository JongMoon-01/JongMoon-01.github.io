---
layout: post
title: "AWS Deep Racer Car 경진대회"
date: 2025-11-27 10:00:00 +0900
categories: [AWS]
tags: [강화학습, AWS, 자율주행]
description: "자율주행, 강화학습"
---

# 1. 참여 이유
AWS DeepRacerCar는 기본적으로 차량의 이동을 강화학습으로 시뮬레이션해 자율 주행 능력을 끌어올리는 대회이다. 그래서 마침 AI도 이미지 처리, 음성 처리, 모델링 등등 본격적으로 배우며 강화학습도 배우고 실습해보면 괜찮겠다 싶어서 대회를 알아보다 마침 DSC 공유대학에서 해당 대회를 개최하게 되어 참여하게 되었다.

# 2. 팀원 구성
팀원은 간단하게 같은과 동기 2명, 타학과 학생 1명으로 구성하게 되었고 총 4명으로 출전했다.

# 3. 대회 목표
대회의 목표는 2가지로 나뉜다. 
- 온라인 부문 : 온라인 시뮬레이션에서 트랙 이탈률이 가장 적고 가장 빠르게 트랙을 완주한 실행이 가장 높은 점수를 받는다.
- 오프라인 부문 : 온라인에서 학습한 알고리즘을 가지고 실제 트랙에서 가장 빠르게 트랙을 완주한 차량이 가장 높은 점수를 받는다.

하지만 온라인의 배점이 오프라인 보다 크게 낮게 책정되어 오프라인에 더 큰 노력을 쏟아 고득점 하는 것이 1등을 하기에 좋은 전략이다. 온라인은 강화학습 알고리즘을 프로그래밍해 시뮬레이션 상에서 차량을 계속 주행시켜 가장 짧은 시간에 트랙을 완주시키게 하는 것이 목표이고, 오프라인은 해당 강화학습 알고리즘으로 학습된 모델을 실제 차량에 적용 시켜 실제 트랙에서도 가장 짧은 시간에 트랙을 완주시키게 하는 것이 목표이다. 이렇게 보면 온라인에서 1등할 경우 오프라인에서도 높은 성적을 거둘 것 같지만, 온라인에서 높은 성능을 보인 모델도 실제로 적용할 경우에는 제대로 작동하지 않는 경우가 많아 강화학습 시 파라미터를 잘 조절해야한다.

# 4. 대회 전략
해당 대회는 가장 기본적인 트랙인 AWS re:invent 2018 트랙으로 진행되었다. 또한 차량 모델도 모노 카메라만 지원하는 기본차량으로 진행되었다.

<center>
<img src="/assets/img/AWSDeepRacerCar_1.png" width="720" height=""/>
<p><b>[그림1]. re:invent 트랙 </b></p>
</center>

<center>
<img src="/assets/img/AWSDeepRacerCar_2.png" width="720" height=""/>
<p><b>[그림2]. AWS 차량 </b></p>
</center>

AWS DeepRacerCar의 주행은 강화학습에 대한 보상함수로 진행된다. 보상함수의 종류로는 다음과 같은 것이 있다.

- Position on track : 매개변수 x 및 y는 환경의 왼쪽 하단 모서리에서 측정한 차량의 위치를 미터 단위로 설명합니다.
- Heading : 방향 매개변수는 좌표계의 X축에서 시계 반대 방향으로 측정된 차량의 방향을 도 단위로 설명합니다.
- Waypoints : waypoints 매개변수는 트랙 중심을 따라 배치된 이정표의 정렬된 목록입니다.
- Track width : track_width 매개변수는 트랙의 너비(미터)입니다.
- Distance from center line : distance_from_center 매개변수는 트랙 중심에서 차량의 변위를 측정합니다.
- All wheels on track : all_wheels_on_track 매개변수는 부울(true/false)로, 차량의 네 바퀴 모두가 트랙 경계 안에 있으면 true이고 바퀴가 트랙 밖에 있으면 false입니다
- Speed : 속도 매개변수는 초당 미터로 측정된 차량의 관찰된 속도를 측정합니다.
- Steering angle : Steering_angle 매개변수는 도 단위로 측정된 차량의 조향 각도를 측정합니다.

나는 우선 레이싱의 ㄹ자도 몰랐기 때문에 카트라이더 프로게이머 문호준 선수가 레이싱 코스에서 Inline/Outline 전략을 설명해주는 영상을 시청한적이 있어서 DeepRacer Car에서도 적용해보기로 결심했다. 우선 나는 얻을 수 있는 정보를 확인한 후 Waypoint가 존재하는것을 보고 트랙을 7개 구역으로 쪼개서 해당 구역의 코너링때 Inline, Outline Course를 타며 가속력을 최대한 극대화 하는 방법으로 가기로 결정했다. 우선 waypoint로 구역을 나눠야하는데 이거는 직선 방정식도 알고 있고 직선 방정식의 한 점에서 떨어진 width/2만큼의 거리에 떨어진 두 점의 위치를 알 수 있기 때문에 수학으로 해결했다. 그래서 중앙선 waypoint를 전부 closet_waypoints에 넣고 closest_waypoints = params['closest_waypoints'] 해당 변수에는 waypoint = [(x1,y1), .... , (xn, yn)]의 형식으로 track center에 해당하는 점이 저장되어 있다. width는 width = params['width'] 변수를 통해 받는다. 이를 위에 예시 처럼 xn,yn의 점과 xn+1, yn+1의점의 기울기의 중간 지점에서의 수직 방정식과 width 만큼 거리가 차이나는 두 점의 집합을 구하라. 부호가 +인 집합과 -인집합을 따로 outline, inline 변수에 저장해서 강화학습으로 Outline과 Inline을 탈 수 있게끔 새로운 reward Parameter를 작성했다.

<center>
<img src="/assets/img/AWSDeepRacerCar_3.png" width="720" height=""/>
<p><b>[그림3]. 최단경로 설정 </b></p>
</center>

* ex. waypoint는 Track의 중앙선이 아닌 경계선을 점의 집합으로 표현한 데이터이다.

```python
print("Hello, GitHub Blog!")


import numpy as np

def find_outline_inline(params):
    closest_waypoints = params['closest_waypoints']
    width = params['width']
    
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

# Example usage:
params = {
    'closest_waypoints': [(1, 1), (2, 3), (4, 4)],  # example waypoints
    'width': 1.0  # example width
}

outline, inline = find_outline_inline(params)
print("Outline Points:")
print(outline)
print("Inline Points:")
print(inline)
```

```python
def reward_function(params):
    # Read input variables
    waypoints = params['waypoints']
    closest_waypoints = params['closest_waypoints']
    heading = params['heading']
    all_wheels_on_track = params['all_wheels_on_track']
    is_left_of_center = params['is_left_of_center']
    outline, inline = find_outline_inline(params)
    
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
    DIRECTION_THRESHOLD = 10.0

    if 0 <= closest_midpoints < 30:  # left
        if is_left_of_center:
            track_direction = math.atan2(
                inline[next_point_inline_idx][1] - inline[prev_point_inline_idx][1],
                inline[next_point_inline_idx][0] - inline[prev_point_inline_idx][0])
            track_direction = math.degrees(track_direction)

            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
            
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5
        else:
            reward = 1e-3


    elif 30 <= closest_midpoints < 40 and all_wheels_on_track:  # right
        if is_left_of_center:
            reward = 1e-3
        else:
            track_direction = math.atan2(
                outline[next_point_outline_idx][1] - outline[prev_point_outline_idx][1],
                outline[next_point_outline_idx][0] - outline[prev_point_outline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5

    elif 40 <= closest_midpoints < 53 and all_wheels_on_track:  # left
        if is_left_of_center:
            track_direction = math.atan2(
                inline[next_point_inline_idx][1] - inline[prev_point_inline_idx][1],
                inline[next_point_inline_idx][0] - inline[prev_point_inline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5
        else:
            reward = 1e-3

    elif 53 <= closest_midpoints < 63 and all_wheels_on_track:  # right
        if is_left_of_center:
            reward = 1e-3
        else:
            track_direction = math.atan2(
                outline[next_point_outline_idx][1] - outline[prev_point_outline_idx][1],
                outline[next_point_outline_idx][0] - outline[prev_point_outline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5

    elif 63 <= closest_midpoints < 80 and all_wheels_on_track:  # left
        if is_left_of_center:
            track_direction = math.atan2(
                inline[next_point_inline_idx][1] - inline[prev_point_inline_idx][1],
                inline[next_point_inline_idx][0] - inline[prev_point_inline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5
        else:
            reward = 1e-3

    elif 80 <= closest_midpoints < 90 and all_wheels_on_track:  # left
        if is_left_of_center:
            track_direction = math.atan2(
                inline[next_point_inline_idx][1] - inline[prev_point_inline_idx][1],
                inline[next_point_inline_idx][0] - inline[prev_point_inline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5
        else:
            reward = 1e-3

    elif 90 <= closest_midpoints < 105 and all_wheels_on_track:  # right
        if is_left_of_center:
            reward = 1e-3
        else:
            track_direction = math.atan2(
                outline[next_point_outline_idx][1] - outline[prev_point_outline_idx][1],
                outline[next_point_outline_idx][0] - outline[prev_point_outline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5

    elif 105 <= closest_midpoints < 120 and all_wheels_on_track:  # left
        if is_left_of_center:
            track_direction = math.atan2(
                inline[next_point_inline_idx][1] - inline[prev_point_inline_idx][1],
                inline[next_point_inline_idx][0] - inline[prev_point_inline_idx][0]
            )
            track_direction = math.degrees(track_direction)
            
            direction_diff = abs(track_direction - heading)
            if direction_diff > 180:
                direction_diff = 360 - direction_diff
                
            if direction_diff > DIRECTION_THRESHOLD:
                reward *= 0.5
        else:
            reward = 1e-3

    return float(reward)
```

# 5. 결과 및 후기
해당 전략으로 온라인 부문 1등을 하긴 했지만 강화학습 알고리즘을 작성하고 장렬하게 3시간 정도 취침을 때린 결과. 남은 팀원이 오프라인 경주 연습을 계속하겠다 했고 그 과정에서 오류가 발생해 8번 정도의 시험 주행 기회 중에 2번 정도밖에 수행하지 못했다.
그래서 오프라인 결과가 좋지 못하게 나와 최종적으로는 입상하지 못했다.. 그래도 선형대수학을 직접 적용해서 문제를 풀어보는 경험을 했는데 이는 AI 도움도 안받고 내 스스로 고민해서 직접 적용하고 결과를 보며 수정해 결국에 온라인 부문에서 1등을 달성한 결과를 얻어 굉장히 뜻깊은 경험으로 아직도 기억하고 있다.