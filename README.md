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

#### 2.1.1 安装依赖

1. 导航与SLAM相关
```shell 
# 导航系统核心包
sudo apt install ros-$ROS_DISTRO-nav2-bringup
# SLAM（同步定位与地图构建）
sudo apt install ros-$ROS_DISTRO-slam-toolbox
```

2. 机器人模型与状态发布
```shell 
# 机器人模型描述和状态发布
sudo apt install ros-$ROS_DISTRO-robot-state-publisher
sudo apt install ros-$ROS_DISTRO-joint-state-publisher
sudo apt install ros-$ROS_DISTRO-xacro
```

3. 仿真与Gazebo集成
```shell 
# Gazebo仿真环境
sudo apt install ros-$ROS_DISTRO-gazebo-ros-pkgs
```

4. ROS2控制系统（核心）
```shell 
# ROS2控制框架核心
sudo apt install ros-humble-ros2-control
sudo apt install ros-humble-ros2-controllers

# Gazebo与ROS2控制集成
sudo apt install ros-humble-gazebo-ros2-control
```

5. 控制器具体实现
```shell 
# 关节状态相关控制器
sudo apt install ros-humble-joint-state-publisher
sudo apt install ros-humble-joint-state-broadcaster
# 差分驱动控制器（移动机器人）
sudo apt install ros-humble-diff-drive-controller
# 控制器管理器
sudo apt install ros-humble-controller-manager
sudo apt install ros-humble-controller-manager-msgs
```

6. 坐标变换与数学计算
```shell 
# 坐标变换工具
sudo apt install ros-$ROS_DISTRO-tf-transformations
sudo pip3 install transforms3d
```

7. 语音与工具包
```shell 
# 语音合成
sudo apt install espeak-ng -y
sudo pip3 install espeakng
# Python包管理
sudo apt install python3-pip -y
```

8. 点云处理
```shell 
# 安装 pcl_ros 和相关依赖
sudo apt install ros-humble-pcl-ros
sudo apt install ros-humble-tf2-geometry-msgs
sudo apt install ros-humble-laser-geometry
```

9. 地图所需元素
```shell
mkdir -p ~/.gazebo
cd ~/.gazebo
git clone https://gitee.com/ohhuo/gazebo_models.git ~/.gazebo/models
rm -rf ~/.gazebo/models/.git
```

10. 安装 Livox-SDK2
```shell
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd ./Livox-SDK2/
mkdir build
cd build
cmake .. && make -j
sudo make install
```

#### 2.1.2 创建工作空间

```shell
mkdir -p ~/ros_ws
cd ~/ros_ws
```

```shell
git clone --recursive https://github.com/HuaJiang2003/nav2_simulation.git
```

    克隆仓库后FAST_LIO_ROS2可能缺失ikd-Tree，在目录src/FAST_LIO_ROS2/include/ikd-Tree下，请自行down一个。

#### 2.1.3 更改子模块相关参数

    FAST_LIO_ROS2：更改lidar和imu的接收话题，本工程中为"/mid360_PointCloud2"和"/imu",use_sim_time:=True!!!

### 2.2运行

安装完成依赖后，可进行构建和运行。

1. Build the Livox ROS Driver 2:

```shell
cd src/livox_ros_driver2
source /opt/ros/humble/setup.sh
./build.sh humble
```

2. 构建功能包

```shell
cd ~/ros_ws
colcon build
```

3. 运行仿真

```shell
source install/setup.bash
ros2 launch fishbot_description gazebo_sim.launch.py
```

4. 建图

- 新建终端 启动fastlio建图功能

```shell
source install/setup.bash
ros2 launch fast_lio mapping.launch.py 
```

- 新建终端 键盘控制节点（控制机器人移动）

```shell
ros2 run teleop_twist_keyboard teleop_twist_keyboard 
```

- 新建终端 保存地图

    打开 RQt 并选择 Plugins->Services->Service Caller. 触发 service/map_save, pcd地图即可保存。

5. 一些环境上的注意事项，我安装了conda，所以这里较乱

```shell
# 若gazebo无法启动，请运行下面的指令
source /usr/share/gazebo/setup.bash
# 若系统中安装有虚拟环境请删除build文件夹后运行下面的指令
source /opt/ros/humble/setup.bash
```

## 3.效果演示

![环境与建图效果](https://raw.githubusercontent.com/HuaJiang2003/nav2_simulation/main/fishbot_description/images/4.png)

## 4.作者

- [jh](https://github.com/HuaJiang2003)