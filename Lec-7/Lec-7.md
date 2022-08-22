# Lec-7

## ROS2 Topic

# Moving Robot

---

(예제)

```jsx
$ rosfoxy
$ cbp py_topic_pkg

# Terminal 1
$ ros2 launch gcamp_gazebo gcamp_world.launch.py

# Terminal 2
$ ros2 run py_topic_pkg cmd_vel_pub_node 
```

동작 시 내부적으로 어떤 작업이 이루어졌냐면

![Screenshot from 2022-08-09 03-11-57.png](Lec-7%20f2e2c1e460574568a6e863e3e9179342/Screenshot_from_2022-08-09_03-11-57.png)

위에 그림처럼 pub_node가 cmd_vel이라는 topic을 통해 drive_contorller 에게 데이터를 보낸걸 알 수 있다. 여기서 중요한 점은

- publish ⇒ pub_node
- subscriber ⇒ drive_contorller
- topic ⇒ cmd_vel

# ROS2 Topic

---

- 데이터가 오고 간다는 것을 의미
- Node들 사이에 Message(데이터)가 오가는 길(Bus)의 이름이다.
- Publisher와 Subscriber로 나누어 송신, 수신자를 구분한다.
- 데이터 = Message, 데이터가 오가는 길 = Topic

# Topic Message

---

- 로봇 프로그래밍 시에는 다양한 센서 데이터가 필요
- 제어 데이터도 주고 받아야한다.
- 기본 제공되는 형태에 사용자가 직접 커스터마이징을 할 수 있다.
- `skidbot/cmd_vel` topic은  **geometry_msgs/msg/Twist** 형식의 message를 사용한다. (로봇의 선속도, 각속도를 포함)
- Topic에 따라 Message type을 잘 골라야한다.

**Message type 보는 법**

rosfoxy → ros2 node list → 찾고자 하는 노드 → ros2 node info / 찾고자 하는 노드 → Publishers, Subscribers 확인

**Message 구성 보는 법**

```jsx
$ **ros2 interface show** geometry_msgs/msg/Twist
```

# Topic Commands

---

- 실행중인 topic 리스트와 세부 정보를 알 수 있는 커멘드

```jsx
$ ros2 topic list
$ ros2 topic info /찾고자 하는 topic
```

- 커멘드를 사용해 Publish 하는 법

```jsx
$ **ros2 topic pub** **--rate 1** /skidbot/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.5, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.0}}"
$ **ros2 topic pub --once**  /skidbot/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

커멘드 순서 <어떠한 topic이름> + <message 타입 이름> + <실질적인 내용> (rate1은 1초마다, once는 한번만)

- Publish가 잘 되는 지 확인하는 법

```jsx
$ ros2 topic echo /알고자 하는 publish
```

## 메모

---

- 실질적인 데이터는 Message라고 한다.
- topic은 일대다 통신이다.
- topic은 지속적인 데이터를 보낼 때 많이 사용한다.
- 제어 데이터는 로봇의 생김새에 따라 많이 달라진다.
- Msg =message의 줄임말
- Twist = 선속도, 각속도를 포함한다. (vector3)
- ROS는 또 다른 ROS의 Message안에 들어갈 수 있다.
- ros2 topic info를 하였을 때 /parameter_events, /rosout 은 Publisher count에 카운트가 안된다.
- echo와 일반 rqt_graph의 차이 ⇒ graph는 세부적인 내용은 볼 수 없지만 echo는 세부적인 내용까지 볼 수 있다.(rqt에서도 확인 할 수 있다. plugins→ topic →topic monitor → 체크 → 꺽새 열기)