# Lec-10 (C++)

## Node 프로그래밍 C++

C++ 언어 이용

# 예제 실행

---

→ 패키지 빌드

```jsx
$ rosfoxy
$ cbp cpp_first_pkg
```

→ 예제 실행

```jsx
# New Terminal 
$ ros2 run cpp_first_pkg simple_node
[INFO] [1621571601.351868900] [simple_node]: Logger Test

$ ros2 run cpp_first_pkg simple_loop_node
[INFO] [1621571623.628317300] [simple_loop_node]: Simple Loop Node
[INFO] [1621571624.137430000] [simple_loop_node]: Simple Loop Node
[INFO] [1621571624.637388900] [simple_loop_node]: Simple Loop Node
...

$ ros2 run cpp_first_pkg simple_oop_node
[INFO] [1621571658.158510000] [simple_oop_node]: I am Simple OOP Example, count : 7340132
[INFO] [1621571658.658082100] [simple_oop_node]: I am Simple OOP Example, count : 7340133
[INFO] [1621571659.152032000] [simple_oop_node]: I am Simple OOP Example, count : 7340134
...

$ ros2 run cpp_first_pkg lifecycle_node
[WARN] [1621571766.599589500] [simple_oop_node]: Node Constructor
[INFO] [1621571767.102470700] [simple_oop_node]: I am Simple OOP Example, count : 1
[INFO] [1621571767.600820400] [simple_oop_node]: I am Simple OOP Example, count : 2
[INFO] [1621571768.022392500] [rclcpp]: signal_handler(signal_value=2)
[INFO] [1621571768.023429800] [simple_oop_node]: ==== Spin Done ====
==== After Shutdown ====
[WARN] [1621571768.025122300] [simple_oop_node]: Node Destructor
```

- `simple_node` :  Node의 생성 후 Log를 출력하는 예제입니다.
- `simple_loop_node` : Node의 생성 후 주기적으로 Log를 출력하는 예제입니다.
- `simple_oop_node` :  Node Composition을 위한, 그리고 좀 더 유연한 ROS2 프로그래밍을 위해 상속을 통한 Node 생성 예제
- `lifecycle_node` : Node가 생성되고 소멸되는 시점과 rclcpp이 시작되고 종료되는 시점 예제

# Node의 생성

---

- **simple.cpp**
    
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
    
    #include "rclcpp/rclcpp.hpp"
    
    int main(int argc, char **argv) {
      rclcpp::init(argc, argv);
      auto node = rclcpp::Node::make_shared("simple_node");
    
      RCLCPP_INFO(node->get_logger(), "Logger Test");
    
      rclcpp::shutdown();
      return 0;
    }
    ```
    
- 모든 Node가 포인터의 형식을 갖는다.
- `shared_ptr` 를 사용하여 메모리 누수를 막기 위해 파생된 모든 데이터들을 추적한다.

→ 실제로는 `std::shared_ptr<rclcpp::Node>` 와 같은 형식을 가질 것입니다.

- **simple_loop.cpp**
    
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
    
    #include "rclcpp/rclcpp.hpp"
    
    int main(int argc, char** argv)
    {
      rclcpp::init(argc, argv);
    	// 생성된 Node는 shared_ptr!! 
      auto node = rclcpp::Node::make_shared("simple_loop_node");
    
      // WallRate & Rate
      // https://qiita.com/NeK/items/bcf518f6dd79f970bb8e
    	// 숫자 2는 2Hz를 의미하며, 숫자가 커질수록 더 짧은 주기로 반복됩니다.
      rclcpp::WallRate rate(2);
    
    	// 마치 아두이노 프로그래밍을 하듯이, 주기적으로 Node를 반복 실행시키게 됩니다.
      while (rclcpp::ok())
      {
        RCLCPP_INFO(node->get_logger(), "Simple Loop Node");
    		// 해당 부분이 실질적으로 Node를 갱신하는 부분입니다.
        // Python의 spin_once와 유사하다고 보시면 됩니다.
        rclcpp::spin_some(node);
    		// time.sleep를 생각하시면 됩니다.
        rate.sleep();
      }
    
      rclcpp::shutdown();
      return 0;
    }
    ```
    
