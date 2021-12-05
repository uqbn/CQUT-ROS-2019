---
title: 实验一
---

## [通过键盘控制小乌龟运动实验过程与结果截图](http://wiki.ros.org/cn/ROS/Tutorials/UnderstandingTopics)

1. 打开一个新终端，启动主节点

    ```bash
    roscore
    ```

2. 打开一个新终端，启动`turtlesim`节点

    ```bash
    rosrun turtlesim turtlesim_node
    ```

3. 打开一个新终端，运行按键控制

    ```bash
    rosrun turtlesim turtle_teleop_key
    ```

## 通过`rostopic pub`命令控制小乌龟运动过程与结果截图

列出所有发布(Published)和订阅(Subscribed)的主题及其类型的详细信息

```bash
rostopic list -v
```

将得到以下信息

```txt
Published topics:
 * /turtle1/color_sensor [turtlesim/Color] 1 publisher
 * /rosout [rosgraph_msgs/Log] 1 publisher
 * /rosout_agg [rosgraph_msgs/Log] 1 publisher
 * /turtle1/pose [turtlesim/Pose] 1 publisher
Subscribed topics:
 * /turtle1/cmd_vel [geometry_msgs/Twist] 1 subscriber
 * /rosout [rosgraph_msgs/Log] 1 subscribe
```

我们可以得知 `* 话题名称 [消息类型] 发布或订阅者的数量`

使用`rosmsg`查看消息的详细信息

```bash
rosmsg show geometry_msgs/Twist
```

将得到以下信息

```yml
geometry_msgs/Vector3 linear
    float64 x
    float64 y
    float64 z
geometry_msgs/Vector3 angular
    float64 x
    float64 y
    float64 z
```

我们需要传递三个线速度和三个角速度

```bash
rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'
```

以上命令会发送一条消息给`turtlesim`，告诉它以`2.0`大小的线速度和`1.8`大小的角速度移动。

- 这条命令将消息发布到指定的话题：

    ```bash
    rostopic pub
    ```

- 这一选项会让rostopic只发布一条消息，然后退出：

    ```bash
    -1
    ```

- 这是要发布到的话题的名称：

    ```bash
    /turtle1/cmd_vel
    ```

- 这是发布到话题时要使用的消息的类型：

    ```bash
    geometry_msgs/Twist
    ```

- 这一选项（两个破折号）用来告诉选项解析器，表明之后的参数都不是选项。如果参数前有破折号（-）比如负数，那么这是必需的。

    ```bash
    --
    ```

- 如前所述，一个turtlesim/Velocity消息有两个浮点型元素：linear和angular。在本例中，'[2.0, 0.0, 0.0]'表示linear的值为x=2.0, y=0.0, z=0.0，而'[0.0, 0.0, 1.8]'是说angular的值为x=0.0, y=0.0, z=1.8。这些参数实际上使用的是[YAML](http://wiki.ros.org/ROS/YAMLCommandLine)语法，在YAML命令行文档中有描述。

    ```bash
    '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'
    ```

```bash
rostopic pub /turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'
```

这将以1 Hz的速度发布velocity指令到velocity话题上。

[画一个正方形](http://wiki.ros.org/cn/ROS/Tutorials/UnderstandingServicesParams)

```bash
rosservice call turtle1/teleport_relative -- 2 0
rosservice call turtle1/teleport_relative -- 0 1.57075
rosservice call turtle1/teleport_relative -- 2 0
rosservice call turtle1/teleport_relative -- 0 1.57075
rosservice call turtle1/teleport_relative -- 2 0
rosservice call turtle1/teleport_relative -- 0 1.57075
rosservice call turtle1/teleport_relative -- 2 0
```

## 通过键盘控制omove移动机器人的运动实验过程与结果截图

1. 移动机器人的NUC控制器插上显示屏和键盘鼠标，开机后连接路由器wifi或手机热点；
2. 通过`ifconfig`或者`ip addr`命令，查看当前控制器的IP地址
3. 保持机器人开机状态，拆除显示屏和键鼠。实验过程中不要关闭wifi热点，保持网络顺畅。
4. 在自己电脑上通过命令`#!bash ssh omove@$ip`远程登录到控制器，`$ip`指第2步查询的IP地址，提示输入密码，输入密码`1`，回车确认。
5. 在控制器中，启动`omove_driver`功能包中的`omove_driver.launch`文件。

    ```bash
    roslaunch omove_driver omove_driver.launch
    ```

6. 在控制器中，启动键盘控制节点`teleop_twist_keyboard`通过键盘按键控制移动机器人运动。

    ```bash
    rosrun teleop_twist_keyboard teleop_twist_keyboard.py
    ```

## 通过`rostopic pub`命令控制omove移动机器人运动过程与结果截图

通过`rostopic pub`命令的方式控制移动机器人运动。

```bash
```
