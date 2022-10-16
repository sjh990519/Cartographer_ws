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
$ cp backpack_2d.lua /home/pray/catkin_ws/src/slam/lua/my_robot.lua
```


<br><br><br>

### :turtle: 복사한 launch & lua 파일을 자신의 로봇에 맞게 커스텀 한다.


### :blue_book: 원본 [ backpack_2d.launch ]
```
<launch>
  <param name="robot_description"
    textfile="$(find cartographer_ros)/urdf/backpack_2d.urdf" />

  <node name="robot_state_publisher" pkg="robot_state_publisher"
    type="robot_state_publisher" />

  <node name="cartographer_node" pkg="cartographer_ros"
      type="cartographer_node" args="
          -configuration_directory $(find cartographer_ros)/configuration_files
          -configuration_basename backpack_2d.lua"
      output="screen">
    <remap from="echoes" to="horizontal_laser_2d" />
  </node>

  <node name="cartographer_occupancy_grid_node" pkg="cartographer_ros"
      type="cartographer_occupancy_grid_node" args="-resolution 0.05" />
</launch>
```

<br><br><br>

### :green_book: 수정 [ my_robot.launch ]
- 수정 내용
- TF 가 정의되어 있으므로 URDF 정의 부분 삭제
- lua 파일을 새로 커스텀한 lua파일을 참조하도록 수정
- <remap>부분을 자신의 센서 토픽에 맞게 수정
  라이다 스캔 데이터의 경우
  from -> scan,    to -> 토픽이름 

```
<launch>

  <node name="cartographer_node" pkg="cartographer_ros"
      type="cartographer_node" args="
          -configuration_directory $(find slam)/lua
          -configuration_basename my_robot.lua"
      output="screen">
    <remap from="scan" to="scan"/>
  </node>

  <node name="cartographer_occupancy_grid_node" pkg="cartographer_ros"
      type="cartographer_occupancy_grid_node" args="-resolution 0.02" />
</launch>
```


<br><br><br><br>


### :blue_book: 원본 [ backpack_2d.lua ]
```
include "map_builder.lua"
include "trajectory_builder.lua"

options = {
  map_builder = MAP_BUILDER,
  trajectory_builder = TRAJECTORY_BUILDER,
  map_frame = "map",
  tracking_frame = "base_link",
  published_frame = "base_link",
  odom_frame = "odom",
  provide_odom_frame = true,
  publish_frame_projected_to_2d = false,
  use_pose_extrapolator = true,
  use_odometry = false,
  use_nav_sat = false,
  use_landmarks = false,
  num_laser_scans = 0,
  num_multi_echo_laser_scans = 1,
  num_subdivisions_per_laser_scan = 10,
  num_point_clouds = 0,
  lookup_transform_timeout_sec = 0.2,
  submap_publish_period_sec = 0.3,
  pose_publish_period_sec = 5e-3,
  trajectory_publish_period_sec = 30e-3,
  rangefinder_sampling_ratio = 1.,
  odometry_sampling_ratio = 1.,
  fixed_frame_pose_sampling_ratio = 1.,
  imu_sampling_ratio = 1.,
  landmarks_sampling_ratio = 1.,
}

MAP_BUILDER.use_trajectory_builder_2d = true
TRAJECTORY_BUILDER_2D.num_accumulated_range_data = 10

return options
```

<br><br><br>

### :green_book: 수정 [ my_robot.lua ]
- Cartographer Document 보고 수정 하면된다.

<br>
  
- 수정 내용
- tracking_frame = "base_link",   ---->   tracking_frame = "base_footprint",
- published_frame = "base_link",   ---->   published_frame = "base_footprint",
- provide_odom_frame = true,   ---->   provide_odom_frame = false,
- publish_frame_projected_to_2d = false,   ---->   publish_frame_projected_to_2d = true,
- use_odometry = false,   ---->   use_odometry = true,
- num_multi_echo_laser_scans = 1,   ---->   num_multi_echo_laser_scans = 0,
  
<br>
  
```
include "map_builder.lua"
include "trajectory_builder.lua"

