[mentorpi_github_repository_readme.md](https://github.com/user-attachments/files/35179494/mentorpi_github_repository_readme.md)
# 🤖 MentorPi AI Robot Repository

Welcome to the **MentorPi** repository! This repository contains setup guides, code examples, ROS packages, and tutorials for working with the **Hiwonder MentorPi**—an advanced, ROS-powered AI robot platform equipped with 2D LiDAR, an RGB-D depth camera, multi-wheel kinematics, and AI integration.

---

## 📑 Table of Contents
1. [Overview & Features](#-overview--features)
2. [Getting Started & Assembly](#1-getting-started--assembly)
3. [Development Environment Setup](#2-development-environment-setup)
4. [Network Configuration](#3-network-configuration)
5. [Core Motion & Sensor Integration](#4-core-motion--sensor-integration)
6. [SLAM Mapping & Autonomous Navigation](#5-slam-mapping--autonomous-navigation)
7. [Computer Vision, AI & Autonomous Driving](#6-computer-vision-ai--autonomous-driving)
8. [Large AI Models & Multi-Robot Swarm Control](#7-large-ai-models--multi-robot-swarm-control)
9. [Documentation References](#-documentation-references)

---

## 🌟 Overview & Features

MentorPi is designed for learning and developing cutting-edge robotics applications. Powered by Raspberry Pi / NVIDIA Jetson master controllers and ROS (Robot Operating System), it supports:
- **Kinematics & Motion Control:** Omnidirectional / Mecanum / Ackermann drive support.
- **Perception:** 2D LiDAR point clouds and RGB-D depth camera spatial sensing.
- **Navigation:** SLAM map building, global path planning, and real-time obstacle avoidance.
- **Artificial Intelligence:** OpenCV vision tracking, MediaPipe pose recognition, machine learning target detection, and LLM/VLM multi-modal AI integration.

---

## 1. Getting Started & Assembly
*Ref: [Hiwonder Getting Ready Guide](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/getting_ready.html)*

Before powering on the robot:
1. **Unboxing Inspection:** Verify all hardware components including chassis motor drivers, LiDAR unit, depth camera, power distribution board, and lithium battery.
2. **Wiring:** Ensure power connections adhere strictly to correct polarity to avoid board damage. Connect LiDAR and depth camera to USB/serial ports.
3. **Power On Check:** Turn on the main switch and confirm status indicator LEDs and cooling fans operate properly.

---

## 2. Development Environment Setup
*Ref: [Hiwonder Environment Setup Guide](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/set_development_environment.html)*

1. **System Setup:** Operates on Ubuntu Linux with ROS (Robot Operating System) pre-installed.
2. **Workspace Creation:** Set up your ROS workspace on the robot's onboard controller:
   ```bash
   mkdir -p ~/mentorpi_ws/src
   cd ~/mentorpi_ws/
   catkin_make
   source devel/setup.bash
   ```
3. **Remote Management:** SSH and VNC viewer setups allow full remote desktop and terminal control over Wi-Fi without attaching physical peripherals to the robot.

---

## 3. Network Configuration
*Ref: [Robot Network Configuration Guide](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/14.Robot%20Network%20Configuration.html)*

Establishing reliable network communication between your PC workstation and MentorPi:
* Connect both the robot and development PC to the same Wi-Fi subnet.
* Update environment variables in `~/.bashrc`:
  ```bash
  export ROS_MASTER_URI=http://<MENTORPI_IP>:11311
  export ROS_IP=<YOUR_WORKSTATION_OR_ROBOT_IP>
  ```
* Test remote connectivity via `ping` and `rostopic list`.

---

## 4. Core Motion & Sensor Integration

### 🕹️ Motion Control
*Ref: [Motion Control Lesson](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/motion_control_lesson.html)*
Publish velocity vector messages (`geometry_msgs/Twist`) to drive the robot:
```bash
rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```

### 📡 2D LiDAR Integration
*Ref: [LiDAR Lesson](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/5.lidar_lesson.html)*
Launch laser scanners to read `sensor_msgs/LaserScan` data for distance sensing, collision warning, and RViz visualization.

### 📷 Depth Camera Sensing
*Ref: [Depth Camera Lesson](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/6.depth_camera_basic_lesson.html)*
Stream synchronized RGB color and depth point clouds (`sensor_msgs/Image`, `sensor_msgs/PointCloud2`) for spatial vision processing.

---

## 5. SLAM Mapping & Autonomous Navigation

### 🗺️ SLAM Mapping
*Ref: [Mapping Lesson](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/7.mapping_lesson.html)*
Build 2D occupancy grid maps using SLAM algorithms (Cartographer / Gmapping):
```bash
roslaunch mentorpi_slam slam.launch
```
Save your completed map using `map_saver`.

### 🧭 Autonomous Navigation
*Ref: [Navigation Lesson](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/8.navigation_lesson.html)*
Load built maps into the ROS Navigation Stack (AMCL localization + move_base path planning) to set autonomous destination points using RViz.

---

## 6. Computer Vision, AI & Autonomous Driving

* **ROS + OpenCV ([Lesson Link](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/9.ros+opencv_lesson.html)):** Perform image filtering, color tracking, and contour detection.
* **MediaPipe HRI ([Lesson Link](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/10.mediapipe_human_robot_interaction.html)):** Control robot movements using hand gesture recognition and skeletal body pose tracking.
* **Machine Learning ([Lesson Link](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/11.machine_learning.html)):** Run YOLO and TensorFlow models for object identification and sorting.
* **Autonomous Driving ([Lesson Link](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/12.autonomous_driving_lesson.html)):** Combine lane detection, stop sign identification, and traffic light recognition for simulated self-driving tasks.

---

## 7. Large AI Models & Multi-Robot Swarm Control

### 🧠 Large AI Models Integration
*Ref: [Large AI Model Course](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/15.Large_AI_Model_Course.html)*
Connect your robot to LLM and VLM APIs. Translate high-level natural language instructions (e.g., *"Find the red ball and move towards it"*) directly into ROS action goals and execution pipelines.

### 👥 Master-Slave & Multi-Robot Group Control
*Ref: [Master/Slave Control Guide](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/13.MasterSlaveAndGroupControl.html)*
Configure multi-robot networks across shared ROS Master nodes to perform synchronized swarm movements, leader-follower dynamics, and collaborative spatial mapping.

---

## 🔗 Documentation References

All modules in this repository correspond to official Hiwonder documentation:
* [Getting Ready](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/getting_ready.html)
* [Development Environment](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/set_development_environment.html)
* [Motion Control](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/motion_control_lesson.html)
* [LiDAR Sensor](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/5.lidar_lesson.html)
* [Depth Camera](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/6.depth_camera_basic_lesson.html)
* [Mapping (SLAM)](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/7.mapping_lesson.html)
* [Navigation](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/8.navigation_lesson.html)
* [ROS + OpenCV](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/9.ros+opencv_lesson.html)
* [MediaPipe HRI](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/10.mediapipe_human_robot_interaction.html)
* [Machine Learning](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/11.machine_learning.html)
* [Autonomous Driving](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/12.autonomous_driving_lesson.html)
* [Master-Slave & Group Control](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/13.MasterSlaveAndGroupControl.html)
* [Robot Network Configuration](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/14.Robot%20Network%20Configuration.html)
* [Large AI Model Course](https://wiki.hiwonder.com/projects/MentorPi/en/latest/docs/15.Large_AI_Model_Course.html)

---
