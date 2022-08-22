# Lec-11

## Topic 프로그래밍 C++

## Build & 예제 실행

---

(5초간 원운동후 정지)

```jsx
$ cbp cpp_topic_pkg

# Windows
> colcon build --symlink-install --packages-select cpp_topic_pkg
> install\setup.bat

# Terminal 1
$ ros2 launch gcamp_gazebo gcamp_world.launch.py
# Windows 
$ ros2 launch gcamp_gazebo gcamp_world_windows.launch.py

# Terminal 2
$ ros2 run cpp_topic_pkg cmd_vel_pub_node
```

# Publisher Node 작성

---

- **cmd_vel_pub.cpp**
    
    ```jsx
    // Copyright 2021 Seoul Business Agency Inc.
    //
    // Licensed under the Apache License, Version 2.0 (the "License");
    // you may not use this file except in compliance with the License.
    // You may obtain a copy of the License at
    //
    // [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)
    //
    // Unless required by applicable law or agreed to in writing, software
    // distributed under the License is distributed on an "AS IS" BASIS,
    // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    // See the License for the specific language governing permissions and
    // limitations under the License.
    
    #include <memory>
    #include "geometry_msgs/msg/twist.hpp"
    #include "rclcpp/rclcpp.hpp"
    
    class TwistPub : public rclcpp::Node {
    private:
      rclcpp::Publisher<geometry_msgs::msg::Twist>::SharedPtr m_pub;
      rclcpp::TimerBase::SharedPtr m_timer;
    
      geometry_msgs::msg::Twist m_twist_msg;
    
      void timer_callback() { move_robot(); }
    
    public:
      TwistPub() : Node("cmd_vel_pub_node") {
        RCLCPP_INFO(get_logger(), "Cmd_vel Pub Node Created");
    
        m_pub = create_publisher<geometry_msgs::msg::Twist>("skidbot/cmd_vel", 10);
        m_timer = create_wall_timer(std::chrono::milliseconds(100),
                                    std::bind(&TwistPub::timer_callback, this));
      }
    
      void move_robot() {
        m_twist_msg.linear.x = 0.5;
        m_twist_msg.angular.z = 1.0;
        m_pub->publish(m_twist_msg);
      }
    
      void stop_robot() {
        m_twist_msg.linear.x = 0.0;
        m_twist_msg.angular.z = 0.0;
        m_pub->publish(m_twist_msg);
    
        RCLCPP_INFO(get_logger(), "==== Stop Robot ====");
      }
    };
    
    int main(int argc, char **argv) {
      rclcpp::init(argc, argv);
    
      auto twist_pub = std::**make_shared**<TwistPub>();
    
      auto t_start = twist_pub->now();
      auto t_now = twist_pub->now();
    
      auto stop_time = 5.0;
    
      while ((t_now - t_start).seconds() < stop_time) {
        t_now = twist_pub->now();
        // rclcpp::**spin_some**(twist_pub);
        twist_pub->move_robot();
    
        RCLCPP_INFO(twist_pub->get_logger(), "%f Seconds Passed", (t_now - t_start).seconds());
      }
    
      twist_pub->stop_robot();
    
      rclcpp::shutdown();
    
      return 0;
    }
    ```
    

```jsx
#include "geometry_msgs/msg/twist.hpp"
#include "rclcpp/rclcpp.hpp"
```

- C++에서는 헤더를 include 로 한다. (snake_case 사용)

이때

1. snake_case = 모든 알파벳은 소문자를 사용하고 띄어쓰기는 _ 를 사용하여 구분
2. CammelCase = 띄어쓰기가 있는 연결부분에 다음 글자는 대문자를 사용

로 구분할 수 있다.

# create_publisher (함수)

---

```jsx
m_pub = create_publisher<geometry_msgs::msg::Twist>("skidbot/cmd_vel", 10);

...

m_twist_msg.linear.x = 0.5;
m_twist_msg.angular.z = 1.0;
m_pub->publish(m_twist_msg);
```

- **publisher를 생성할 때 사용한다.**
1. <> 안에 Message type 
2. 첫 번째 매개변수는 생성할 Topic의 이름
3. 두 번째 매개변수는 queue size

ex)create_publisher<Message type>(”Topic의 이름”, queue size)

- **m_pub->publish(m_twist_msg);**

⇒    m_pub을 통해 → publish 함수 호출 ( 보낼 메세지)

# Main 부

---

