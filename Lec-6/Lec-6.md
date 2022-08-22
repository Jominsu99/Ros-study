# Lec-6

## ROS2 Launch, launch file 작성

# Launch

---

- 여러 Node들을 한번에 실행할 수 있다.
- `ros2 launch <패키지 이름> <launch 파일 이름>` 로 명령어가 구성되어있다.
- 기본적으로 Python 문법을 사용한다. Ex) launch.py
- **Ex) gcamp_world.launch.py**
    
    launch file의 제일 아래부분
    
    ```
    return LaunchDescription(
        [
            # robot state publisher allows robot model spawn in RVIZ
            robot_state_publisher_node,
            # start gazebo, notice we are using libgazebo_ros_factory.so instead of libgazebo_ros_init.so
            # That is because only libgazebo_ros_factory.so contains the service call to /spawn_entity
            ExecuteProcess(
                cmd=["gazebo", "--verbose", world, "-s", "libgazebo_ros_factory.so"],
                output="screen",
            ),
            # tell gazebo to spwan your robot in the world by calling service
            ExecuteProcess(
                cmd=[ "ros2", "service", "call", "/spawn_entity", "gazebo_msgs/SpawnEntity", spwan_args ],
                output="screen",
            ),
            ExecuteProcess(
                cmd=["ros2", "run", "rviz2", "rviz2", "-d", rviz],
    							output="screen"
            ),
        ]
    )
    
    ```
    

위에 코드를 실행시키면 세가지 작업이 동작한다

- gazebo 실행
- robot을 등장
- rviz를 실행

여기서 세가지 동작을 한꺼번에 실행 시켜주는 것이 launch이고 만약 개별적으로 하고 싶다면

Ex) rviz실행

```jsx
$ rosfoxy
$ ros2 run rviz2 rviz2
```

을 해주면 된다. 다른 2개도 개별적으로 실행시킬 수 있다. 하지만 과정이 매우 복잡하고 어렵다. ⇒ launch file을 사용하는 이유!!

# Launch file 작성 방식

---

원하는 파일 경로 ⇒ touch 를 사용하여 파일 생성 ⇒ gedit 을 사용하여 생성된 파일을 입력 및 수정 ⇒ 빌드 ⇒ lauch 실행

```jsx
$ cd ~/gcamp_ros2_ws/src/gcamp_ros2_basic/gcamp_gazebo/launch
$ touch first_launch.launch.py
$ gedit first_launch.launch.py
```

```jsx
$ cd ~/gcamp_ros2_ws
$ cbp gcamp_gazebo
$ ros2 launch gcamp_gazebo first_launch.launch.py
```

여기서 생성된 파일을 입력 및 수정 할 때 두 가지 방식이 있다.

## 1. ExecuteProcess방식

---

```jsx
import os

from launch import LaunchDescription
from launch.actions import ExecuteProcess, IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration

from launch_ros.actions import Node

# this is the function launch  system will look for
def generate_launch_description():

    # create and return launch description object
    return LaunchDescription(
        [
            ExecuteProcess(
                cmd=["ros2", "run", "rviz2", "rviz2"], output="screen"
            ),
        ]
    )
```

으로 return 타입인 LaunchDescription 안에 코드가 쓰인 걸 볼 수 있다.

즉 ExecuteProcess을 사용하여 `**cmd=["ros2", "run", "rviz2", "rviz2", "-d", rviz], output="screen"`** 과 같은 커멘드들을 하나 하나 입력해주는 것이다.

만약 여러가지 node를 실행하고 싶을 경우 ExecuteProcess를 추가해 코드를 작성하면 된다.

ex)

