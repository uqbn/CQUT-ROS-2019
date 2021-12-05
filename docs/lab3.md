---
title: 实验三
---

## 笔记本摄像头和omove移动机器人视觉获取图像步骤和截图

装usb_cam程序包

```bash
sudo apt-get install ros-melodic-usb-cam
```

启动测试文件，驱动笔记本电脑摄像头

!!! note
    启动前，请检查右下角电脑的摄像头是否与虚拟机连接

```bash
roslaunch usb_cam usb_cam-test.launch
```

!!! note
    如果摄像头打开后又关闭，请尝试将虚拟机的USB兼容性为`3.0`

启动omove深度视觉驱动文件

```bash
roslaunch realsense2_camera rs_camera.launch
```

通过rqt工具获取图像

```bash
rosrun rqt_image_view rqt_image_view
```

查看图像消息格式

```bash
rosrun rqt_msg rqt_msg
```

## 激光雷达消息获取

启动omove激光雷达和omove小车驱动文件

```bash
roslaunch omove_driver omove_driver_rplidar.launch
```

查看激光雷达消息格式

```bash
rosrun rqt_msg rqt_msg
```

## omove移动机器人视觉传感器、激光雷达和IMU消息格式查看

```bash
rosmsg show sensor_msgs/Image
rosmsg show sensor_msgs/LaserScan
rosmsg show sensor_msgs/Imu
```

## ROS图像和opencv图像的转换原理、方法

- ROS图像转换为opencv图像

    ```py
    cv_image = bridge.imgmsg_to_cv2(image_message, desired_encoding="passthrough")
    ```

- opencv图像转换ROS图像

    ```py
    cv_image = cv2_to_imgmsg(cv_image, encoding="passthrough")
    ```
