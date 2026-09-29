
### Autonomous Vision-Guided Line Tracking & Reactive Obstacle Avoidance

```markdown
![ROS 2 Humble](https://img.shields.io/badge/ROS_2-Humble-blue.svg)
![Python 3.10](https://img.shields.io/badge/Language-Python_3.10-yellow.svg)
![OpenCV 4.x](https://img.shields.io/badge/Vision-OpenCV_4.x-red.svg)
![Gazebo Simulator](https://img.shields.io/badge/Simulator-Gazebo_Classic-orange.svg)
![License](https://img.shields.io/badge/License-Apache_2.0-red.svg)

An industrial ROS 2 vision navigation package combining real-time monocular camera image processing with closed-loop angular tracking and reactive LiDAR distance obstacle clearance for differential-drive mobile platforms.

```

---

## 🏗 System Architecture & Closed-Loop Control Pipeline

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     Target Platform / Gazebo Sim                        │
│               (Monocular RGB Camera + LiDAR Range Array)                │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ Topic: /camera/image_raw (sensor_msgs/Image)
                                     │ Topic: /scan (sensor_msgs/msg/LaserScan)
┌────────────────────────────────────▼────────────────────────────────────┐
│                    OpenCV Image Processing Node                         │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ HSV Color Space Thresholding & Morphological Filtering            │  │
│  │ Region of Interest (ROI) Masking & Centroid Extraction            │  │
│  │ Track Error Calculation: e_track = cx_target - cx_frame           │  │
│  └─────────────────────────────────┬─────────────────────────────────┘  │
└────────────────────────────────────┼────────────────────────────────────┘
                                     │ Pixel Error Vector & Laser Distances
┌────────────────────────────────────▼────────────────────────────────────┐
│              Vision Line-Tracking & Reactive Safety Node                │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ Closed-Loop Proportional Angular Steering Controller              │  │
│  │ LiDAR Proximity Safety Intercept: Emergency Braking & Pivot       │  │
│  └─────────────────────────────────┬─────────────────────────────────┘  │
└────────────────────────────────────┼────────────────────────────────────┘
                                     │ Topic: /cmd_vel (geometry_msgs/msg/Twist)
┌────────────────────────────────────▼────────────────────────────────────┐
│                       Robot Motor Controllers                           │
└─────────────────────────────────────────────────────────────────────────┘

```

---

## 🔑 Key Technical Features

* **Real-Time Computer Vision Pipeline:** Processes raw camera streams (`sensor_msgs/msg/Image`) via `cv_bridge` and OpenCV, applying HSV color space segmentation, Gaussian blur noise reduction, and moment centroid extraction to track floor trajectory lines.
* **Closed-Loop Steering Control:** Computes lateral pixel displacement between image center and path centroid to generate continuous proportional angular velocity ($\omega_z$) commands.
* **Multi-Modal Reactive Safety Override:** Intercepts vision tracking commands using raw 2D LiDAR range inputs to execute emergency stops or pivot maneuvers when dynamic obstacles cross the path.
* **Dynamic Speed Saturation:** Automatically scales down linear velocity $v_x$ during high-angular-turn corrections to mitigate path hunting and overshooting at sharp track curves.

---

## 💻 Tech Stack & Interfaces

* **ROS 2 Middleware:** Humble Hawksbill
* **Programming Languages:** Python 3.10 (`rclpy`), OpenCV 4.x
* **Core ROS 2 Interfaces:** `cv_bridge`, `sensor_msgs/msg/Image`, `sensor_msgs/msg/LaserScan`, `geometry_msgs/msg/Twist`
* **Simulation Target:** Gazebo Classic 11 / Differential Drive Robot

---

## 🚀 Quick Start Guide

### Prerequisites

Ensure ROS 2 Humble and Gazebo Classic are installed on Ubuntu 22.04 LTS.

```bash
# 1. Clone repository into workspace
cd ~/ros2_ws/src
git clone [https://github.com/YAGNADATTA25/autonomous-line-tracking-vision-robot.git](https://github.com/YAGNADATTA25/autonomous-line-tracking-vision-robot.git)

# 2. Build workspace
cd ~/ros2_ws
colcon build --symlink-install --packages-select line_tracking
source install/setup.bash

# 3. Launch Simulation & Vision Controller
ros2 launch line_tracking line_tracking.launch.py

```

---
