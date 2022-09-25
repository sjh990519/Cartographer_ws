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

<br>

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


















