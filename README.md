为满足睿尔曼机械臂在ROS2中的使用，为高校提供更先进的机械臂集成系统研究方案，根据RM-65， RM-75等系列机械臂原有ROS1包架构，提出以下ROS2架构升级计划
|Info| Content |
|--|--|
| Author | Herman Ye @Realman Robotics |
| Version| 1.0 |
| Date| 2023-7-19 |

# 目录



- [ROS包的版本和分支管理策略](#ROS包的版本和分支管理策略)
	- [历史版本管理](#历史版本管理)
	  - [ROS 1 Melodic分支](#ros-1-melodic分支)
	  - [ROS 1 Kinetic分支](#ros-1-kinetic分支)
	- [ROS 1 Noetic版本管理](#ros-1-noetic版本管理)
	  - [ROS 1 Noetic开发分支](#ros-1-noetic开发分支)
	  - [ROS 1 Noetic稳定分支](#ros-1-noetic稳定分支)
	- [ROS 2 Humble版本管理](#ros-2-humble版本管理)
	  - [ROS 2 Humble开发分支](#ros-2-humble开发分支)
	  - [ROS 2 Humble稳定分支](#ros-2-humble稳定分支)
	- [对新机械臂的包管理策略](#对新机械臂的包管理策略)











<p id="ROS包的版本和分支管理策略"></p>  

# ROS包的版本和分支管理策略

以下是针对睿尔曼机械臂ROS包的版本和分支管理策略，目标是保持代码的稳定性，同时便于开发和维护。

<p id="历史版本管理"></p>
  
## 历史版本管理

### ROS 1 Melodic分支
  - 分支名：`melodic`
  - 状态：Frozen（冻结）
  - 描述：保留原有的ROS Melodic版本的代码，进行内容检查后冻结，不再进行开发和更新。

### ROS 1 Kinetic分支
  - 分支名：`kinetic`
  - 状态：Frozen（冻结）
  - 描述：保留原有的ROS Kinetic版本的代码，进行内容检查后冻结，不再进行开发和更新。

## ROS 1 Noetic版本管理

### ROS 1 Noetic开发分支
  - 分支名：`noetic-dev`
  - 状态：Developing（开发中）
  - 描述：基于Melodic和Kinetic版本的内容，用于进行Noetic版本的开发和测试。

### ROS 1 Noetic稳定分支
  - 分支名：`noetic`
  - 状态：Stable（稳定）
  - 描述：将`noetic-dev`分支的经过验证的稳定版本迁移到此分支。

## ROS 2 Humble版本管理

### ROS 2 Humble开发分支
  - 分支名：`humble-dev`
  - 状态：Developing（开发中）
  - 描述：用于开发和测试ROS 2 Humble版本的代码。

### ROS 2 Humble稳定分支
  - 分支名：`humble`
  - 状态：Stable（稳定）
  - 描述：将`humble-dev`分支的经过验证的稳定版本迁移到此分支。

## 对新机械臂的包管理策略

对于新开发的机械臂，建议根据以下表格进行版本管理：

|ROS Version| Branch | Status|
|---|---|---|
| ROS 1 | noetic-dev|Developing|
| ROS 1 | noetic|Stable|
| ROS 2 | humble-dev|Developing|
| ROS 2 | humble|Stable|

这意味着，对于每款新的机械臂，只维护以上4个分支。
当然，根据客户的实际需求，考虑是否需要维护Ubuntu 16.04或18.04对应的ROS版本。如果不需要，就只维护这4个分支，以减少维护成本。




硬件抽象层（rm65_hardware_interface包）
驱动层（rm65_driver包）
运动控制和规划层（使用moveit2包）
此包将包含运动规划的配置文件，包括机械臂的关节和连接、运动学解算器、碰撞检测等相关配置。
应用层(pick_and_place)
# rm
## rm_install
## rm_bringup
## rm_driver
这个包是直接与机械臂的控制器通信，从而获取数据并发送指令
在connect函数中，实现了对机械臂的连接，包括实例化了一些服务和操作，并初始化了机械臂的状态。

disconnect_handler函数用于处理断开机械臂的连接。在断开前，会检查是否有控制器正在控制机械臂。如果有，就不允许断开。

connect_handler函数用于处理连接机械臂。在连接前，会检查是否已经连接了机械臂。如果已经连接，就不允许重复连接。

接下来的部分，实现了ROS的服务，用于处理机械臂的连接和断开。

之后，实例化了一个控制器管理器，并启动了一个异步spinner，用于处理ROS的消息。

在主循环中，首先检查机械臂是否有控制器正在控制，以及是否有未处理的错误。如果条件满足，就进入到一个子循环中，更新机械臂的状态，以及控制器的状态。并根据机械臂的模式，处理机械臂的错误。如果机械臂断开连接，就让线程休眠一段时间。

当有控制器正在控制机械臂，且没有未处理的错误时，就执行控制循环，不断更新机械臂和控制器的状态。如果在控制过程中出现了控制异常，就将错误标记设为真，从而在下一次主循环中，处理这个错误。

整个程序的设计思路是将连接、断开、控制、错误恢复等操作封装成ROS的服务和操作，使得其他ROS节点可以通过发送服务请求或者操作目标，来控制机械臂的行为。
##  rm_simulation
### rm_gazebo
### rm_issac_sim
## rm_example
## rm_docs
## rm_driver
此类将与硬件抽象层通信，执行如启动、停止、移动机械臂等任务。
并接收其他节点的命令，控制机械臂。
## rm_ros_interfaces
## ur_kinematics
## rm_calibration
## rm_hardware_interface
api
包含与机械臂硬件交互的方法。包括读取传感器数据，发送控制命令等

## rm_ai
## rm_moveit_config
## camera
## rm_config
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4a9633eb48143c1aea1f7da28d386f9.png)
以下是一个对上述四层架构的更详细说明：

1. **硬件抽象层（`rm65_hardware_interface`包）**：

- `RM65Hardware` 类（C++）：此类将包含与机械臂硬件交互的方法。包括读取传感器数据，发送控制命令等。

- `RM65Status.msg`：定义硬件状态的ROS消息类型。例如，包含关节角度，电机温度，错误码等。

- `RM65Control.srv`：定义控制硬件的ROS服务类型。例如，服务请求可能包含目标关节角度，服务响应可能包含执行状态。

2. **驱动层（`rm65_driver`包）**：

- `RM65Driver` 类（C++）：此类将与硬件抽象层通信，执行如启动、停止、移动机械臂等任务。

- `rm65_driver_node`：这是一个ROS节点，创建一个 `RM65Driver` 对象，并接收其他节点的命令，控制机械臂。

3. **运动控制和规划层（使用`moveit2`包）**：

首先，你需要为RM-65型机械臂创建一个URDF（Unified Robot Description Format）模型。然后，可以使用MoveIt Setup Assistant来创建MoveIt配置，这个过程会生成一个新的ROS2包（例如，叫做 `rm65_moveit_config`）。

- `rm65_moveit_config`包：此包将包含运动规划的配置文件，包括机械臂的关节和连接、运动学解算器、碰撞检测等相关配置。

- `move_group`节点：MoveIt 提供了一个名为 `move_group` 的节点，该节点提供运动规划和执行的服务。

4. **应用层**：

这个层次的内容完全取决于你的具体应用。比如，你可能需要创建一个名为 `rm65_pick_and_place` 的包，该包包含一个 `PickAndPlace` 类（C++），它使用 `move_group` 提供的服务来规划和执行拾取和放置的动作。

最后，还需要一些 `.launch.py` 文件来启动上述节点。例如，你可能会有一个 `rm65_bringup.launch.py` 文件，当运行此文件时，它将启动 `rm65_driver_node` 和 `move_group` 节点。
硬件组件划分 软件组件划分

1.package介绍
序号	名称	作用
1	rm_65_description	RM65-B机器人描述功能包，其中有创建好的机器人模型和配置文件
rm_65.urdf.xacro：不带手爪的RM65-B机器人模型文件
2	rm_65_moveit_config	使用Setup Assistant工具根据机械臂URDF模型rm_65.urdf.xacro创建生成的一个MoveIt!配置的功能包，它包含了大部分MoveIt!启动所需的配置文件和启动文件，以及包含一个简单的演示demo
3	rm_gazebo	gazebo仿真机器人所用到参数和文件配置
4	rm_65_demo	MoveIt!编程示例，包括场景规划、避障规划和pick and place
5	rm_msgs	RM65-B所用到的所有控制消息和状态消息
6	rm_control	机器人控制器，将Moveit规划的机械臂轨迹，通过三次样条插值细分，按照20ms的控制周期发给rm_driver节点，周期可调，但是应大于10ms
7	rm_driver	（1）与机械臂通过以太网口建立socket连接，机器人IP地址：192.168.1.18，请保证上位机的IP在同一局域网内，使用ROS控制机械臂，请务必确认机械臂处于以太网口通信模式；                                                                         （2）订阅各topic数据，更新RVIZ内机械臂各关节角度
8	rm_bringup	启动机器人，运行对应的launch文件后，可自动运行rm_driver，rm_control和moveit交互RVIZ界面，直接在仿真界面中拖拽机器人即可控制真实机器人运动

在升级到ROS2时，需要注意ROS2有许多新的功能和改进，其中包括更强大的通信层，改进的安全性和真实时间支持等。不过在实际的转换过程中，大部分的ROS1代码可以直接迁移到ROS2，只需要针对一些具体的ROS2的新特性和API更改进行调整。以下是一份基于当前ROS1包架构的ROS2架构重构详细方案：

1. rm_65_description：这个包应该没有太多需要修改的地方，主要是提供URDF/XACRO模型文件，这些文件在ROS1和ROS2之间是兼容的。你可能需要更新包的CMakeLists.txt和package.xml文件以便使用ament工具构建，这是ROS2的构建工具。

2. rm_65_moveit_config：ROS2的MoveIt版本是MoveIt 2，它基本上是功能完全相同的ROS2兼容版本。你需要使用MoveIt 2的Setup Assistant来生成新的配置文件，但大部分的设置和ROS1版本应该是一样的。同时，你可能需要更新一些MoveIt的启动文件以便使用ROS2的参数和启动系统。

3. rm_gazebo：对于Gazebo仿真，你需要将其升级为Gazebo ROS2的版本。一般来说，你需要对ROS2和Gazebo接口进行一些更新，但大部分仿真环境和模型应该可以直接迁移。

4. rm_65_demo：同样的，这个包主要是MoveIt的演示，因此需要基于MoveIt 2进行更新。你可能需要更新一些API调用和消息类型，但基本上的功能应该可以直接迁移。

5. rm_msgs：在ROS2中，消息定义基本上没有变化，但是需要注意的是，在ROS2中，消息和服务都是在单独的包中定义的，这意味着你需要创建一个新的包来包含你的消息和服务定义。同时，消息和服务的构建系统也有所不同，你需要更新CMakeLists.txt和package.xml以使用ament。

6. rm_control：这个包主要处理机械臂的控制器，需要根据ROS2的特性和API进行一些更新。特别地，你需要注意ROS2的执行器模型和生命周期管理，它们可能会影响控制器的设计和实现。

7. rm_driver：同样，这个包需要根据ROS2的API进行更新。你需要注意ROS2的节点模型和参数系统，以及与设备通信的接口和协议。可能需要针对新的通信层和参数系统进行一些调整。

8. rm_bringup：这个包主要用于启动整个系统，需要根据ROS2的启动系统进行更新。ROS2使用Python编写的启动文件，它们比ROS1的XML文件更强大和灵活。你需要将ROS1的launch文件转换为ROS2的Python脚本。

这只是一个概述，实际的迁移工作可能需要更详细的规划和更多的工作。但是，幸运的是，ROS社区提供了大量的教程和指导，以帮助你在ROS2中进行开发和迁移。

|Package| Function |
|--|--|
| rm_description |  |






架构重构参考：
https://github.com/phodal/migration
[Frankaemika arm documentation](https://frankaemika.github.io/docs/)

references
[franka_ros](https://github.com/frankaemika/franka_ros)
[franka_ros2](https://github.com/frankaemika/franka_ros2)
[niryo_one_ros](https://github.com/NiryoRobotics/niryo_one_ros)
[Dobot M1](https://www.dobot.cc/dobot-m1/product-overview.html)
[UR robot](https://github.com/ros-industrial/universal_robot)
[Dobot CR_ROS](https://github.com/Dobot-Arm/CR_ROS)
[rex-gym](https://github.com/nicrusso7/rex-gym)
[Open hand](https://www.eng.yale.edu/grablab/openhand/)
[Dummy robot](https://github.com/peng-zhihui/Dummy-Robot)
[Thor robot](https://github.com/AngelLM/Thor)
[Small robot arm](https://github.com/SkyentificGit/SmallRobotArm)
[Google catgrasp](https://github.com/wenbowen123/catgrasp)
[mini pupper](https://github.com/mangdangroboticsclub/mini_pupper_ros)
[Delta robot](https://github.com/deltaxrobot?tab=repositories)
[Robotic arm scara](https://jjrobots.com/robotic-arm-scara-project-updates/)
[ros llm](https://github.com/Auromix/ROS-LLM)
[pick-place-robot](https://github.com/Salman-H/pick-place-robot)
[RobotArmHelix](https://github.com/Gabryxx7/RobotArmHelix)
[Fabrik2DArduino](https://github.com/henriksod/Fabrik2DArduino)
[BetaBots-Robot-Arm-Project](https://github.com/4ndreas/BetaBots-Robot-Arm-Project)

