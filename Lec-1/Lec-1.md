# Lec-1

로봇을 어떻게 개발할 수 있을까요?

기구 설계 → 임베디드 (모터 구동) → 컴퓨터 비전(카메라) → 웹 연동 (클라이언트)

시뮬레이션 → 머신러닝 (AI) → 각 로봇마다 가지는 특성 → 강인한 로봇 제작

ROS 

Robot Operating System 의 등장 배경

(1) 기존의 존재하는 다양한 로봇 opensource 제공
- (기능 패키지들로 구성)
 
(2) 로봇 개발에 필요한 다양한 툴 제공
- 시뮬레이션 → Gazebo, ISSAC
- 임베디드 → ROS = rosserial, ROS2 = micro-ROS
- 시각화, 디버깅 → RViz, RQt
 
(3) 동작에 있어 안전함을 보장 (강인한 로봇)

ROS2

(1) 등장 배경 = 기존 ROS의 문제점 (실시간성이 보장되지 않음, 보안성 취약, python2 사용)을 해결

(2) Layer

- OS Layer = Linux/Windows/Mac/RTOS 지원
- Application Layer = Master 를 통하지 않고 ROS 프로그램 자체로만 돈다. (전처리 X)
- Middleware Layer = DDS 사용 (실무에서 사용 방식 그대로)

(3) 성능면에서도 Latency(지연) 문제 해결 → ROS2는 실시간성이 보장된 RT 사용

ROS 2 활용 예시 (기업)

(1) 배달 로봇 ( kiwibot, ROBOTIS)

(2) 자율 주행 자동차 (apex.ai)

(3) 시뮬레이션 제작 (MORAI)

메모

시뮬레이션을 하는 이유 = 비용 감소 (오차를 줄이므로)

ROS2는 기존의 ROS를 업데이트 한 것이 아닌 새로운 버전이다.
