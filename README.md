# Where Am I — AMCL Robot Localisation

A Gazebo simulation demonstrating Adaptive Monte Carlo Localisation (AMCL). A custom differential-drive robot localises itself on a pre-built map using a LiDAR particle filter — the robot knows the map but not its starting position.

## What it does

The robot is placed in a Gazebo world with a pre-built occupancy grid map. AMCL maintains a particle cloud of pose hypotheses over the map; as the robot moves and takes LiDAR readings, the cloud converges on the true pose.

## Launch

```bash
catkin_make
source devel/setup.bash

# Start Gazebo + robot
roslaunch my_robot world.launch

# Start AMCL localisation
roslaunch my_robot amcl.launch

# Teleoperate to help particles converge
roslaunch my_robot teleop.launch
```

## Repository layout

```
my_robot/
  urdf/        Robot description (URDF)
  worlds/      Gazebo world files
  config/      AMCL and costmap parameters
  maps/        Pre-built occupancy grid maps
  launch/      Launch files
  meshes/      3D model meshes
```

## AMCL tuning notes

Key parameters in `config/`:
- `min_particles` / `max_particles` — particle count range (more = more accurate, slower)
- `laser_model_type: likelihood_field` — smooth, fast likelihood model (vs `beam` which is more accurate but slower)
- `update_min_d` / `update_min_a` — minimum motion before running an update step
- `recovery_alpha_slow` / `recovery_alpha_fast` — inject random particles when the filter loses confidence (kidnapped robot recovery)

## TF tree

```
map → odom → base_link → laser
```

- `map → odom`: published by AMCL (correction, can jump)
- `odom → base_link`: published by odometry (continuous)

## Tech stack

ROS 1 Noetic · Gazebo · AMCL · C++
