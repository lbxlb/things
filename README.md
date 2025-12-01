# 生物模型交互与ROS 2集成项目

## 项目概述

本项目融合了基于MuJoCo的人体上肢物理仿真与ROS 2机器人操作系统，实现了兼具高精度食指追踪功能与分布式通信能力的综合仿真系统。项目核心包含两大部分：一是通过MediaPipe手部追踪驱动的食指指向仿真，二是基于ROS 2的关节数据发布与感知处理模块，可实现虚拟模型与外部系统的实时数据交互。

### 演示视频

- **食指指向功能演示**：
https://private-user-images.githubusercontent.com/221759988/520554004-6e1964c4-4a01-4905-afb3-ba1b4de3c089.gif?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjQ1NTM4NjcsIm5iZiI6MTc2NDU1MzU2NywicGF0aCI6Ii8yMjE3NTk5ODgvNTIwNTU0MDA0LTZlMTk2NGM0LTRhMDEtNDkwNS1hZmIzLWJhMWI0ZGUzYzA4OS5naWY_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMjAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTIwMVQwMTQ2MDdaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kOGRjZTIyZWVmYmVjODgxOTkwOGI1NDU4MzNiN2I0YzYxNzQxNWQ5NzY0NmVjNjZjNTU0ZWYwYWQwZjE5NmUxJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.g1FsAwKBBljVU2fKFTLreuZ2c_c4lPKkNorevi5X450

## 主要特点