- main
    
    ```jsx
    int main(int argc, char **argv)
    {
      rclcpp::init(argc, argv);
    
    	// auto를 사용하면 아주 편합니다. 
      auto twist_pub = std::make_shared<TwistPub>();
    
    	// 시간을 다루는 부분이며 5초의 기간을 설정하고 있습니다.
      auto t_start = twist_pub->**now**();
      auto t_now = twist_pub->**now**();
    
    	auto stop_time = 5.0;
    
    	// 정해진 시간 동안만 publish하기 위해 spin_some을 사용합니다.
    	// 본 예제에서는 move_robot() 함수를 호출하였습니다. 
      while ((t_now - t_start).**seconds**() < stop_time)
      {
        t_now = twist_pub->now();
    		// rclcpp::spin_some(twist_pub);
        twist_pub->move_robot();
    
    		// 경과 시간을 콘솔로 출력하고 있습니다.
    		RCLCPP_INFO(twist_pub->get_logger(), "%f Seconds Passed", (t_now - t_start).seconds());
      }
    
      twist_pub->stop_robot();
    
      rclcpp::shutdown();
    
      return 0;
    }
    ```
    
1. init로 초기화
2. rclcpp shutdown()

위에 두개 코드만 신경 써주면 된다.

# ROS2 시간 API (C++)

---

- 단위는 `seconds` / `nanoseconds` 두 가지 종류가 있다.

`nanoseconds` = 1e9 =10의 9제곱

- now() = 현재 시간을 알 수 있고 `rclcpp::Time` 타입을 가진다.

```jsx
	// 시간을 다루는 부분이며 5초의 기간을 설정하고 있습니다.
  auto t_start = twist_pub->now();
  auto t_now = twist_pub->now();

	auto stop_time = 5.0;

	// 정해진 시간 동안만 publish하기 위해 spin_some을 사용합니다.
  while ((t_now - t_start).seconds() < stop_time)
  {
    t_now = twist_pub->now();
		twist_pub->move_robot();
```

이 때 while문 안에 spin_some함수를 사용할 수도 있다.

```jsx
while ((t_now - t_start).nanoseconds() < t_delta)
  {
    t_now = twist_pub->now();
    rclcpp::spin_some(twist_pub);
  }

  twist_pub->stop_robot();
```

- Spin함수를 사용하면 Node를 반복 실행 할 수 있으므로 적극 활용하자.

# Subscriber Node작성

---

- Publisher Node를 구성할 때와 마찬가지로 헤더는 include를 사용하고 snake_case를 사용한다.

```jsx
#include "rclcpp/rclcpp.hpp"
#include "sensor_msgs/msg/laser_scan.hpp"

rclcpp::Subscription<sensor_msgs::msg::LaserScan>::SharedPtr m_sub;
```

### using

- using 함수를 사용하여 축약어를 만들 수 있다.

```jsx
using LaserScan = sensor_msgs::msg::LaserScan;
```

### Class 내부

```jsx
class LaserSub : public rclcpp::Node
{
private:
	// using을 사용하도록 바꾸셔도 좋습니다!!
  rclcpp::Subscription<sensor_msgs::msg::LaserScan>::SharedPtr m_sub;

public:
  LaserSub() : Node("topic_sub_oop_node")
  {
		// 중요!! subscriber를 생성하는 방식을 유심히 보시기 바랍니다.
		// timer 생성 시 사용하였던 std::bind가 쓰이고 있으며
		// 이 부분 또한 익명함수로 대체 가능합니다.
    m_sub = this->**create_subscription**<sensor_msgs::msg::LaserScan>(
        "skidbot/scan", 10, std::bind(&LaserSub::sub_callback, this, std::placeholders::_1));
  }
	
	// subscribe 시마다 동작하게 될 callback입니다.
	// 들어오는 데이터도 SharedPtr 형식입니다.
  void sub_callback(const sensor_msgs::msg::LaserScan::SharedPtr msg)
  {
    RCLCPP_INFO(this->get_logger(), "I got %f", (msg->ranges)[360]);
  }
};
```

---

# create_subscription

---

- **subscription을 생성할 때 사용한다.**
1. <> 안에 Message type 
2. 첫 번째 매개변수는 생성할 Topic의 이름
3. 두 번째 매개변수는 queue size
4. 세 번째 매개변수는 callback함수를 전달

이때 Class 안에 있을 경우 `std::bind` 로 불러내야한다.

### LaserScan message

---

```jsx
$ ros2 interface show sensor_msgs/msg/LaserScan
...
**float32[] ranges**             # range data [m]
                             # (Note: values < range_min or > range_max should be discarded)
...
```

- C++ 에서는 ranges는 Vector형식 (python은 list 형식)

→ size(), for문 사용가능

```jsx
std::cout << (msg->ranges).size() << std::endl;

for (const auto& e : msg->ranges)
  std::cout << e << std::endl;
```

