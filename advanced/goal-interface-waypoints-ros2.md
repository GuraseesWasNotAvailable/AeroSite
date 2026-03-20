---
description: >-
  This page shows how to set up ROS 2 DDS with ArduPilot SITL for global
  position control.
---

# Goal Interface - Waypoints ROS2

### Usage

This page assumes that you’ve successfully followed the ROS 2 basic setups from this wiki: [ROS 2](https://ardupilot.org/dev/docs/ros2.html#ros2), [ROS 2 with SITL](https://ardupilot.org/dev/docs/ros2-sitl.html#ros2-sitl), and [ROS 2 with gazebo](https://ardupilot.org/dev/docs/ros2-gazebo.html#ros2-gazebo).

Once that’s done, start the MicroROS Agent.

{% tabs %}
{% tab title="ArduPilot 4.5" %}


```
cd ~/ardu_ws/src
source ~/ardu_ws/install/setup.bash
cd ardupilot/libraries/AP_DDS
ros2 run micro_ros_agent micro_ros_agent udp4 -p 2019 -r dds_xrce_profile.xml
```
{% endtab %}

{% tab title="ArduPilot 4.6 and later" %}


```
cd ~/ardu_ws/src
source ~/ardu_ws/install/setup.bash
cd ardupilot/libraries/AP_DDS
ros2 run micro_ros_agent micro_ros_agent udp4 -p 2019
```
{% endtab %}
{% endtabs %}

And, in another terminal, run SITL.

```
cd ~/ardu_ws/src/ardupilot
source ~/ardu_ws/install/setup.bash
./Tools/autotest/sim_vehicle.py -w -v ArduPlane --console  --enable-dds --map -DG
```

Now that SITL is running, you have two options: run the ROS2 Node or use CLI commands.

### Using CLI Commands

1.  **Arming the Plane**:

    * Run the following command to arm the motors:

    ```
    ros2 service call /ap/arm_motors ardupilot_msgs/srv/ArmMotors "{arm: true}"
    ```
2.  **Switching to Takeoff Mode**:

    * Change the flight mode to takeoff:

    ```
    ros2 service call /ap/mode_switch ardupilot_msgs/srv/ModeSwitch "{mode: 13}"
    ```
3.  **Reaching Takeoff Altitude**:

    * Wait until the plane reaches the takeoff altitude. You can monitor the altitude using:

    ```
    ros2 topic echo /ap/geopose/filtered
    ```

    * Ensure the plane’s altitude reaches the desired level (e.g., 630 meters AGL).
4.  **Switching to Guided Mode**:

    * Once the takeoff altitude is reached, switch to guided mode:

    ```
    ros2 service call /ap/mode_switch ardupilot_msgs/srv/ModeSwitch "{mode: 15}"
    ```
5.  **Sending Waypoint Command**:

    * Publish the goal position to guide the plane to the desired waypoint:

    ```
    ros2 topic pub /ap/cmd_gps_pose ardupilot_msgs/msg/GlobalPosition "{latitude: -35.345996, longitude: 149.159017, altitude: 635, coordinate_frame: 5}"
    ```

### Using ROS2 Node

```
cd ~/ardu_ws
source ./install/setup.bash
ros2 run ardupilot_dds_tests plane_waypoint_follower
```

### Understanding the ROS 2 Node

Here’s a detailed explanation of how this [ROS2 Node works](https://github.com/ArduPilot/ardupilot/blob/master/Tools/ros2/ardupilot_dds_tests/ardupilot_dds_tests/plane_waypoint_follower.py):

1. **Initialization**:
   * The node is initialized with _PlaneWaypointFollower(Node)_, which sets up the necessary parameters and clients for communication with ArduPilot services.
2. **Service Clients**:
   * _self.\_client\_arm_ and _self.\_client\_mode\_switch_ are created to handle arming the motors and switching flight modes, respectively.
   * These clients wait for the respective services to become available before proceeding.
3. **Publishers and Subscribers**:
   * A publisher _self.\_global\_pos\_pub_ is created to send global position commands.
   * A subscriber _self.\_subscription\_geopose_ listens for GeoPose messages to update the current position of the plane.
4. **Callback Function**:
   * _geopose\_cb(self, msg: GeoPoseStamped)_ processes incoming GeoPose messages and updates the current position.
5. **Arming and Mode Switching Functions**:
   * _arm(self)_ sends a request to arm the motors.
   * _switch\_mode(self, mode)_ sends a request to change the flight mode to either takeoff or guided.
   * _arm\_with\_timeout(self, timeout: rclpy.duration.Duration)_ and _switch\_mode\_with\_timeout(self, desired\_mode: int, timeout: rclpy.duration.Duration)_ try to arm and switch modes with a timeout.
6. **Mission Execution**:
   * The node attempts to arm the plane and switch to takeoff mode.
   * Once the plane reaches the takeoff altitude, it switches to guided mode.
   * The node sends a goal position for the plane to reach using guided control.
   * The _achieved\_goal(goal\_global\_pos, cur\_geopose)_ function checks if the plane has reached the goal.

### Demo

Here’s a quick video showcasing the results expected from this tutorial:

{% embed url="https://youtu.be/SHHw190RaHc" %}

{% embed url="https://youtu.be/FnChCmwBbHA" %}

For more information, refer to this [PR](https://github.com/ArduPilot/ardupilot/pull/25722).
