# 차선 유지 및 장애물 회피 기능을 갖춘 자율주행 젯봇

<img src="https://img.shields.io/badge/Jupyter Notebook-F37626?style=flat&logo=Jupyter&logoColor=white"/><img src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=Python&logoColor=white"/>



## 프로젝트 목표
- 차선을 인식하여 정확한 주행을 하도록 하고 장애물 조우시 회피 또는 정지로 충돌을 방지하고자 한다.
<img src="https://user-images.githubusercontent.com/4470398/204947699-4feb33cd-ab75-41f6-bedd-10b22eb2e961.gif" width="400" height="280"/>


> 주행예시



#### 차선 데이터를 통해 차선 인식 및 유지

![yahboom6](https://user-images.githubusercontent.com/113576244/206137498-552918c2-c908-4bb1-8b07-d7bd94e3ede3.gif)


[![Video Label](http://img.youtube.com/vi/HTF2EmKARus/0.jpg)](https://youtu.be/HTF2EmKARus)



#### 장애물 데이터를 통해 정지 및 회피




## 개발 환경 설정
```sh
sudo pip install jupyterlab
jupyter lab
```



## 코드 설명



1. 카메라 이미지 전처리
2. 도로 추종 및 충돌 회피를 위한 신경망 모델 실행
3. 장애물이 감지될 때마다 Jetbot이 도로 추종 및 정지를 수행할 수 있는 if 문을 확인
4. 대략적인 스티어링 값을 계산
5. 비례/파생 제어(PD)를 사용하여 모터를 제어



```c
import math

angle = 0.0
angle_last = 0.0
count_stops = 0
go_on = 1
stop_time = 10 # The number of frames to remain stopped
x = 0.0
y = 0.0
speed_value = speed_control_slider.value

def execute(change):
    global angle, angle_last, blocked_slider, robot, count_stops, stop_time, go_on, x, y, blocked_threshold
    global speed_value, steer_gain, steer_dgain, steer_bias
                
    steer_gain = steering_gain_slider.value
    steer_dgain = steering_dgain_slider.value
    steer_bias = steering_bias_slider.value
       
    image_preproc = preprocess(change['new']).to(device)
     
    #Collision Avoidance model:
    
    prob_blocked = float(F.softmax(model_trt_collision(image_preproc), dim=1).flatten()[0])
    
    blocked_slider.value = prob_blocked    
    stop_time=stopduration_slider.value
    
    if go_on == 1:    
        if prob_blocked > blocked_threshold.value: # threshold should be above 0.5
            count_stops += 1
            go_on = 2
        else:
            #start of road following detection
            go_on = 1
            count_stops = 0
            xy = model_trt(image_preproc).detach().float().cpu().numpy().flatten()        
            x = xy[0]            
            y = (0.5 - xy[1]) / 2.0
            speed_value = speed_control_slider.value
    else:
        count_stops += 1
        if count_stops < stop_time:
            x = 0.0 #set x steering to zero
            y = 0.0 #set y steering to zero
            speed_value = 0 # set speed to zero (can set to turn as well)
        else:
            go_on = 1
            count_stops = 0
            
    
    angle = math.atan2(x, y)        
    pid = angle * steer_gain + (angle - angle_last) * steer_dgain
    steer_val = pid + steer_bias 
    angle_last = angle
    robot.left_motor.value = max(min(speed_value + steer_val, 1.0), 0.0)
    robot.right_motor.value = max(min(speed_value - steer_val, 1.0), 0.0) 

execute({'new': camera.value})
```



## 한계점
학습된 모델로 충돌 회피를 테스트 하는 과정에서 지속적인 카메라 오류가 났고 해결하지 못했다.
