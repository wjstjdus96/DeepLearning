# 차선 유지 및 장애물 회피 기능을 갖춘 자율주행 젯봇

<img src="https://img.shields.io/badge/Jupyter Notebook-F37626?style=flat&logo=Jupyter&logoColor=white"/><img src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=Python&logoColor=white"/>



## 프로젝트 목표
- 차선을 인식하여 정확한 주행을 하도록 하고 장애물 조우시 회피 또는 정지로 충돌을 방지하고자 한다.
<img src="https://user-images.githubusercontent.com/4470398/204947699-4feb33cd-ab75-41f6-bedd-10b22eb2e961.gif" width="400" height="280"/>


> 주행예시



#### 차선 데이터를 통해 차선 인식 및 유지
> 학습시킨 사진 첨부



#### 장애물 데이터를 통해 정지 및 회피
> 학습시킨 사진 첨부



## 개발 환경 설정
```sh
sudo pip install jupyterlab
jupyter lab
```



## 코드블럭 설명
```c
//```뒤에 자신이 원하는 언어 (생략 가능)
#include <stdio.h>
int main(void) {
  printf("Hello World!");
}
```



## 한계점
학습된 모델로 충돌 회피를 테스트 하는 과정에서 지속적인 카메라 인식 오류가 났고 해결하지 못했다.

> 오류 코드 첨부하기