```jsx
    # create and return launch description object
    return LaunchDescription(
        [
            # robot state publisher allows robot model spawn in RVIZ
            robot_state_publisher_node,
            # start gazebo, notice we are using libgazebo_ros_factory.so instead of libgazebo_ros_init.so
            # That is because only libgazebo_ros_factory.so contains the service call to /spawn_entity
            ExecuteProcess(
                cmd=["gazebo", "--verbose", world, "-s", "libgazebo_ros_factory.so"],
                output="screen",
            ),
            # tell gazebo to spwan your robot in the world by calling service
            ExecuteProcess(
                cmd=[ "ros2", "service", "call", "/spawn_entity", "gazebo_msgs/SpawnEntity", spwan_args ],
                output="screen",
            ),
            ExecuteProcess(
                cmd=["ros2", "run", "rviz2", "rviz2", "-d", rviz], 
								output="screen"
            ),
        ]
    )
```

### 문법

- **`generate_launch_description` :** 모든 launch file에 빠지지 않고 제일 먼저 등장하는 함수이다. (이 이름을 가진 함수를 찾아들어가기 때문에)
- `**LaunchDescription` :** 기본적으로 list 구조를 가지며 어떤 Node들을 실행시킬지 기술해두는 곳이다. 다만 특정한 규칙으로 실행시킨 Node에 대한 옵션은 지정해주어야 한다.
- `**ExecuteProcess` :** 프로세스 하나를 실행하는 코드

→ cmd : 터미널에서 입력하는 커멘드를 그대로 실행하고자 할 때 사용된다. 입력은 list 형태로 주어지며, space를 기점으로 구분된다.

→ output : Error log등의 output이 출력되는 수단이다.

## 2. Node방식

---

```jsx
#!/usr/bin/env python3

import os

from launch import LaunchDescription
from launch.actions import ExecuteProcess, IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration

from launch_ros.actions import Node

# this is the function launch  system will look for
def generate_launch_description():

    turtlesim_node = Node(
        package='turtlesim',
        executable='turtlesim_node',
        parameters=[],
        arguments=[],
        output="screen",
    )

    turtlesim_teleop_node = Node(
        package='turtlesim',
        executable='draw_square',
        parameters=[],
        arguments=[],
        output="screen",
    )

    # create and return launch description object
    return LaunchDescription(
        [
            turtlesim_node,
            turtlesim_teleop_node,
        ]
    )
```

위에 코드처럼 return 타입인 LaunchDescription 안에는 정의된 Node만 있다. 

대신 위에 각각의 Node를 정의해주는 걸 볼 수 있는데 문법을 보면

### 문법

- **`Node` : Node 하나를 실행시킬 수 있는 옵션이다.**

→`**package` : 실행시킬 Node가 포함된 package를 선택**

→`**executable` : colcon build 후 실행 가능한 프로그램이 생성**

→`**parameters` : 매개변수 추가**

→`**arguments` , `name`, `remappings`**

# ExecuteProcess VS Node

---

- Node 방식이 launch 의미에 좀 더 부합한다
- 때에 따라 Node 방식으로 할 수 없는 경우가 있어 ExecuteProcess도 알아두어야 한다.
- Node가 ExecuteProcess보다 타이핑 양이 많다.

결론 : 둘 다 알아두어야 한다. (정답 X)

## 메모

---

- launch파일은 기본적으로 python 문법 사용 (ros1은 xml)
- ExecuteProcess ⇒ 여러개를 쓰면 여러가지 노드를 실행할 수 있다.
- launch file을 만들어 놓으면 두고두고 쓸 수 있다.
- touch 명령어 사용시 새로운 파일을 만들 수 있다.
- generate_launch_description 는 launch.py가 들어간 파일을 실행 시키려면 무조건 있어야한다.
- Return LaunchDescription도 마찬가지 =  어떤 Node들을 실행시킬지 쓴다.
- Node 코드안에

Package = 실행시킬 package

executable = 실행시킬 프로그램

parameter = yml, yaml로 끝난다. 폴더는 param, config이름 사용

output = screen

- node방식의 장점 LaunchDescription 안이 깔끔해진다.
- from으로 가져올때

→node는 launch_ros.actions

→executeprocess는 launch.actions

- launch_ros와 launch의 차이점은 launch는 ROS의 구애받지 않고 테스트를 수행하기 위해 코어 라이브러리에서 사용되지만 launch_ros는 ROS 특정 개념을 시작 및 테스트에 추가한다(종속성 활용).