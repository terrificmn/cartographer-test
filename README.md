# cartographer 테스트 with L515 and T265

테스트 중 입니다.
런치파일 실행은 되지만 
```
[ WARN] [1627020861.768025361]: W0723 15:14:21.000000 31294 tf_bridge.cc:52] "imu_link" passed to lookupTransform argument target_frame does not exist.
```
메세지와 함께 안됨니다.


<br/>

## 테스트 하려면 cartographer 패키지 필요

툴 설치 wstool rosdep ninja (build를 하기 위한 툴이라고 함)
```
sudo apt-get update
sudo apt-get install -y python-wstool python-rosdep ninja-build stow
```

cartographer_ros 를 위한 워크스페이스 만들기   
보통 catkin_ws 로 만들지만 
빌드를 catkin_make_isolated 로 해서 나중에 catkin_make가 안되고 불편해서 워크스페이스를 따로 만듬
(따로 만들면 기존의 catkin_ws 에서 catkin_make 가 가능, 또는 catkin_make_isolated 를 사용하려면  
catkin_make_isolated --pkg <your package name> 식으로 가능하다고 함)

```
cd 
mkdir catkin_ws_iso
cd catkin_ws_iso   ### 또는 cd catkin_ws
wstool init src
wstool merge -t src https://raw.githubusercontent.com/cartographer-project/cartographer_ros/master/cartographer_ros.rosinstall
wstool update -t src
```

[cartographer_ros] Done. 이라고 나오면 잘 되고 있는 것  
이제 dependencies 설치하기 (경로는 catkin_ws_iso 안에서)
```
sudo rosdep init
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
```
rosdep을 이미 한번 실행했으면 에러가 발생하지만 무시하면 됨
ERROR: default sources list file already exists:

설치가 잘 되었다면 #All required rosdeps installed successfully 나온다

계속 catkin_ws_iso 위치임. abseil-cpp가 필요하다고 함 스크립트 파일로 설치
```
src/cartographer/scripts/install_abseil.sh
```
충돌방지를 위해서 apt-get으로 설치했다면 지워준다
```
sudo apt-get remove ros-${ROS_DISTRO}-abseil-cpp
```
설치가 안되어 있으면 설치가 안되어 있으므로 안 지운다라고 뜨면 정상

마지막으로 빌드 
```
catkin_make_isolated --install --use-ninja
```

<br/>


## 기타 필요패키지 설치
imu_tools 설치
브랜치만 클론해서 따로 설치한다 (멜로딕 버전)
```
$ cd ~/catkin_ws/src
$ git clone -b melodic https://github.com/ccny-ros-pkg/imu_tools.git
```

rosdep을 이용해서 dependency를 해결
```
$ rosdep install imu_tools
```
rosdep이 없다면 
```
$ sudo apt-get install python-rosdep
```

catkin_ws 디렉토리에서 빌드를 해준다
```
$ catkin_make
```

<br/>


## pointcloud_to_laserscan 패키지
```
git clone -b lunar-devel https://github.com/ros-perception/pointcloud_to_laserscan.git
```
또는 apt-get 으로 가능할 듯 
```
sudo apt-get install ros-melodic-pointcloud-to-laserscan
```

