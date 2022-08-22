# Lec-2

### ROS2 Foxy 설치 - Linux 20.04

## ROS2 사용 가능 환경

---

- Native Ubuntu Linux (O)
- Windows WSL을 통한 Ubuntu Linux
- Native Windows

## 편의성 프로그램

---

- **Terminator (다중 분할 터미널을 위한 인터페이스)**

→ [sudo = root 권한으로 실행한다는 뜻]

→ [apt(Advanced Package Tool) = 우분투의 패키지 관리자]

- **VScode : text editor**

→ Visual Studio의 문법 검사 기능

→ 여러가지 Extensions 제공

## 추가 설정들

---

- purge modemmanager 제거
- 사용자 계정 확인 ⇒ terminal에서 whoami 코드 입력

위에 사항들을 만족하였으면 

ROS2 Foxy 설치 → Gazebo11 설치 (Gazebo11이란 실습이 진행될 시뮬레이션이다.) → 그림자 X 경우→ 그래픽 드라이버 설치 → 재부팅 → 중간 점검 (오류 발생시 구글 검색) → **개발 환경 Setup**

- **개발 환경 Setup**

→ Colcon Build system 설치

→ workspace 생성

(ROS2는 종속성이 많다. 커멘트 라인 툴들을 통해서 프로그램을 키고 끌 수 있다. 다만 이것들을 사용하기 위해선 workspace를 사용해야한다.)

→ Colcon을 통해 빌드 진행

### 튜토리얼 패키지 실습 코드 (빌드 진행)

```jsx
$ git clone https://github.com/ros/ros_tutorials.git -b foxy-devel
$ ls ros_tutorials
```

여기서 오류가 발생한다면

![Screenshot_from_2022-08-07_23-08-30](https://user-images.githubusercontent.com/82068871/185958220-a6babadb-265b-4467-94cc-3561579eaf63.png)

위에 코드를 치면 된다.

### 종속성 확인 코드

```jsx
$ cd ../
$ rosdep install -i --from-path src --rosdistro foxy -y

```

여기서 오류가 발생한다면

![Screenshot from 2022-08-07 23-09-10.png](Lec-2%20fb1966ca54b84171a7d3c560d4e596a5/Screenshot_from_2022-08-07_23-09-10.png)

위에 코드를 치면된다.

---

# Alias

**Alias = 별명, 혹은 축약어이다.** 

---

[bashrc의 alias = 긴 리눅스 명령어의 앞 글자를 따서 축약어로 사용할 수 있게 한다.]

ex)

```jsx
alias eb='gedit ~/.bashrc'
alias sb='source ~/.bashrc'

alias cba='colcon build --symlink-install'
alias cbp='colcon build --symlink-install --packages-select'
alias killg='killall -9 gzserver && killall -9 gzclient && killall -9 rosmaster'
**alias rosfoxy='source /opt/ros/foxy/setup.bash && source ~/gcamp_ros2_ws/install/local_setup.bash'**

```

이고 이 단축키를 사용하기 위해서는 터미널에 `$ gedit ~/.bashrc`명령어를 입력 후 위에 내용을 붙여 넣기 해준다.

## 단축기

---

종료 = Ctrl + C

가로 분할 = Ctrl + shift + e

세로 분할 = Ctrl + shift + o

창 닫기 = Ctrl + shift + w

창 간 이동 = Alt + 화살표

# Turtlesim

---

### 실행 코드

(1번 터미널) : ros2 run turtlesim turtlesim_node

(2번 터미널) : ros2 run turtlesim turtle_teleop_key

(실행 화면)

![Screenshot from 2022-08-07 23-20-53.png](Lec-2%20fb1966ca54b84171a7d3c560d4e596a5/Screenshot_from_2022-08-07_23-20-53.png)
