# Lec-8

## Topic 프로그래밍 - python

# Publisher Node 작성

- (**cmd_vel_pub.py)**
    
    ```jsx
    # Copyright 2021 Seoul Business Agency Inc.
    #
    # Licensed under the Apache License, Version 2.0 (the "License");
    # you may not use this file except in compliance with the License.
    # You may obtain a copy of the License at
    #
    #     http://www.apache.org/licenses/LICENSE-2.0
    #
    # Unless required by applicable law or agreed to in writing, software
    # distributed under the License is distributed on an "AS IS" BASIS,
    # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    # See the License for the specific language governing permissions and
    # limitations under the License.
    
    # Referenced from Below Link
    # https://docs.ros2.org/foxy/api/rclpy/api/init_shutdown.html#rclpy.create_node
    
    # !/usr/bin/env/ python3
    from geometry_msgs.msg import Twist
    import rclpy
    from rclpy.node import Node
    
    class CmdVelPublisher(Node):
    
        def __init__(self):
            super().__init__('cmd_vel_pub_node')
            self.publisher = self.create_publisher(
                Twist, 'skidbot/cmd_vel', 10
            )  # queue size
            timer_period = 0.5  # seconds
            self.timer = self.create_timer(timer_period, self.publish_callback)
            self.get_logger().info(
                'DriveForward node Started, move forward during 5 seconds \n'
            )
    
        def publish_callback(self):
            twist_msg = Twist()
            twist_msg.linear.x = 0.5
            twist_msg.angular.z = 1.0
            self.publisher.publish(twist_msg)
    
        def stop_robot(self):
            stop_msg = Twist()
            stop_msg.linear.x = 0.0
            stop_msg.angular.z = 0.0
            self.publisher.publish(stop_msg)
    
    def main(args=None):
        rclpy.init(args=args)
    
        cmd_vel_publisher = CmdVelPublisher()
        start_time = cmd_vel_publisher.get_clock().now().to_msg().sec
        clock_now = start_time
        time_delta = 0
    
        while (clock_now - start_time) < 5:
            rclpystop_robot.spin_once(cmd_vel_publisher)
            clock_now = cmd_vel_publisher.get_clock().now().to_msg().sec
    
            time_delta = clock_now - start_time
            cmd_vel_publisher.get_logger().info(f'{time_delta} seconds passed')
    
        cmd_vel_publisher.stop_robot()
    
        cmd_vel_publisher.get_logger().info('\n==== Stop Publishing ====')
        cmd_vel_publisher.destroy_node()
    
        rclpy.shutdown()
    
    if __name__ == '__main__':
        main()
    ```
    

---

- Message Type 코드
- python인 rclpy 코드
- Node 방법을 쓰기 위한 코드

```jsx
import rclpy
from rclpy.node import Node

from geometry_msgs.msg import Twist
```

이때 Message Type은 ros2 info 명령어로 알 수 있다.

ex) **geometry_msgs/msg/Twist**  ⇒ **`from geometry_msgs.msg import Twist`**

## main문

---

- Node 초기 작업 - 1
- Node 생성물 생성 - 2
- Node를 반복 실행(spin) - 3
- get_logger().info를 통한 상태 확인 - 4
- 생성물 종료 - 5
- Node 종료 - 6

```jsx
def main(args=None):
		# 1
    rclpy.init(args=args)
		
		# 2
    cmd_vel_publisher = CmdVelPublisher()

		# 3
    **rclpy.spin(cmd_vel_publisher)**

		# 4 
    cmd_vel_publisher.get_logger().info('\n==== Stop Publishing ====')

		# 5
    cmd_vel_publisher.destroy_node()

		# 6
    rclpy.shutdown()
```

→ rclpy.spin = 계속해서 주기적으로 Node를 동작시킨다.

만약 특정 시간만큼만 동작 시킬라면

