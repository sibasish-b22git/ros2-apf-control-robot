# ROS 2 Artificial Potential Field (APF) Robot Navigator

A reactive, LiDAR-based Artificial Potential Field (APF) navigation controller for differential drive robots in ROS 2 and Gazebo. 

This package allows a robot to autonomously navigate to dynamic `[x, y]` coordinates while smoothly steering around obstacles in real-time. It processes standard LaserScan data to generate repulsive force vectors and Odometry data to generate attractive force vectors, combining them into a normalized kinematic velocity command.

## 🛠️ Prerequisites

* **OS:** Ubuntu 24.04 (Recommended)
* **ROS 2:** Jazzy Jalisco (or Humble Hawksbill)
* **Simulation:** Gazebo Sim (Harmonic/Fortress)
* **Python Packages:** `numpy`, `matplotlib`, `tf_transformations` and related

## 1. What This Project Does
This project implements an **Artificial Potential Field (APF)** algorithm for a differential drive robot in ROS 2. Instead of pre-planning a path, the robot reacts to its environment live at 100Hz.
It treats the world as a landscape of magnetic forces:
* **The Goal (Attractive Force):** The algorithm generates an attractive vector directed toward the target [x, y] coordinates. The magnitude of this force is directly proportional to the Euclidean distance between the robot and the goal, providing the primary navigational drive.
* **The Obstacles (Repulsive Force):** To ensure collision avoidance, a repulsive potential function is generated based on real-time LiDAR point clouds. For any obstacle localized within a defined threshold (repulsion_radius), the system computes a repulsive force inversely proportional to the squared distance, creating a localized high-potential barrier around the obstacle.
* **The Resultant Force:** The resultant navigational vector is derived from the superposition of the attractive and repulsive gradients. The controller maps this net vector to the robot's differential drive kinematics, continuously resolving the optimal linear and angular velocities required for safe, collision-free convergence.

---

## 2. How to Run It

**Step 1: Clone the Repository into your system**
   Clone this repository into the `src` folder of your ROS 2 workspace:
   ```bash
   cd ~/your_ros2_workspace
   git clone https://github.com/sibasish-b22git/ros2-apf-control-robot/tree/main
```

**Step 2: Launch the Robot and Simulation**
This boots up Gazebo, spawns the robot based on its URDF, and starts broadcasting the TF tree and sensor data.
```bash
ros2 launch diff_drive_description diff_drive_gazebo.launch.py robot_type:=with_control
```

**Step 3: Run the APF Brain**
Open a new terminal and start the controller. It will immediately begin listening to Odometry and LiDAR.
```bash
ros2 launch diff_drive_control apf.launch.py
```

**Step 4: Issue a Dynamic Goal**
Open a third terminal. Use the ROS 2 parameter system to magically drop a new goal into the world without restarting the node:
```bash
ros2 param set /apf_controller goal "[-5.0, 0.0]"
```

---

## 3. How to Visualize the Robot in Gazebo

To see exactly how close your robot is to hitting walls, a 3D perspective can trick your eyes. You need a perfect bird's-eye view.

1.  In Gazebo, look at the top toolbar for the **View Angle** (the 3D cube icon). Click the **Top** face to look 90 degrees straight down.
2.  Next to it, click the camera icon and change the projection from **Perspective** to **Orthographic**.
3.  This flattens the world into a 2D map, completely removing depth distortion.

---
