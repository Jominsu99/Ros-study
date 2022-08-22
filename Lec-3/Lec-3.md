# Lec-3

### ROS2 Foxy설치 - window10 환경에서

**조건 1 : 최신 Windows 업데이트 적용**

**조건 2 : installing chocolatey**

- chocolatey = 패키지 관리 프로그램 중 하나이다. Powershell을 통해 설치한다.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

**조건 3 : chocolatey를 통해 종속성 프로그램 설치**

- Windows Terminal = PC에 설치된 다양한 터미널 프로그램들을 손쉽게 사용 가능하게 하는 프로그램

```jsx
choco install -y microsoft-windows-terminal
```

**<Powershell, Windows Terminal은 관리자 권한으로 실행>**

- Python 3.8.3 <종료 코드 exit()>

```powershell
choco install -y python --version 3.8.3
```

- Git = 버전 관리자 <설치 확인 코드 git - -version>

```jsx
> choco install git

> git --version
```

- Visual C++ Redistributables = ROS2 빌드를 위한 배포판 C++

```powershell

choco upgrade --checksum64 F3B7A76D84D23F91957AA18456A14B4E90609E4CE8194C5653384ED38DADA6F3 --checksum 99DCE3C841CC6028560830F7866C9CE2928C98CF3256892EF8E6CF755147B0D8 vcredist2010
choco install -y vcredist2010 vcredist2013 vcredist140
```

- OpenSSL = 여러 프로그램 설치 시 인증을 해준다. <시스템 환경 변수에도 OpenSLL의 위치를 추가>
- Visual Studio 2019 = C++컴파일러 등의 개발 환경을 모두 갖춘 windows의 통합 개발 환경(IDE)이다.
- CMake = 운영체제에 상관없이 사용 가능한 빌드 툴이며, ROS2가 사용하는 colcon build system은 CMake에 기반하고 있다. <시스템 환경 변수에 CMake의 위치를 추가>

```jsx
choco install -y cmake
```

추가 종속성 설치

- Nuget → <log4cxx.0.10.0-2.nupkg 제외>

```jsx
> choco install -y -s <PATH\TO\DOWNLOADS> asio cunit eigen tinyxml-usestl tinyxml2 log4cxx bullet

# 저의 경우 다음과 같습니다.
> choco install -y -s [절대 경로] asio cunit eigen tinyxml-usestl tinyxml2 log4cxx bullet

asio;cunit;eigen;tinyxml-usestl;tinyxml2;log4cxx;bullet
By installing you accept licenses for the packages.

asio v1.12.1
asio package files install completed. Performing other installation steps.
HKEY_LOCAL_MACHINE\SOFTWARE\Kitware\CMake\Packages\Asio
@{CMakePackageDir=C:\opt\chocolatey\lib\asio\share\cmake; PSPath=Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\
SOFTWARE\Kitware\CMake\Packages\Asio; PSParentPath=Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Kitwa
re\CMake\Packages; PSChildName=Asio; PSDrive=HKLM; PSProvider=Microsoft.PowerShell.Core\Registry}
 The install of asio was successful.
  Software install location not explicitly set, could be in package or
  default install location if installer.

...

Chocolatey installed 7/7 packages.
 See the log for details (C:\opt\chocolatey\logs\chocolatey.log).

**Installed:
 - tinyxml2 v6.0.0
 - asio v1.12.1
 - cunit v2.1.3
 - bullet v2.89.0
 - tinyxml-usestl v2.6.2
 - eigen v3.3.4
 - log4cxx v0.10.0**
```

- 기타 파이썬 패키지들 설치

```jsx
> python -m pip install --upgrade pip
> python -m pip install -U catkin_pkg cryptography empy ifcfg lark-parser lxml netifaces numpy opencv-python pyparsing pyyaml setuptools rosdistro
> python -m pip install -U pydot PyQt5
```

- Graphviz

```jsx
> choco install graphviz
```

- OpenCV 3.4.6 = Computer Vision Library이며 시각적인 데이터를 다룰 수 있는 여러 기능을 포함한다.

---

# ROS 설치

---

- 방법 1 → releases version 설치
- 방법 2 → [aka.ms/ros](http://aka.ms/ros) 통한 빠른 설치

→ 방법 2 설치 → windows terminal 관리자 권한 실행 → 코드 입력

```jsx
> set ChocolateyInstall=c:\opt\chocolatey
> choco source add -n=ros-win -s="https://aka.ms/ros/public" --priority=1
> choco upgrade ros-foxy-desktop -y --execution-timeout=0
```

→ workspace 생성 후 colcon build 및 기본 커멘드 → windows terminal 환경 Setup → Gazebo 환경 Setup

- **workspace 생성 후 colcon build**

ROS2는 일반 터미널에서 실행이 불가하여 x64 Native Tools Command를 사용한다.

(x64 Native Tools Command을 실행 후 설치)

```jsx
> c:\opt\ros\foxy\x64\setup.bat
> mkdir c:\gcamp_ros2_ws\src
> pushd c:\gcamp_ros2_ws

> cd src
> git clone https://github.com/Road-Balance/gcamp_ros2_basic.git
> cd ../
> colcon build --symlink-install --packages-select gcamp_gazebo

> c:\gcamp_ros2_ws>colcon build --symlink-install --packages-select gcamp_gazebo
[0.866s] root DEBUG Using proactor: IocpProactor
Starting >>> gcamp_gazebo
Finished <<< gcamp_gazebo [5.08s]

Summary: 1 package finished [5.33s]
```

<aside>
💡 여기서 터미널을 종료 X → `**uuidgen**`을 입력하신 뒤 나오는 코드를 기록한다.

</aside>

- **windows terminal 환경 Setup**

ROS2 전용 터미널을 만들기 위함이다.

<aside>
💡 `<your-uuid>` 자리에 앞서 **x64 Native Tools Command에서 얻은 uuid**를 적어준다.

</aside>

- **Cazebo 환경 Setup**

ROS2의 시뮬레이션이다.

(window terminal을 열고 다음 커맨드들을 입력)

```jsx
setx -m HOME C:\gcamp_ros2_ws
setx -m HOMEPATH C:\gcamp_ros2_ws
setx -m GAZEBO_MASTER_URI http://localhost:11345
setx -m GAZEBO_MODEL_DATABASE_URI http://models.gazebosim.org
setx -m GAZEBO_RESOURCE_PATH C:\opt\ros\foxy\x64\share\gazebo-10
setx -m GAZEBO_PLUGIN_PATH C:\opt\ros\foxy\x64\share\gazebo-10\plugins
setx -m GAZEBO_MODEL_PATH C:\opt\ros\foxy\x64\share\gazebo-10\models
setx -m SDF_PATH C:\opt\ros\foxy\x64\share\sdformat\1.6
```

(package build 작업 수행)

```jsx
> pushd C:\gcamp_ros2_ws

> colcon build --symlink-install --packages-select custom_interfaces
> colcon build --symlink-install --packages-select py_service_pkg
> colcon build --symlink-install --packages-select gcamp_gazebo

> install\setup.bat
```

## 메모

---

- (절대 경로 얻는 법)
![%ED%99%94%EB%A9%B4_%EC%BA%A1%EC%B2%98_2022-08-08_003206](https://user-images.githubusercontent.com/82068871/185958939-dfa88dff-dd8b-4b1c-b8fa-0ccc5d9b4a40.png)


위에 사진을 클릭한다.

- window 11에 대한 방법은 나와있지 않아 정리만 하였다.