## Main 부

---

- subscriber는 반드시 spin을 통한 갱신이 있어야 최신 message를 수신할 수 있다.

```jsx
int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);
  rclcpp::**spin**(std::make_shared<LaserSub>());
  rclcpp::shutdown();

  return 0;
}
```

# Parking.cpp 예제

---

- 위에서 사용한 publish와 subscribe를 모두 사용해야한다.

```jsx
#include <memory>
#include "geometry_msgs/msg/twist.hpp"
#include "rclcpp/rclcpp.hpp"
#include "sensor_msgs/msg/laser_scan.hpp"

using Twist = geometry_msgs::msg::Twist;
using LaserScan = sensor_msgs::msg::LaserScan;
```

- Parking.cpp class 내부
    
    ```jsx
    class ParkingNode : public rclcpp::Node
    {
    private:
    	// publisher, subscriber가 모두 필요합니다.
      rclcpp::Publisher<Twist>::SharedPtr m_pub;
      rclcpp::Subscription<LaserScan>::SharedPtr m_sub;
    
      Twist m_twist_msg;
    
    public:
      ParkingNode() : Node("robot_parking_node")
      {
        RCLCPP_INFO(get_logger(), "Parking Node Created");
    		
    		// publisher와 subscriber를 모두 생성합니다.
        m_pub = create_publisher<Twist>("skidbot/cmd_vel", 10);
        m_sub = create_subscription<LaserScan>("skidbot/scan", 10,
                                               std::bind(&ParkingNode::sub_callback, this, std::placeholders::_1));
      }
    	
    	// subscribe 시마다 실행될 callback입니다.
      void sub_callback(const LaserScan::SharedPtr msg)
      {
        auto forward_distance = (msg->ranges)[360];
    
    		if (forward_distance > 0.8) {
          move_robot(forward_distance);
        } else {
          stop_robot();
          rclcpp::shutdown();
        }
      }
    
    	// 로봇 제어를 손쉽게 가능하도록 함수로 만들어 사용합니다.
      void move_robot(const float &forward_distance)
      {
        m_twist_msg.linear.x = 0.5;
        m_twist_msg.angular.z = 0.0;
        m_pub->publish(m_twist_msg);
    
        std::cout << "Distance from Obstacle ahead : " << forward_distance << std::endl;
      }
    
      void stop_robot()
      {
        m_twist_msg.linear.x = 0.0;
        m_twist_msg.angular.z = 0.0;
        m_pub->publish(m_twist_msg);
    
        RCLCPP_WARN(get_logger(), "Stop Robot and make Node FREE!");
      }
    };
    ```
    

Parking의 Node의 순서

1. 전방 장애물과의 거리를 laser scan으로 받은 뒤
2. 정해준 거리를 기준으로 정지와 주행 여부를 결정
3. 만약 정지면 rclcpp를 종료

```jsx
	// subscribe 시마다 실행될 callback입니다.
  void sub_callback(const LaserScan::SharedPtr msg)
  {
    auto forward_distance = (msg->ranges)[360];

		if (forward_distance > 0.8) {
      move_robot(forward_distance);
    } else {
      stop_robot();
      rclcpp::shutdown();
    }
  }
```

# 메모

---

- C++ 는 헤더를 include로 한다.
    
    (snake_case = 두개의 단어를 연결할 때 모든 알파벳은 소문자, 언더스코어 사용)
    
- cammel case = 연결 할 때는 띄어쓰기 있으면 대문자 (Message type, 코드에서 사용할 때)

ex) shared_ptr → SharedPtr

- template → <> 이거 사용

ex) Publisher<geometry_msgs::msg::Twist>::SharedPtr m_pub;

- get_logger를 사용하는 이유 : 현재 ROS 디버그 콘솔을 알 수 있다.(시간 , 어떤 레벨 디버깅 코드인지).
- **create_subscription**<sensor_msgs::msg::LaserScan>("skidbot/scan", 10, std::bind(&LaserSub::sub_callback, this, std::placeholders::_1));

→ <message type>(sub topic 이름, 큐사이즈, callback함수)

이때 callback 함수가 class 안에 정의가 됐으므로 std::bind로 묶어줘야함, 또 매개변수가  void sub_callback(const sensor_msgs::msg::LaserScan::SharedPtr **msg)**
**위에 처럼 하나 있으므로 std::placeholders::_1를 사용**

- subscribe는 Message를 계속 수신해야한다. (spin 사용)
- void = 정상적으로 반환하지만 호출자에게 결과 값을 제공하지 않는 함수의 결과를 위한 타입이다.