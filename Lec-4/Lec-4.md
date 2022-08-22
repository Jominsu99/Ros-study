# Lec-4

# Gazebo simulation

---

## 환경 구성

- **프로젝트 Clone && Build**
- **Gazebo 설정 파일에 사용 모델 경로 추가**

방법1 ⇒ 해당 위치에 파일을 직접 만들기

```jsx
$ cd ~/.gazebo
$ touch gui.ini

$ gedit gui.ini

# 다음 내용을 입력합니다.
[geometry]
x=0
y=0
[model_paths]
filenames=<your-config>
```

방법 2 ⇒ Gazebo를 통해 자동 생성하기

## Gazebo Launch

---

```jsx
$ cd ~/gcamp_ros2_ws/
$ rosfoxy
$ ros2 launch gcamp_gazebo gcamp_world.launch.py
```

을 통해 실행한다. 이때 Anaconda와 같은 가상환경일 경우 conda deactivate를 시켜줘야 실행이 된다.

## Gazebo 기본 사용법

---

- 마우스 ⇒ 좌표계 이동
- 마우스 힐 ⇒ 좌표계 회전, 확대, 축소
- 물체 삭제 ⇒ delete, 물체 추가 ⇒ insert, 물체 이동 ⇒ 왼쪽 2번째 아이콘, View 전환 ⇒ 오른쪽 끝 아이콘

![Screenshot_from_2022-08-08_17-22-48](https://user-images.githubusercontent.com/82068871/185959549-c77c4fb6-94fa-407e-8e99-3ee258c12816.png)


- 종료 ⇒ killg

## TeleOperation 실습

---

ROS에서는 키보드를 통한 Gazebo 상 로봇의 원격조종을 제공한다.

(패키지 설치)

`sudo apt install ros-foxy-teleop-twist-keyboard`

(커멘드 입력)

```jsx
# 새 터미널에서
$ ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r __ns:=/skidbot

Reading from the keyboard  and Publishing to Twist!
---------------------------
Moving around:
   u    i    o
   j    k    l
   m    ,    .

For Holonomic mode (strafing), hold down the shift key:
---------------------------
   U    I    O
   J    K    L
   M    <    >

t : up (+z)
b : down (-z)

anything else : stop

q/z : increase/decrease max speeds by 10%
w/x : increase/decrease only linear speed by 10%
e/c : increase/decrease only angular speed by 10%

CTRL-C to quit
```

위와 같이 키보드를 통해 움직일 수 있다.

## 메모

- ls = list를 불러올 수 있다.
- gedit  는 파일을 열고 수정 혹은 삭제
- cd ~~~는 그 위치에 파일로 이동
- colcon build 가 패키지 빌드하기
- mkdir -p ~/@@@ 은 그 위치에 workspace 생성
- rm -rf ~~ 는 소스코드 삭제
- git clone  은 소스코드 다운
- gazebo 는 중력 물리 시뮬레이션이 구동되어 있다.
- killg 는 gazebo에 관한 걸 모두 종료
- cbp 는 현재 위치에 관한 프로젝트 패키지 빌드
- pwd 명령어를 통해 현재 폴더의 위치를 알 수 있다. <폴더에서 마우스 우클릭 후 터미널 열기>
- Gazebo는 좌표체계를 갖는다.
- eb 는 .bashrc 를 연다.
