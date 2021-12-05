---
title: 实验四
---

```bash
roslaunch realsense2_camera rs_camera.launch
```

## 颜色识别

```py title="scripts/ros-opencv-color.py"
#!/usr/bin/env python
import rospy
import cv2
import numpy as np
from cv_bridge import CvBridge, CvBridgeError
from sensor_msgs.msg import Image
class rosopencvcolor():
    def __init__(self):
        rospy.init_node('ros_opencv_color')
        self.cv_bridge = CvBridge()
        rospy.Subscriber('/camera/color/image_raw', Image, self.color_img_cb)
        self.lane_img_pub = rospy.Publisher('/lane_image', Image, queue_size=5)
    def color_img_cb(self, data):
        try:
            cv_img = self.cv_bridge.imgmsg_to_cv2(data, 'bgr8')
        except CvBridgeError as e:
            print e
        height, width, channels = cv_img.shape
        lower = np.array([0,0,140], dtype = "uint8")
        upper = np.array([255, 255, 255], dtype = "uint8")
        mask = cv2.inRange(cv_img, lower, upper)
        extraction = cv2.bitwise_and(cv_img, cv_img, mask = mask)
        if not rospy.is_shutdown():
            self.lane_img_pub.publish(self.cv_bridge.cv2_to_imgmsg(np.hstack([cv_img, extraction]), "bgr8"))
            cv2.imshow("Image window", np.hstack([cv_img, extraction]))
            cv2.imshow('test', cv_img)
            cv2.waitKey(1)
if __name__ == '__main__':
    rosopencvcolor()
    rospy.spin()
    cv2.destroyAllWindows()
```

## 轮廓识别

```py title="scripts/ros-opencv-findcontours.py"
#!/usr/bin/env python
import os
import math
import rospy
import cv2
import numpy as np
from cv_bridge import CvBridge, CvBridgeError
from sensor_msgs.msg import Image
class rosopencvfindcontours():
    def __init__(self):
        rospy.init_node('ros_opencv_findcontours')
        self.cv_bridge = CvBridge()
        rospy.Subscriber('/camera/color/image_raw', Image, self.color_img_cb)
        self.lane_img_pub = rospy.Publisher('/lane_image_findcontours', Image, queue_size=5)
    def color_img_cb(self, data):
        try:
            cv_img = self.cv_bridge.imgmsg_to_cv2(data, 'bgr8')
        except CvBridgeError as e:
            print e
        ret, thresh = cv2.threshold(cv2.cvtColor(cv2.GaussianBlur(cv_img, (5, 5), 0), cv2.COLOR_BGR2GRAY), 127, 255, cv2.THRESH_BINARY)
        img_, contours, hierarchy = cv2.findContours(thresh, cv2.RETR_LIST, cv2.CHAIN_APPROX_SIMPLE)
        cv2.drawContours(cv_img, contours, -1, (255, 120, 0), 3)
        cv2.imshow('Image', np.hstack((cv2.cvtColor(thresh, cv2.COLOR_GRAY2BGR), cv_img)))
        cv2.waitKey(1)
if __name__ == '__main__':
    rosopencvfindcontours()
    rospy.spin()
    cv2.destroyAllWindows()
```