- 주기적으로 동작이 필요할 땐 timer가 필요하게 된다.

→ Timer는 두 가지 종류가 존재

1. `rclcpp::WallRate` = steady_clock 기반으로 단순히 계속해서 늘어나는 시간이다.
2. `rclcpp::Rate` = system_clock 기반으로 시스템에 부하가 걸리면 오차가 일어날 가능성이 있다.
- **simple_oop.cpp**
    
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
    #include "rclcpp/rclcpp.hpp"
    
    // 핵심!! rclcpp::Node를 상속받습니다.
    class Talker : public rclcpp::Node
    {
    private:
    	// Node를 주기적으로 실행시켜 줄 timer 입니다.
      rclcpp::TimerBase::SharedPtr m_timer;
      size_t m_count;
    	
      // 실질적으로 실행될 함수입니다.
      void timer_callback()
      {
        m_count++;
    		// Log를 남겨보겠습니다.
        RCLCPP_INFO(this->get_logger(), "I am Simple OOP Example, count : %d", m_count);
      }
    
    public:
    	// Node의 생성 시에, 이름을 설정해주어야 하며 이는 다음과 같이 구현합니다.
      Talker() : Node("simple_oop_node")
      {
    		// create_wall_timer 함수에 timer와 실행시킬 함수를 전달하면 편리하게 주기적 실행을 할 수 있습니다.
    		// this->는 굳이 명시하지 않아도 됩니다.
        m_timer = this->create_wall_timer(std::chrono::milliseconds(500), std::bind(&Talker::timer_callback, this));
      }
    };
    
    int main(int argc, char **argv)
    {
      rclcpp::init(argc, argv);
    
    	// spin은 Node내부에 정해진 timer에 따라 Node를 주기적으로 동작, 갱신시켜줍니다.
      rclcpp::spin(std::make_shared<Talker>());
      rclcpp::shutdown();
      return 0;
    }
    ```
    
- `std::bind`를 사용하면 클래스 내의 함수를 마치 일반 함수처럼 사용할 수 있다.

→ 하지만 위와 같이 쓸라면 추가 정보가 필요하다.

1. 이 함수가 어떤 형태 객체 내의 함수인지
2. 이 함수가 매개변수를 몇 개 받는지

```jsx
m_timer = this->create_wall_timer(std::chrono::milliseconds(500), std::bind(&Talker::timer_callback, this));
```

- `rclcpp::spin`을 사용하면 똑똑하게 주기적으로 반복 실행을 해준답니다. 매개변수로는 shared_ptr 타입의 Node를 넣어주면 됩니다.

```jsx
while (rclcpp::ok()){
  rclcpp::spin_some(node);
  rate.sleep();
}

vs

