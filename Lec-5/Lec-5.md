# Lec-5

## ROS2 Node, Package와 기본 커멘드

# Node란?

---

로봇 개발을 위해 로봇을 구성하는 센서들, 그리고 이들 센서 데이터를 통해 지속적으로 판단하고 동작해야 하는 시스템이 필요하다.

**Ex) 카메라를 통한 표지판 인식**

카메라 센서로 이미지를 받는다 → 받아들인 이미지가 다음 동작을 판단 → 판단한 결과를 가지고 실제 모터 제어

이때 각각의 기능을 수행하는 Node들이 있으며 서로 데이터를 주고받는 구조가 갖추어져 있다.

# Topic, Service, Action

---

- Node들이 서로 데이터를 주고 받기 위해 필요한 방식들이다.
- 각각은 서로 다른 특징을 갖고 있다.

# Node의 실행

---

- 단일 실행시 `ros2 run` + <패키지 이름> + <실행 프로그램 이름>

**위에 커멘드를 실행하기 전에 rosfoxy를 해야한다.**

- 실행 중인 Node들의 리스트 확인

```jsx
$ ros2 node list
```

위에 커멘드는 node들의 자세한 정보는 표시하지 않는다.

Ex) turtlesim

![Screenshot_from_2022-08-08_18-40-25](https://user-images.githubusercontent.com/82068871/185960166-2f5a083d-eddc-4f37-87d3-317508ecfc0d.png)


위와 같이 3개의 리스트들이 있는 걸 알 수 있다.

- 특정 Node의 정보 살펴보기

`**ros2 node info`** 의 명령어를 사용한다.

Ex) turtlesim

![Screenshot_from_2022-08-08_18-37-55](https://user-images.githubusercontent.com/82068871/185960205-2b401662-93a6-41fa-acff-78074742eb16.png)


위와 같이 topic, service, action, parameter 의 정보를 표시하고 있다.

# Rqt_Graph

---

실행중인 Node들의 어떠한 데이터가 오가는지 잘 전송되고 있는 지 같은 정보를 그래프로 **시각화**하여 볼 수 있는 툴이다.

(패키지 설치)

```jsx
# rqt 관련 패키지 설치
$ sudo apt install ros-foxy-rqt*
# 실행
$ rqt_graph
```

Ex) turtlesim

![Screenshot_from_2022-08-08_18-43-51](https://user-images.githubusercontent.com/82068871/185960242-036203cf-8f31-4477-9779-06f77e16b114.png)


여기서 알아두어야 할 점은

**동그라미 ⇒ 노드, 화살표 ⇒ 데이터 경로**

# Package

---

(rnanosaur의 패키지 예시)

![Screenshot_from_2022-08-08_18-46-40](https://user-images.githubusercontent.com/82068871/185960284-26a866e5-fedc-4714-867e-64fd3492c7f6.png)

- 파일 관점 ⇒ 관련 라이브러리, 모델링 파일, 설정 파일들을 모아둔 폴더
- 기능 관점 ⇒ 시뮬레이션, 하드웨어 관련, 모델링, 원격 조종 등으로 분리시킨 모듈

ex) drive ⇒하드웨어, description → urdf ⇒ 모델링

- `colcon build` 가 수행되는 빌드의 단위이기도 하다.

### Package 생성

---

(커멘드)

```jsx
$ ros2 pkg create --build-type ament_cmake  <패키지이름> --dependencies rclcpp <종속성> 
$ ros2 pkg create --build-type ament_python <패키지이름> --dependencies rclpy <종속성> 
```

C++일 경우 1번

python일 경우 2번

(Hello의 이름으로 C++ 패키지 생성)

![Screenshot_from_2022-08-08_21-54-47](https://user-images.githubusercontent.com/82068871/185960311-d96e803b-d521-4f4a-a768-0d116d15433d.png)

![Screenshot_from_2022-08-08_21-54-59](https://user-images.githubusercontent.com/82068871/185960330-29503cb1-a8cf-44a7-b3a3-13a019147b8e.png)



## 메모

---

- node들은 topic, service, action, parameter을 통해 서로 데이터를 주고 받을 수 있다. 각각은 다른 특징이 있다.
- ros2 run을 통해 node를 실행 할 수 있다.
- ros2 node list를 통해 실행중인 로드를 볼 수 있다.
- ros2 node info ~~을 통해 특정 Node의 정보를 알 수 있다. (topic, service, action, parameter)
- Rqt_Graph 특정 정보를 시각화하여 볼 수 있다. 디버깅도 가능하다.
- rqt는 시각화 도구들의 집합이다.
