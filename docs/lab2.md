---
title: 实验二
---

## [ROS工作空间与环境管理](http://wiki.ros.org/cn/ROS/Tutorials/CreatingPackage)

```bash
mkdir -p ~/catkin_ws/src      #创建工作空间catkin_ws和子目录src，自定义空间名
cd ~/catkin_ws                #进入到工作空间catkin_ws
catkin_make                   #编译工作空间catkin_ws
```

## ROS程序包创建

```bash
cd ~/catkin_ws/src                          #进入程序包目录
catkin_create_pkg lab std_msgs rospy roscpp #创建程序包lab
cd lab                                      #进入程序包lab
```

## [ROS编程话题发布与订阅的代码和分析](http://wiki.ros.org/cn/ROS/Tutorials/WritingPublisherSubscriber%28python%29)

```bash
mkdir scripts
touch scripts/talker.py scripts/listener.py    # 创建空白文件
chmod +x scripts/talker.py scripts/listener.py # 添加执行权限
```

编辑 `scripts/talker.py`

```bash
gedit scripts/talker.py
```

```python title="scripts/talker.py"
#!/usr/bin/env python
# license removed for brevity
import rospy
from std_msgs.msg import String
def talker():
    pub = rospy.Publisher('chatter', String, queue_size=10)
    rospy.init_node('talker', anonymous=True)
    rate = rospy.Rate(10)  # 10hz
    while not rospy.is_shutdown():
        hello_str = "hello world %s" % rospy.get_time()
        rospy.loginfo(hello_str)
        pub.publish(hello_str)
        rate.sleep()
if __name__ == '__main__':
    try:
        talker()
    except rospy.ROSInterruptException:
        pass
```

编辑 `scripts/listener.py`

```bash
gedit scripts/listener.py
```

```py title="scripts/listener.py"
#!/usr/bin/env python
import rospy
from std_msgs.msg import String
def callback(data):
    rospy.loginfo(rospy.get_caller_id() + "I heard %s", data.data)
def listener():

    # In ROS, nodes are uniquely named. If two nodes with the same
    # name are launched, the previous one is kicked off. The
    # anonymous=True flag means that rospy will choose a unique
    # name for our 'listener' node so that multiple listeners can
    # run simultaneously.
    rospy.init_node('listener', anonymous=True)
    rospy.Subscriber("chatter", String, callback)
    # spin() simply keeps python from exiting until this node is stopped
    rospy.spin()
if __name__ == '__main__':
    listener()
```

编辑 `CMakeLists.txt`

```bash
gedit CMakeLists.txt
```

在文件末尾添加以下内容

```cmake title="CMakeLists.txt"
catkin_install_python(PROGRAMS
  scripts/talker.py
  scripts/listener.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
```

编译工程

```bash
cd ~/catkin_ws
catkin_make
```

加载环境

```bash
source devel/setup.bash
```

运行主节点

```bash
roscore
```

运行订阅者

```bash
cd ~/catkin_ws
source devel/setup.bash
rosrun lab listener.py
```

运行发布者

```bash
cd ~/catkin_ws
source devel/setup.bash
rosrun lab talker.py
```

## 编程控制omove移动机器人自主运动

```bash
touch scripts/run.py
chmod +x scripts/run.py
```

```py title="scripts/run.py"
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import rospy
from geometry_msgs.msg import Twist
def run():
    pub = rospy.Publisher('cmd_vel', Twist, queue_size=10)
    rospy.init_node('run', anonymous=True)
    rate = rospy.Rate(10)  # 10hz
    count = 0
    while not rospy.is_shutdown():
        twist = Twist()
        if 0 == count:
            rospy.loginfo("前进")
        elif 20 == count:
            rospy.loginfo("左移")
        elif 40 == count:
            rospy.loginfo("后退")
        elif 60 == count:
            rospy.loginfo("右移")
        if count <= 20:
            twist.linear.x = 1
        elif 20 < count and count <= 40:
            twist.linear.y = 1
        elif 40 < count and count <= 60:
            twist.linear.x = -1
        elif 60 < count and count <= 80:
            twist.linear.y = -1
        else:
            count = -1
        pub.publish(twist)
        rate.sleep()
        count += 1
if __name__ == '__main__':
    try:
        run()
    except rospy.ROSInterruptException:
        pass
```

```cmake title="CMakeLists.txt"
catkin_install_python(PROGRAMS
  scripts/run.py
  scripts/talker.py
  scripts/listener.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
```

上传代码

```bash
scp ~/catkin_ws/src/lib omove@$ip:~/omve_ws/src
```

连接控制器然后编译

```bash
ssh omove@$ip
cd ~/omove_ws
catkin_make
```

启动驱动节点

```bash
roslaunch omove_driver omove_driver.launch
```

运行程序

```bash
rosrun lab run.py
```
