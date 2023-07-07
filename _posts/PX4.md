---
title: PX4 : Open Source Autopilot for Drones
author: crystall son
date: 2023-07-07
category: Jekyll
layout: post
---
PX4 Autopilot: Open Source Autopilot for Drones
https://px4.io/

## How to install
개발 환경 : Ubuntu 20.04/ROS Noetic 혹은 Ubuntu 18.04/ROS Melodic
※ 관련 자료 검색 시 PX4 폴더 이름에 유의 (일부 자료에서 Firmware로 표기됨) : 현재 Firmware → **PX4-Autopilot**로 변경됨

1. PX4 설치
https://docs.px4.io/main/ko/dev_setup/dev_env_linux_ubuntu.html#ros-gazebo-classic
```bash
wget https://raw.githubusercontent.com/PX4/Devguide/master/build_scripts/ubuntu_sim_ros_melodic.sh
bash ubuntu_sim_ros_melodic.sh
```

2. MAVROS 설치
https://docs.px4.io/main/ko/ros/mavros_installation.html
```bash
#binary install 
sudo apt-get install ros-${ROS_DISTRO}-mavros ros-${ROS_DISTRO}-mavros-extras ros-${ROS_DISTRO}-mavros-msgs
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
sudo bash ./install_geographiclib_datasets.sh
```
※ 본인의 ROS DISTRO에 맞게 설치 : ${ROS_DISTRO}에 해당 distro 입력 (ex. melodic, noetic)
※ ROS Noetic 참고 : https://velog.io/@jdja2004/SITL-%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95  'MAVROS 설치')

## Simulation
### Offboard 예시
1. ROS/MAVROS Offboard control - 코드 예시
C++ : https://docs.px4.io/main/ko/ros/mavros_offboard_cpp.html
Python : https://docs.px4.io/main/ko/ros/mavros_offboard_python.html

2. ROS/MAVROS Offboard control - ROS launch file 예시
https://docs.px4.io/main/ko/ros/mavros_offboard_python.html#creating-the-ros-launch-file

3. Offboard control github 참고 자료 
https://github.com/Jaeyoung-Lim/modudculab_ros
https://github.com/Jaeyoung-Lim/mavros_controllers
