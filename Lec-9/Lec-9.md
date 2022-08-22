# Lec-9

## Topic 프로그래밍 - python

# Subscriber Node 작성

---

- **laser_raw.py**
    
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
    
    # !/usr/bin/env/ python3
    
    import rclpy
    from rclpy.node import Node
    from sensor_msgs.msg import LaserScan
    
    class LaserSubscriber(Node):
    
        def __init__(self):
            super().__init__('laser_sub_node')
            queue_size = 10
            self.subscriber = self.create_subscription(
                LaserScan, 'skidbot/scan', self.sub_callback, queue_size
            )
            self.subscriber  # prevent unused variable warning
    
        def sub_callback(self, msg):
            self.get_logger().info(f'Raw Laser Data : {msg.ranges}')
    
    def main(args=None):
        rclpy.init(args=args)
    
        laser_subscriber = LaserSubscriber()
    
        rclpy.spin(laser_subscriber)
    
        laser_subscriber.destroy_node()
        rclpy.shutdown()
    
    if __name__ == '__main__':
        main()
    ```
    

코드에서 Publisher Node의 차이점

- Message Type 을 확인했을 때 → 2D Lidar는 sensor_msgs/msg/LaserScan 이라는 형식을 사용합니다.

이때 2D Lidar 란 사방으로 가는 빛을 이용해 물체와의 거리를 볼 수 있는 센서이다. (2차원)

```jsx
# 2D Lidar는 sensor_msgs/msg/LaserScan 이라는 형식을 사용합니다.
**from sensor_msgs.msg import LaserScan**

class LaserSubscriber(Node):

    def __init__(self):
        super().__init__("laser_sub_node")
        queue_size = 10
				self.subscriber = self.**create_subscription**(
            LaserScan, 'skidbot/scan', self.sub_callback, queue_size
        )
        self.subscriber # prevent unused variable warning

		# Topic을 통해 subscribe 할 때마다 이 함수가 실행됩니다. 
		# 그리고 두번째 매개변수인 msg에는 전달받은 Message가 담겨 있습니다.
    def **sub_callback**(self, msg):
				self.get_logger().info(f'Raw Laser Data : {msg.ranges}')
```

- (3차원)은 Rviz라는 툴을 사용한다.

### Create_Subscription

---

- `LaserScan` : Topic 통신에 사용될 Message Type
- `"skidbot/scan"` : 데이터를 Subscribe받을 Topic의 이름을 지정합니다. (topic list로 찾아지는 것들로 해야함)
- `self.sub_callback` : callback 함수
- `queue_size` : 대기열의 크기 (쓰레기통)

### **Queue_Size**

---

- 데이터를 몇 개나 보관할지를 결정하는 QoS옵션이다.

—> History옵션에 가깝다.

1. KEEP_LAST = 정해진 메세지 큐 사이즈 만큼의 데이터를 보관
2. KEEP_ALL = 모든 데이터를 보관

## LaserScan Message

---

- Message Type을 확인했을 때
- **LaserScan Type**
    
    ```jsx
    $ ros2 interface show sensor_msgs/msg/LaserScan
    # Single scan from a planar laser range-finder
    #
    # If you have another ranging device with different behavior (e.g. a sonar
    # array), please find or create a different message, since applications
    # will make fairly laser-specific assumptions about this data
    
    std_msgs/Header header # timestamp in the header is the acquisition time of
                                 # the first ray in the scan.
                                 #
                                 # in frame frame_id, angles are measured around
                                 # the positive Z axis (counterclockwise, if Z is up)
                                 # with zero angle being forward along the x axis
    
    float32 angle_min            # start angle of the scan [rad]
    float32 angle_max            # end angle of the scan [rad]
    float32 angle_increment      # angular distance between measurements [rad]
    
    float32 time_increment       # time between measurements [seconds] - if your scanner
                                 # is moving, this will be used in interpolating position
                                 # of 3d points
    float32 scan_time            # time between scans [seconds]
    
    float32 range_min            # minimum range value [m]
    float32 range_max            # maximum range value [m]
    
    float32[] ranges             # range data [m]
                                 # (Note: values < range_min or > range_max should be discarded)
    float32[] intensities        # intensity data [device-specific units].  If your
                                 # device does not provide intensities, please leave
                                 # the array empty.
    ```
    

이며 여기서 실질적으로 lidar data를 담고 있는 부분은 ranges이다.

이때 Ranges의 길이는 720의 list로, 0.25도의 분해능으로 Scan된 물체와의 거리를 저장한다.

그러므로 전방의 물체를 확인하려면 360번째의 리스트 성분을 봐야한다.

```python
		def sub_callback(self, msg):
				# ranges의 360번째 성분만 살펴보면 됩니다!!
        self.get_logger().info(f'Raw Laser Data : {msg.ranges}')
```

## 손쉬운 Subscribe

---

- ros2 topic echo 을 사용하면 된다. 하지만 가독성이 떨어지는 경우도 있다.

## Parking Example

---

- 로봇을 다양한 방향과 속도로 움직일 수 있다. (Publisher Node)
- 로봇에 달린 Lidar 센서로 전방의 물체와의 거리를 알 수 있다. (Subscriber Node)

이전에 배운 두 가지 방식으로 자동 주차 코드를 만들 수 있다.

# 메모

---

- 2d Lidar 센서 = 사방으로 가는 빛을 이용해 물체와의 거리를 볼 수 있는 센서 (2차원)
- ros에서는 rivz라는 걸 통해서 시각화를 매우 편리하게 해준다.
- subscriber 생성 코드  = self.create_subscription(message type, topic이름 지정→ ros2 topic list로 알아봄, callback 함수, 큐사이즈 =쓰레기통(적절한 데이터를 쌓아논다.)
- callback 함수는 지정해줘야함 self.~~~

ex) def callback함수 이름 (self, msg=하나의 매개변수를 지정해야하고 subscriber에 대해서 들어온 데이터에 해당한다.)

- 큐사이즈 = 데이터를 몇개나 보관할것인지

History옵션에 가깝다. ⇒ 데이터를 몇개나 보관할지 결정하는 Qos옵션

- ranges가 실질적인 lidar data를 담고 있다.

→ 720개의 list로 0.25도의 분해능으로 scan 된 물체와 거리를 지정

→ 90도 부분 360도, 0도 0도, 180도 부분 → 720도 부분(전방일때는 90도→ 360번째 list)