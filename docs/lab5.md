---
title: 实验五
---

安装gazebo（如安装完整版ROS系统，则已经安装gazebo，可跳过此步）

```bash
sudo apt-get update
sudo apt-get install ros-melodic-gazebo*
```

## gazebo仿真环境创建

1. 菜单栏选择Edit（编辑）>Building Editor（模拟建筑物编辑器），或使用快捷键Ctrl+B打开环境编辑器
2. 选择调色板区域Wall（墙壁），在界面右上侧二维视图区域单击鼠标左键开始绘制墙壁，移动鼠标可以拖动墙壁，会以橙色高量显示墙壁和其长度，再次单击鼠标左键确认墙壁的终点。可以选择继续移动鼠标以上一面墙壁的终点开始绘制下一面墙壁，也可以单击鼠标右键结束此面墙壁的绘制。
3. 以相同的方法绘制一个封闭仿真环境，绘制完成后可以对墙壁进行编辑和调整，鼠标左键双击需要编辑的墙壁可以弹出检查器，在检查器中可以对墙壁的位置、长度、厚度和高度等参数进行编辑。这里将外墙长度设置为4m，内外墙高度都设置为1m，厚度默认为0.15m。
4. 给墙壁添加材质和纹理特征，选择调色板区域下方Add Texture（添加质地）中的Bricks（砖）选项，然后鼠标左键单击三维视图区域的外墙，即可以为外墙添加砖的纹理和质地。
5. 在菜单栏选择File（文件）>Save As（另存为），另存当前编辑好的仿真环境文件，选择一个保存路径并取名为`my_building`（可自定义名称），保存完成之后可以看到生成一个名为`my_building`的文件夹，包含`model.config`和`model.sdf`两个文件。
6. 新建一个环境，导入之前所创建的建筑物，定好原点，保存为`my_world.sdf`
!!! note
    将`my_building`文件夹复制到用户home目录的`.gazebo/models`路径下，用户即可在Gazebo界面的面板Insert选项卡中看到此模型文件，可直接选中插入到场景窗口中。

## Turtlebot3 Burger机器人运动仿真

安装依赖包

```bash
sudo apt-get install -y ros-melodic-joy ros-melodic-teleop-twist-joy ros-melodic-teleop-twist-keyboard ros-melodic-laser-proc ros-melodic-rgbd-launch ros-melodic-depthimage-to-laserscan ros-melodic-rosserial-arduino ros-melodic-rosserial-python ros-melodic-rosserial-server ros-melodic-rosserial-client ros-melodic-rosserial-msgs ros-melodic-amcl ros-melodic-map-server ros-melodic-move-base ros-melodic-urdf ros-melodic-xacro ros-melodic-compressed-image-transport ros-melodic-rqt-image-view ros-melodic-gmapping ros-melodic-navigation ros-melodic-interactive-markers
```

创建ROS工作空间并下载Turtlebot移动机器人软件包

```bash
mkdir -p ~/turtlebot_ws/src && cd ~/turtlebot_ws/src
git clone https://hub.fastgit.org/ROBOTIS-GIT/turtlebot3_msgs.git
git clone https://hub.fastgit.org/ROBOTIS-GIT/turtlebot3.git
git clone https://hub.fastgit.org/ROBOTIS-GIT/turtlebot3_simulations.git
cd ~/turtlebot_ws && catkin_make
```

运行仿真功能包并观察效果

```bash
cd ~/turtlebot_ws
source devel/setup.bash
export TURTLEBOT3_MODEL=burger     #选择导入burger model
roslaunch turtlebot3_gazebo turtlebot3_world.launch
```

键盘控制仿真环境中的机器人运动

```bash
source devel/setup.bash
export TURTLEBOT3_MODEL=burger
rosrun turtlebot3_teleop turtlebot3_teleop_key
```

修改仿真环境模型为自己创建的仿真环境

1. 将之前创建的`my_world.sdf`文件复制到`~/turtlebot_ws/src/turtlebot3_simulations/turtlebot3_gazebo/worlds/`目录下，打开`~/turtlebot_ws/src/turtlebot3_simulations/turtlebot3_gazebo/launch/turtlebot3_world.launch`文件，将其中加载仿真环境的命令改为：

    ```xml
    <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/my_world.sdf"/>
    ```

2. 在此文件中修改加载Turtlebot3模型在仿真环境中的初始位置：

    ```xml
    <arg name="x_pos" default="0.0"/>
    <arg name="y_pos" default="0.0"/>
    <arg name="z_pos" default="0.0"/>
    ```

3. 再次运行仿真功能包并观察效果

    ```bash
    source devel/setup.bash
    export TURTLEBOT3_MODEL=burger
    roslaunch turtlebot3_gazebo turtlebot3_world.launch
    ```

## Turtlebot3 Burger机器人地图构建和自动导航过程

1. 保持仿真环境和键盘控制窗口运行，打开一个新的终端窗口运行slam文件：

    ```bash
    source devel/setup.bash
    export TURTLEBOT3_MODEL=burger
    roslaunch turtlebot3_slam turtlebot3_slam.launch
    ```

2. 键盘控制机器人在环境中运动，创建满意的地图后用命令保存地图：

    ```bash
    source devel/setup.bash
    rosrun map_server map_saver -f ~/turtlebot_ws/src/turtlebot3/turtlebot3_navigation/maps/my_map
    ```

3. 打开`~/turtlebot_ws/src/turtlebot3/turtlebot3_navigation/launch/turtlebot3_navigation.launch`文件，将需要加载的地图改为之前保存的地图：

    ```xml
    <arg name="map_file" default="$(find turtlebot3_navigation)/maps/my_map.yaml"/>
    ```

4. 保持仿真环境运行，关闭其余窗口运行的文件，运行自动导航文件：

    ```bash
    source devel/setup.bash
    export TURTLEBOT3_MODEL=burger
    roslaunch turtlebot3_navigation turtlebot3_navigation.launch
    ```

    鼠标左键选择rviz工具栏的`2D Pose Estimate`重定位仿真环境中机器人位置和地图中一致，然后选择rviz工具栏中`2D Nav Goal`选项，长按鼠标左键在地图上为机器人指定一个导航目标点。松开鼠标后，在短时间内规划出了一条最优的路径，机器人开始向目标点运动。