rclcpp::spin(std::make_shared<Talker>());
```

→ 이 때 얼마만큼의 주기로 실행되어야 하는 지도 알아야된다. (클래스 안에 timer가 선언)

```jsx
class Talker : public rclcpp::Node
{
private:
  rclcpp::TimerBase::SharedPtr m_timer;
	...

public:
  Talker() : Node("simple_oop_node")
  {
    m_timer = this->create_wall_timer(std::chrono::milliseconds(500), std::bind(&Talker::timer_callback, this));
  }
};
```

# Node & rclcpp - lifecycle

---

**Node가 생성되고 사라지는, 그리고 Spin이 시작되는 시점**

- **lifecycle.cpp**
    
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
    #include "rclcpp/rclcpp.hpp"
    
    class Talker : public rclcpp::Node
    {
    private:
      rclcpp::TimerBase::SharedPtr m_timer;
      size_t m_count;
    
      void timer_callback()
      {
        m_count++;
    		// spin이 Node를 주기적으로 동작시키는 시점을 찍어봅니다.
        RCLCPP_INFO(this->get_logger(), "I am Simple OOP Example, count : %d", m_count);
      }
    
    public:
      Talker() : Node("simple_oop_node"), m_count(0)
      {
    		// 생성자 부분에 Log를 찍어봅니다.
        RCLCPP_WARN(this->get_logger(), "Node Constructor");
    
        m_timer = this->create_wall_timer(std::chrono::milliseconds(500), std::bind(&Talker::timer_callback, this));
      }
    
      ~Talker(){
    		// 소멸자 부분에 Log를 찍어봅니다.
        RCLCPP_WARN(this->get_logger(), "Node Destructor");
      }
    };
    
    int main(int argc, char **argv)
    {
      rclcpp::init(argc, argv);
      
      auto talker = std::make_shared<Talker>();
      rclcpp::spin(talker);
      
    	// Spin이 언제 종료되는지 찍어봅니다.
      RCLCPP_INFO(talker->get_logger(), "==== Spin Done ====");
      rclcpp::shutdown();
    
    	// rclcpp이 언제 종료되는지 찍어봅니다.
      std::cout << "==== After Shutdown ====" << std::endl;
    
      return 0;
    }
    ```
    

**위에 코드를 실행 시 결과**

```jsx
$ ros2 run cpp_first_pkg lifecycle_node
[WARN] [1621571766.599589500] [simple_oop_node]: Node Constructor
[INFO] [1621571767.102470700] [simple_oop_node]: I am Simple OOP Example, count : 1
[INFO] [1621571767.600820400] [simple_oop_node]: I am Simple OOP Example, count : 2
[INFO] [1621571768.022392500] [rclcpp]: signal_handler(signal_value=2)
[INFO] [1621571768.023429800] [simple_oop_node]: ==== Spin Done ====
==== After Shutdown ====
[WARN] [1621571768.025122300] [simple_oop_node]: Node Destructor
```

### 동작 순서

1. **rclcpp 실행** 
2. **Node 생성**
3. **spin이 Node를 주기적으로 실행**
4. **signal_handler 강제 종료를 시킨 시점**
5. **spin을 벗어남**
6. **rclcpp 종료**
7. **Node 소멸**
- rclcpp가 종료된 이후, Node가 소멸되므로 Node의 소멸자에는 웬만하면 ros통신이 필요한 코드는 지양하는 것이 좋다.

# 메모

---

- rclcpp에서 Node생성은 auto node = rclcpp::Node::make_shared(생성노드 이름);
- RCLCPP_INFO(node->get_logger(), "Logger Test");
node ->이 뜻은 노드에 안에 있다라는 뜻
    
    노드생성을 하지않으면 rclcpp::get_logger()
    
- C++에서도  rclcpp::init(argc, argv); 초기화 ,rclcpp::shutdown(); 셧다운은 해준다.
- ros2는 모든 Node가 (shared_ptr)포인터 형식을 갖는다.

(→) 을 사용할 수 있음 ex) msg→ranges

- // Python의 spin_once ⇒ rclcpp::spin_some(node);
- rclcpp::ok는 강제적으로 종료하지않는한 계속 while문의 돈다.
- time.sleep ⇒ rate.sleep(); rclcpp::WallRate rate(2);

(wall_timer(얼마만의 주기, 무엇을 실행)

- c++ timer 종류 2가지 차이점 wallrate ⇒ steady_clock, Rate⇒ system_clock
- std::bind(&Object::greeting, &my_obj, std::placeholders::_1);
    
    (무슨 object인지, 실질적인 object, 몇개의 매개변수를 갖는지)
    
    이거를 사용하면 클래스내의 함수를 일반 함수처럼 사용 가능
    
- 마지막으로 하는건 shutdown 이전에 ({}=스코프)
- auto는 자동으로 변수의 데이터타입을 지정해준다.