- **高精度骨骼模型**：包含上肢完整骨骼结构，细化手部掌骨、指骨（近节/中节/远节）的几何与物理参数
- **增强型肌肉驱动**：新增食指相关肌肉群（FDPI、EDM等）的肌腱路径定义，提升指向动作真实性
- **实时手势映射**：集成MediaPipe手部追踪，将真实手势实时映射到虚拟模型
- **目标追踪功能**：支持配置目标坐标，实现食指自动指向指定位置
- **ROS 2分布式通信**：通过ROS 2节点实现关节数据发布、感知数据处理与外部系统交互
- **模块化设计**：保持与[User-in-the-Box](https://github.com/User-in-the-Box/user-in-the-box)原项目的兼容性，同时扩展ROS 2接口

## 文件说明

| 文件名 | 描述 |
|--------|------|
| **config.yaml** | 仿真参数配置文件，包含仿真步长（dt）、渲染模式、窗口分辨率及目标位置（target_pos）等 |
| **simulation.xml** | MuJoCo模型定义文件，包含骨骼结构、肌肉肌腱参数、标记点及关节活动范围等核心信息 |
| **evaluator.py** | 程序入口脚本，通过命令行参数接收配置文件和模型文件路径，初始化并启动仿真 |
| **simulator.py** | 仿真器核心逻辑，包含MuJoCo环境初始化、Viewer适配、仿真循环控制及手势映射功能 |
| **assets/** | 模型资源文件夹，存放网格文件（.stl）和纹理文件，定义骨骼与手部的几何形状 |
| **mujoco_ros_demo/** | ROS 2功能包目录 |


## 模型结构

仿真模型定义在`simulation.xml`中，核心组件包括：
- **骨骼结构**：详细的上肢骨骼（锁骨、尺骨、掌骨及指骨等），通过网格文件定义几何形状
- **标记点（sites）**：用于定位肌肉附着点和关键位置（如手指关节、肌肉路径点）
- **肌腱与肌肉**：定义主要肌肉的路径（三角肌、肱二头肌等）及物理参数，实现逼真驱动
- **关节**：定义各关节的活动范围和轴方向（如肘关节弯曲角度限制）

## 系统要求

- Ubuntu 22.04 LTS (ROS 2 Humble)
- Python 3.8+（3.10.18）
- MuJoCo 2.3.0+（3.3.7）
- OpenCV
- MediaPipe
- NumPy
- PyYAML
- Conda 环境管理器

## 安装步骤

### 1. 克隆仓库

```bash
# 克隆主项目
git clone https://github.com/yourusername/mobl-arms-index-pointing.git
cd mobl-arms-index-pointing

# 克隆依赖项目（如需要）
git clone https://github.com/User-in-the-Box/user-in-the-box.git
```

### 2. 安装ROS 2 Humble

按照官方指南安装：https://docs.ros.org/en/humble/Installation.html

### 3. 创建并配置环境

```bash
# 创建并激活conda环境
conda create -n mjoco_ros python=3.10
conda activate mjoco_ros

# 安装核心依赖
pip install mujoco mediapipe numpy opencv-python pyyaml

# 安装ROS 2相关依赖（如需要）
sudo apt install ros-humble-ros-base

# 安装项目包
pip install -e .
```

### 4. 模型文件准备

完整模型文件集（包含40+手部精细模型文件）可通过以下链接下载：
[完整模型文件集网盘链接](https://pan.baidu.com/s/1SN5SWpyfKR7KYDbE8lzvBw?pwd=9e9u)

下载后解压到 `mujoco_ros_demo/config/assets/` 目录

## 配置说明

### 仿真参数配置（config.yaml）

```yaml
dt: 0.05                # 仿真步长（越小精度越高，性能消耗越大）
render_mode: "human"    # 渲染模式（"human"显示窗口，"offscreen"无窗口运行）
resolution: [1280, 960] # 窗口分辨率 [宽度, 高度]
target_pos: [0.4, 0, 0.7] # 食指追踪目标坐标
```

### ROS 2功能包结构

```
mujoco_ros_demo/
├── config/
│   ├── assets/           # 3D模型文件(STL格式)
│   └── humanoid.xml      # 机器人模型配置文件
├── launch/
│   └── main.launch.py    # ROS2启动文件
├── mujoco_ros_demo/
│   ├── __init__.py
│   ├── mujoco_publisher.py   # 关节角度发布节点
│   ├── data_subscriber.py    # 关节数据订阅节点
│   ├── data_acquire.py       # 外部数据采集节点
│   └── perception_node.py    # 感知处理节点
├── package.xml           # ROS2包配置
└── setup.py              # 安装配置
```

## 节点说明

1. **MujocoPublisher** (mujoco_publisher.py)
   - 功能：加载MuJoCo模型并发布关节角度数据
   - 发布主题：/joint_angles (std_msgs/Float64MultiArray)
   - 参数：model_path - 机器人模型文件路径

2. **DataSubscriber** (data_subscriber.py)
   - 功能：订阅关节角度数据并计算平均值
   - 订阅主题：/joint_angles (std_msgs/Float64MultiArray)
   - 输出：终端打印关节角度及平均值

3. **DataAcquire** (data_acquire.py)
   - 功能：采集外部传感器或设备数据（如手势追踪原始数据）
   - 发布主题：/raw_sensor_data (自定义消息类型)
   - 支持：MediaPipe原始数据、外部传感器输入等
  https://private-user-images.githubusercontent.com/221759988/520554001-b4dee659-670e-4c0a-ab8a-a7c5126004dc.gif?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjQ1NTI3NjIsIm5iZiI6MTc2NDU1MjQ2MiwicGF0aCI6Ii8yMjE3NTk5ODgvNTIwNTU0MDAxLWI0ZGVlNjU5LTY3MGUtNGMwYS1hYjhhLWE3YzUxMjYwMDRkYy5naWY_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMjAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTIwMVQwMTI3NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1mNTJlMzliYjIwNTdiMTBhMWEyOWU0NDUyOWU3ZjcwZDRjOGNjMmIxNDYzZjRjMmZjNWU0NzUwNjRhM2VlNDVkJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.sk_HP_beY5f4_Sj_KCKGfOjLsQHaucDq5Bb65hCsXIM

4. **PerceptionNode** (perception_node.py)
   - 功能：处理感知数据，实现手势识别与解析
   - 订阅主题：/raw_sensor_data
   - 发布主题：/processed_gesture (包含解析后的手势指令)
   https://private-user-images.githubusercontent.com/221759988/520554003-af9bf70d-ba00-4abc-94a1-a185fdc5fc65.gif?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjQ1NTI3NjIsIm5iZiI6MTc2NDU1MjQ2MiwicGF0aCI6Ii8yMjE3NTk5ODgvNTIwNTU0MDAzLWFmOWJmNzBkLWJhMDAtNGFiYy05NGExLWExODVmZGM1ZmM2NS5naWY_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMjAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTIwMVQwMTI3NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1hZjgyMDEzZjRjZDRhYzg4OTZjZmM3OWNiYTNlZGEwNjhiNTRhY2ZmZTAwNDE2YWNmMzQyNWM3OTkxNGJmYzgzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.o_TfpXA5Yt-Ufng_a3iwQKQliXguzvH1ossrj_qedQs

6. **Main** (main.launch.py)
  - 功能：启动ROS 2系统并连接各个节点
  - 启动节点：MujocoPublisher、DataSubscriber
 https://private-user-images.githubusercontent.com/221759988/520554002-6d851842-9d47-4847-8fb6-8a4475a25e9f.gif?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjQ1NTI3NjIsIm5iZiI6MTc2NDU1MjQ2MiwicGF0aCI6Ii8yMjE3NTk5ODgvNTIwNTU0MDAyLTZkODUxODQyLTlkNDctNDg0Ny04ZmI2LThhNDQ3NWEyNWU5Zi5naWY_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMjAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTIwMVQwMTI3NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kMzRmNmVkOWQ3OWEwNTBmODgyODQ1NWJiNDYxZTdkZGIwOTMxOTU3NzNkMWFlOTg5YjlkNGZiYjYxMDg2ZjU3JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.mS7aSJXiNI6-dplx0yoFkWuJ39FGG2_lKQ60UYtcAq8



## 使用方法

### 1. 基础仿真运行

```bash
python evaluator.py --config config.yaml --model simulation.xml
```

### 2. ROS 2系统运行

```bash
# 构建项目
colcon build
source install/setup.bash

# 启动ROS 2节点
ros2 launch mujoco_ros_demo main.launch.py
```

### 3. 查看运行状态

- 查看节点信息：`ros2 node list`
- 查看话题信息：`ros2 topic list`
- 可视化工具：`rqt`

## 项目来源/参考

- [MuJoCo](https://github.com/deepmind/mujoco) - 高性能物理引擎
- [ROS 2](https://github.com/ros2) - 机器人操作系统
- [User-in-the-Box](https://github.com/User-in-the-Box/user-in-the-box) - 基础模型参考

## 许可证

本项目基于Apache-2.0许可证发布。