options = {
  map_builder = MAP_BUILDER,
  trajectory_builder = TRAJECTORY_BUILDER,
  map_frame = "map",
  tracking_frame = "base_footprint",
  published_frame = "base_footprint",
  odom_frame = "odom",
  provide_odom_frame = true,
  publish_frame_projected_to_2d = false,
  use_pose_extrapolator = false,
  use_odometry = false,
  use_nav_sat = false,
  use_landmarks = false,
  num_laser_scans = 1,
  num_multi_echo_laser_scans = 0,
  num_subdivisions_per_laser_scan = 10,
  num_point_clouds = 0,
  lookup_transform_timeout_sec = 0.2,
  submap_publish_period_sec = 0.3,
  pose_publish_period_sec = 5e-3,
  trajectory_publish_period_sec = 30e-3,
  rangefinder_sampling_ratio = 1.,
  odometry_sampling_ratio = 1.,
  fixed_frame_pose_sampling_ratio = 1.,
  imu_sampling_ratio = 1.,
  landmarks_sampling_ratio = 1.,
}

MAP_BUILDER.use_trajectory_builder_2d = true
TRAJECTORY_BUILDER_2D.num_accumulated_range_data = 7
TRAJECTORY_BUILDER_2D.min_range = 0.1
TRAJECTORY_BUILDER_2D.max_range = 10
TRAJECTORY_BUILDER_2D.use_imu_data = false
TRAJECTORY_BUILDER_2D.use_online_correlative_scan_matching = true
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.linear_search_window = 0.1
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.translation_delta_cost_weight = 0.1
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.rotation_delta_cost_weight = 1e-1

return options
```

<br><br><br>

### :mag: Cartographer 패키지 서치 
#### test_ws
```
$ source devel_isolated/setup.bash
```

<br>
  
#### catkin_ws/src
```
$ catkin_init_worspace
$ catkin_make
```

<br><br><br>
  
  
### :four: 실행
  
#### :computer: Desktop
```  
$ roscore
```
  
<br>  
  
#### :strawberry: Raspberry Pi
```
$ roslaunch kobuki_node minimal.launch
$ roslaunch hls_lfcd_lds_driver hlds_laser.launch 
```
  
<br>
  
#### :computer: Desktop
```
$ roslaunch slam my_robot.launch
```
  
<br><br><br>
  
#### :sunrise: Rviz 실행  
```
$ rosrun rviz rviz
```  

#### Rviz 설정
- ADD : Map
- ADD : TF
- ADD : PointCloud2  
  
  
<br><br>
  
### :smile: 실행 화면 
#### YOUTUBE : https://youtu.be/wBjoO7pHt2k
[![결과](https://user-images.githubusercontent.com/94280596/192163083-8313a2c5-f20c-4f03-9279-e7241a28e342.png)](https://user-images.githubusercontent.com/94280596/196027554-39ec5e14-a347-42ff-b8d4-a01646c03222.mp4)

<br><br>
  
#### 로봇 조종
* 유저가 직접 로봇을 조정하며 SLAM 작업을 한다.
* 로봇의 속도를 너무 급하게 바꾸거나 너무 빠른 속도로 전/후진 회전 하지 않도록 한다.
* 계측할 환경의 세세하게 로봇이 돌아다니면서 스캔을 해야 정확하게 스캔이 가능하다.

<br>

#### :computer: Desktop
```
$ roslaunch kobuki_keyop keyop.launch
```


<br><br>
  
#### Topic Data 저장
* 로봇을 조종하면서 SLAM 작업을 진행하는데, 이떄 kobuki & Cartographer_ros 패키지에서 발행하는 
* /scan, /tf 토픽을 scan_data 이라는 파일명의 .bag 파일로 저장한다.
* 추후 이 파일을 가지고 맵을 만들 수도 있으며, 맵핑작업시에 실험 당시의 /scan과 /tf 토픽 을 재현할 수 있다.

<br>

#### :computer: Desktop
```
$ rosbag record -O scan_data /scan /tf
```
```
$ rqt_graph
```

<br><br>


#### [ rqt_graph ]
![rqt_graph](https://user-images.githubusercontent.com/94280596/196026789-dce951a8-74ab-4a36-868f-9210cb3a369a.png)


<br><br>
  
#### Map 저장
* 로봇을 이동시키면 로봇의 오도메트리, tf 정보, 센서의 스캔 정보를 기반으로 맵이 작성된다.
* 모든 작업이 완료 되었으며 map_saver 노드를 실행하여 맵을 작성한다.
* 저장은 map_saver를 동작시킨 디렉토리에 저장된다.
* 파일명은 따로 붙이지 않으면 [ map.pgm 파일명과 / map.yaml 파일명으로 저장된다. ]



<br>

#### :computer: Desktop
```
$ rosrun map_server map_saver
```
