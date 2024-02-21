---
title: Multi UAV Offboard Flight
author: soojung son
date: 2023-08-07
category: px4
layout: post
---

PX4 Autopilot: Open Source Autopilot for Drones
https://px4.io/


## 오프보드 모드란?

<관련 문서>

* 비행 모드 - 오프보드 https://docs.px4.io/main/ko/flight_modes/offboard.html

* Drone Apps & APIs - 리눅스 오프보드 제어 https://docs.px4.io/main/ko/ros/offboard_control.html
  
* 하드웨어 - 보조 컴퓨터 설정 : https://docs.px4.io/main/ko/companion_computer/pixhawk_companion.html




## 다중 UAV Onboard 비행 실험 준비 조건

### 1. 텔레메트리 ID 설정

짝이 되는 두 텔레메트리 (드론에 연결된 텔레메트리와 지상국에 연결된 텔레메트리)의 ID를 일치시키고,
서로 다른 드론마다 텔레메트리 ID를 다르게 부여

적용 방법: 

https://ardupilot.org/copter/docs/common-configuring-a-telemetry-radio-using-mission-planner.html

MissionPlanner : 

1. 기체에 텔레메트리 연결 후 배터리 연결

2. 텔레메트리(기체에 연결한 텔레메트리와 짝이 되는)를 미션플래너를 실행하는 컴퓨터와 연결
   ※ 미션 플래너에서는 연결 버튼 누르지 않기

3. 미션플래너 : 설정 -> 추가 하드웨어 -> Sik 무선신호 탭

4. Load settings에서 Net ID 변경 : local과 Remote 동일하게
   ※ local : 미션 플래너 컴퓨터와 연결된 텔레메트리
   ※ Remote 파라미터를 바꿀 수 없을 때
     : 기체와 연결된 텔레메트리를 컴퓨터와 연결하고 Local 파라미터에서 Net ID 변경


### 2. MAVROS ID 설정

각 기체별로 ID를 다르게 설정한다. 기체의 ID는 MAV_SYS_ID와 px4.launch의 ID로 설정하며, 동일 기체는 두 ID가 일치해야 한다.

1) MAV_SYS_ID 설정
   : QGroundControl 파라미터

3) launch파일에서 ID 설정
<arg name="ID" value=" "/>
value=""에 숫자 입력


### 3. px4.launch 에서 udp_url 설정
<arg name="fcu_url" default="/dev/ttyACM0"/>
픽스호크와 연결된 usb port 설정(ex. "/dev/ttyACM0" 혹은 "/dev/ttyUSB0")


### 4. 각 UAV별 그룹 이름 설정

아래 예시처럼, px4.launch 파일에서 각 UAV별 그룹 이름을 설정하여 토픽이 겹치지 않도록 한다.

    <arg name="ns1" default="UAV1"/>
    <arg name="ns2" default="UAV2"/>
    <arg name="ns3" default="UAV3"/>
    <arg name="ns4" default="UAV4"/>
    <arg name="ns5" default="UAV5"/>
    
    <!-- UAV1 -->
    <group ns="$(arg ns1)">

    ....

    <!-- UAV2 -->
    <group ns="$(arg ns2)">

### 5. QGroundControl에서 다중 텔레메트리 연결

보통 여러 텔레메트리를 연결하면 자동으로 QGroundControl에서 '기체1', '기체2'과 같이 여러 기체가 인식된다.
          

## UAV Onboard 비행 실험 순서

1. QGroundcontrol - GPS 측량 수행
  참고) https://docs.px4.io/main/ko/gps_compass/rtk_gps.html
   1) QGroundcontrol을 실행하는 컴퓨터에 RTK GPS 베이스를 연결한다.
   2) 응용프로그램 설정 - 일반 - RTK GPS에서 측량수행을 선택한다.
   3) QGroundControl을 껐다가 다시 실행한다.
   4) 메인페이지에서 RTK가 흰색으로 바뀔때까지 기다린다.
   5) RTK가 흰색으로 바뀌면, 응용프로그램 설정 - 일반 - RTK GPS에서 '현재 위치 저장하기' 버튼이 활성화될 것이다. 해당 버튼을 누르고 지정된 기준 위치 사용을 선택한다.
   6) QGroundControl을 껐다가 다시 실행한다.
      
2. ROS 연결
   전제조건 ) 서버와 각 UAV 사이에 서버를 master로 하는 ROS 통신 설정이 되어있다. (같은 와이파이 연결, bashrc에서 Master pc ip 설정)
   1) 서버에서 roscore를 실행한다.
   2) 각 UAV에서 px4.launch 파일을 실행한다. 위에서 설명했듯이 udp_url 설정과 ID 설정에 유의한다.
   3) 서버에서 각 UAV와 연결이 되었는지 확인한다.

3. 오프보드 활성화를 위한 Setpoint 발행
   '${uavname}/mavros/setpoint_position/local' 토픽을 발행하는 노드를 실행한다.
    2hz 이상으로 발행한다.
   
4. 시동

5. 조종기로 OFFBOARD 모드 변환
   전제 조건) 조종기 스위치에 OFFBOARD 모드 활성화가 설정되어 있다. 
   mavlink 메세지로도 오프보드 모드 활성화가 가능하지만, 조종기로 변경하는 것이 더 안전하다.
   https://docs.px4.io/main/ko/ros/offboard_control.html


## 오프보드 매개변수

https://docs.px4.io/main/ko/flight_modes/offboard.html

오프보드 모드는 아래의 매개 변수의 영향을받습니다.

매개변수	설명

1. COM_OF_LOSS_T	: 
  Time-out (in seconds) to wait when offboard connection is lost before triggering offboard lost failsafe (COM_OBL_RC_ACT)
  오프보드 연결이 끊어졌을 때 오프보드 연결 끊김 페일세이프를 트리거하기 전에 대기할 시간 제한(초)(COM_OBL_RC_ACT)

2. COM_OBL_RC_ACT	: 
  Flight mode to switch to if offboard control is lost (Values are - 0: Position, 1: Altitude, 2: Manual, 3: *Return, 4: Land).
  기내 제어 기능이 상실된 경우 전환할 비행 모드(0: Position, 1: Altitude, 2: Manual, 3: *Return, 4: Land)

3. COM_RC_OVERRIDE	: 
  Controls whether stick movement on a multicopter (or VTOL in MC mode) causes a mode change to Position mode. 기본적으로 오프보드 모드에서는 활성화되지 않습니다.
  멀티콥터(또는 MC 모드의 VTOL)에서 스틱을 움직이면 위치 모드로 모드가 변경되는지 여부를 제어합니다.

4. COM_RC_STICK_OV	: 
  The amount of stick movement that causes a transition to Position mode (if COM_RC_OVERRIDE is enabled).
  위치 모드로 전환하는 스틱 움직임의 양입니다(COM_RC_OVERRIDE가 활성화된 경우).

5. COM_RCL_EXCEPT	: 
  Specify modes in which RC loss is ignored and the failsafe action not triggered. Set bit 2 to ignore RC loss in Offboard mode.
  RC 손실이 무시되고 페일 세이프 동작이 트리거되지 않는 모드를 지정합니다. 오프보드 모드에서 RC 손실을 무시하려면 비트 2를 설정합니다.


