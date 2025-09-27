# 基于 ROS 2 和 Navigation 2 自动巡检机器人

## 1.项目介绍

本项目旨在在 Navigation2 利用 Mid360 以及 Fast_Lio 设计了一个自动巡检机器人仿真功能。

目前尚未完善，只实现了建图功能，且整个工程依托答辨，谨慎使用

各功能包功能如下：
- fishbot_description 机器人描述文件，包含仿真相关配置
- fishbot_navigation2 机器人导航配置文件
- fishbot_application 机器人导航应用 Python 代码
- fishbot_application_cpp 机器人导航应用 C++ 代码
- autopatrol_interfaces  自动巡检相关接口
- autopatrol_robot  自动巡检实现功能包
- FAST_LIO_ROS2  建图包
- livox_laser_simulation_ros2  mid360仿真驱动包
- livox_ros_driver2  mid360驱动包

## 2.使用方法

本项目开发平台信息如下：

- 系统版本： Ubunt22.04
- ROS 版本：ROS 2 Humble

### 2.1安装

本项目建图采用 fastlio，导航(准备)采用 Navigation 2,仿真采用 Gazebo，运动控制采用 ros2-control 实现，构建之前请先安装依赖，指令如下：

1. 安装依赖

```
sudo apt install ros-$ROS_DISTRO-nav2-bringup ros-$ROS_DISTRO-slam-toolbox
sudo apt install ros-$ROS_DISTRO-robot-state-publisher  ros-$ROS_DISTRO-joint-state-publisher ros-$ROS_DISTRO-gazebo-ros-pkgs ros-$ROS_DISTRO-ros2-controllers ros-$ROS_DISTRO-xacro
sudo apt install python3-pip  -y
sudo apt install espeak-ng -y
sudo pip3 install espeakng
sudo apt install ros-$ROS_DISTRO-tf-transformations
sudo pip3 install transforms3d
```

2. 创建工作空间

```
mkdir -p ~/ros_ws
cd ~/ros_ws
```

```
git clone --recursive https://github.com/HuaJiang2003/nav2_simulation.git
```

    克隆仓库后FAST_LIO_ROS2可能缺失ikd-Tree，在目录src/FAST_LIO_ROS2/include/ikd-Tree下，请自行down一个。

3. 更改子模块相关参数

    FAST_LIO_ROS2：更改lidar和imu的接收话题，本工程中为"/mid360_PointCloud2"和"/imu",use_sim_time:=True!!!

### 2.2运行

安装完成依赖后，可进行构建和运行。

1. Build the Livox ROS Driver 2:

```
cd src/livox_ros_driver2
source /opt/ros/humble/setup.sh
./build.sh humble
```

2. 构建功能包

```
cd ~/ros_ws
colcon build
```

3. 运行仿真

```
source install/setup.bash
ros2 launch fishbot_description gazebo_sim.launch.py
```

4. 建图

- 新建终端 启动fastlio建图功能

```
source install/setup.bash
ros2 launch fast_lio mapping.launch.py 
```

- 新建终端 键盘控制节点（控制机器人移动）

```
ros2 run teleop_twist_keyboard teleop_twist_keyboard 
```

- 新建终端 保存地图

    打开 RQt 并选择 Plugins->Services->Service Caller. 触发 service/map_save, pcd地图即可保存。

5. 一些环境上的注意事项，我安装了conda，所以这里较乱

```
# 若gazebo无法启动，请运行下面的指令
source /usr/share/gazebo/setup.bash
# 若系统中安装有虚拟环境请删除build文件夹后运行下面的指令
source /opt/ros/humble/setup.bash
```

## 3.效果演示

![环境与建图效果](https://raw.githubusercontent.com/HuaJiang2003/nav2_simulation/main/fishbot_description/images/1.png)

目前因为配置原因并未移动机器人实现整个地图的构建，电脑卡爆了。。。。。。。。

## 4.作者

- [jh](https://github.com/HuaJiang2003)