# Cartographer_ws

<br><br><br>

## Google Cartographer_slam 실습내용
![1](https://user-images.githubusercontent.com/94280596/192159411-8c3549aa-d3c5-4656-92d9-2700a37c4d9e.png)


<br><br><br>


## :bell: Setting
### OS : Unbuntu 20.0.4 LTS, Rapbian Buster
### Ros : Noetic Ninjemys
### Robot : Kobuki (Yujinrobot),  Raspberry Pi 4 (Model B)
### Sensor : 360 Laser Distance Sensor LDS-01 (Lidar_ROBOTIS)



<br><br><br>

## :notebook: Cartographer Ros [Install]

### :one: Ros Version Check
- 현재 지원 되는 버전
- Kinetic
- Melodic
- Noetic

<br><br><br>

### :two: Building & Installation
- Cartographer Ros를 빌드하려면 wstool & rosdep 이 필요로 한다.
- 더 빠른 빌드를 위해서는 Ninja 를 사용하면 된다

<br>

- ROS Noetic이 있는 Ubuntu Focal
```
$ sudo apt-get update
$ sudo apt-get install -y python3-wstool python3-rosdep ninja-build stow
```

<br>

- 이전 배포판
```
$ sudo apt-get update
$ sudo apt-get install -y python-wstool python-rosdep ninja-build stow
```

<br><br><br>

- 도구 설치 후 새로운 Cartographer_ros 작업 공간을 생성한다.
```
$ mkdir test_ws
$ cd test_ws
$ wstool init src
$ wstool merge -t src https://raw.githubusercontent.com/cartographer-project/cartographer_ros/master/cartographer_ros.rosinstall
$ wstool update -t src
```

<br><br><br>

- Cartographer_ros 종속성 설치
- rosdep 필요한 패키지를 설치한다.
```
$ sudo rosdep init
$ rosdep update
$ rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO}

  # install 에러 시
$ rosdep install --from-paths src --ignore-src --rosdistro=noetic -y
```


<br><br><br>

- Cartographer_ros 사용 시 수동으로 abseil-cpp 라이브러리르 수동으로 설치해야 한다.
```
$ src/cartographer/scripts/install_abseil.sh
```

<br>

- 충돌하는 버전 시 수동으로 삭제한다.
```
$ sudo apt-get remove ros-${ROS_DISTRO}-abseil-cpp
```

- 빌드하고 설치한다.
```
$ catkin_make
```

<br><br><br>

### 3️⃣: Cartographer Custom
- 자신의 로봇과 라이다센서에 맞게 커스텀 하는 작업  :white_check_mark:

- 새로운 워크스페이스 만든 후 필요 없는 폴더 삭제 후 launch 폴더 & lua 폴더 생성
```
$ mkdir catkin_ws/ src
$ cd src
$ catkin_create_pkg slam roscpp
$ cd slam
$ sudo rm -rf include/ src
$ mkdir launch && mkdir lua
```

<br><br>

- Cartographer 패키지에서 사용할 launch 파일 및 lua 파일 복제
- launch 파일에서 lua파일을 참조하고 있기 때문에 복제

<br>

- launch
```
$ cd ~/test_ws/src/cartographer_ros/cartographer_ros
$ cp backpack_2d.launch /home/pray/catkin_ws/src/slam/launch/my_robot.launch
```
- lua
```
$ cd ~/test_ws/src/cartographer_ros/cartographer_ros/configuation_files
$ cp backpack_2d.launch /home/pray/catkin_ws/src/slam/lua/my_robot.lua
```





