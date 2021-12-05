---
title: 介绍
---

## 系统环境

- 虚拟机：VMware 16.x
- 操作系统：ubuntu 18.04
- 机器人操作系统：melodic

## [虚拟机](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)

### 安装

注册码网上搜

## [操作系统](https://mirrors.ustc.edu.cn/ubuntu-releases/18.04)

### 安装

### 换源

打开类似`软件和更新`的应用，选择国内源

### 安装增强工具(虚拟机)

```bash
sudo apt install -y open-vm-tools open-vm-tools-desktop
```

## [机器人操作系统](http://wiki.ros.org/cn/melodic/Installation/Ubuntu)

### 配置Ubuntu软件仓库

配置你的Ubuntu软件仓库（repositories）以允许使用“restricted”“universe”和“multiverse”存储库。你可以根据Ubuntu软件仓库指南来完成这项工作。

### 设置sources.list

```bash
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
```

### 设置密钥

```bash
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

### 安装

首先更新软件源

```bash
sudo apt update
```

然后根据需求安装机器人操作系统

- 桌面完整版（推荐）： : 包含 ROS、rqt、rviz、机器人通用库、2D/3D 模拟器、导航以及 2D/3D 感知包。

    ```bash
    sudo apt install ros-melodic-desktop-full
    ```

- 桌面版： 包含 ROS，rqt，rviz 和机器人通用库

    ```bash
    sudo apt install ros-melodic-desktop
    ```

- ROS-基础包： 包含 ROS 包，构建和通信库。没有图形界面工具。

    ```bash
    sudo apt install ros-melodic-ros-base
    ```

- 单独的包： 你也可以安装某个指定的ROS软件包（使用软件包名称替换掉下面的PACKAGE）：

    ```bash
    sudo apt install ros-melodic-PACKAGE
    ```

### 初始化 rosdep

安装国内版 `rosdep`

```bash
sudo apt install -y python3-pip
pip3 install rosdepc -i https://pypi.mirrors.ustc.edu.cn/simple
```

执行初始化

```bash
sudo rosdepc init
rosdepc update
```

### 设置环境

- bash

    ```bash
    echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
    source ~/.bashrc
    ```

- zsh

    ```zsh
    echo "source /opt/ros/melodic/setup.zsh" >> ~/.zshrc
    source ~/.zshrc
    ```

### 安装开发依赖

```bash
sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool build-essential
```