```jsx
def main(args=None):
		# 1
    rclpy.init(args=args)
		# 2
    cmd_vel_publisher = CmdVelPublisher()
		
    start_time = cmd_vel_publisher.get_clock().now().to_msg().sec
    clock_now  = start_time
		time_delta = 0

	
    while (clock_now - start_time) < 5:
				
        rclpy.**spin_once**(cmd_vel_publisher)
        clock_now = cmd_vel_publisher.get_clock().now().to_msg().sec

        time_delta = clock_now - start_time
        print(f'{time_delta} seconds passed')

    cmd_vel_publisher.stop_robot()
		# 4 
    cmd_vel_publisher.get_logger().info('\n==== Stop Publishing ====')
		# 5
    cmd_vel_publisher.destroy_node()
		# 6
    rclpy.shutdown()
```

→ rclpy.spin_once = 단 한 번만 spin시키기 위해 동작한다.

### 중요

- ROS2의 모든 개발은 Class 형태로 개발을 추천하고 모든 Class는 Node를 기본적으로 상속 받아야 한다. 이때 Node안에는 node 동작에 필수적인 기능들이 모두 구현되어 있다.
- 상위 Class가 구현해 둔 것을 추가 개발 없이 동일하게 사용할 수 있고, 여기서 상속 받은 Class를 자신만의 기능도 추가하여 사용 가능하다. (상속)

→ `super().__init__` = 상위 클래스를 상속

```jsx
class CmdVelPublisher(**Node**):

    def __init__(self):
				
        super().__init__("cmd_vel_pub_node")
				self.publisher = self.create_publisher(Twist, "skidbot/cmd_vel", 10)
				timer_period = 0.5  # seconds
        self.timer = self.create_timer(timer_period, **self.publish_callback**)

				
        self.get_logger().info(" DriveForward node Started, move forward during 5 seconds \n")
    
    def publish_callback(self):
		def stop_robot(self):
```

### Create_publisher

---

- `Twist` = Topic 통신에 사용될 Message Type
- `"skidbot/cmd_vel"` = Topic의 이름을 지정한다. (topic list로 찾아지는 것들로 해야함)
- `10` = 대기열의 크기 (쓰레기통)

### publish_callback, stop_robot 함수 지정

---

- Message Type을 확인 후 각각의 매개변수 지정

```jsx
		def publish_callback(self):
				# 상단 형식에 맞추어 Twist Message를 채워줍니다.

				# 전방 속도 0.5 / 각속도 1.0 => 원 운동
        twist_msg = Twist()
        twist_msg.linear.x  = 0.5
        twist_msg.angular.z = 1.0
        **self.publisher.publish(twist_msg)**

    def stop_robot(self):
        stop_msg = Twist()

				# 전방 속도 0.0 / 각속도 0.0 => 정지
        stop_msg.linear.x  = 0.0
        stop_msg.angular.z = 0.0
        self.publisher.publish(stop_msg)
```

# 메모

---

- python을 사용하기 위해선 반드시 rclpy를 import 해야함
- Node 방법을 쓰기 위해 rclpy.node import Node를 함
- 생성물 종료 destory_node(), Node종료 shutdown()
- ros2 run은 Node, ros2 launch는 launch
- rclpy.spin (하고자하는 node)= while 문과 같은 역할 (반복실행)
- spin_once =  단 한 번만 하고 중지
- get_logger()로 찍었을 때 [info] [clock time] [ 실행 Node]
- pulisher 생성 코드  = self.create_publisher(message type, topic이름 지정→ ros2 topic list로 알아봄, 큐사이즈 =쓰레기통(적절한 데이터를 쌓아논다.)
- topic=pulisher는 연속성이라 얼마나 자주 보낼것이냐를 지정해주는 코드 self.creat_timer(초 지정 함수ex) time=0.5, 무슨 함수를 반복 실행지ex)publish_callback)
- pulish_callback → 먼저 message type을 알아야함 (ros2 interface show ~~~~) →