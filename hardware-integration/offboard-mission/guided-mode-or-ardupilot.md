---
description: Description about Guided Mode of Ardupilot
---

# Guided Mode | Ardupilot

In this mode the position, velocity or attitude reference / target / setpoint is provided by a companion computer connected via serial cable and MAVLink. The offboard setpoint can be provided by API like [MAVROS](https://github.com/mavlink/mavros).

Normal mainstream Ardupilot Firmware uses this message flow and Runs a high-level PID Controller:

#### Message Flows <a href="#message-flows" id="message-flows"></a>

<table><thead><tr><th width="206">ROS msg name</th><th>msg type</th><th>uses</th></tr></thead><tbody><tr><td><p></p><p>mavros/local_position/odom</p></td><td>nav_msgs::Odometry</td><td><p></p><p>Contains local odometry data</p></td></tr><tr><td>mavros/setpoint_position/local</td><td><p></p><p>geometry_msgs::PoseStamped</p></td><td>Used in offboard mode for postion setpoint</td></tr></tbody></table>

#### Example <a href="#example" id="example"></a>

<figure><img src="https://wiki.ariitk.in/~gitbook/image?url=https%3A%2F%2F2216434675-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-legacy-files%2Fo%2Fassets%252F-LVITCb6SLM7LAIvJagI%252F-LeDglHP-nCJeF85wcPi%252F-LeDmno6XmxsdZnKaIOT%252FUntitled%2520presentation.png%3Falt%3Dmedia%26token%3D9af9dff0-db8c-4edb-a0a6-67790a4927c7&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=aaea97b9&#x26;sv=2" alt=""><figcaption></figcaption></figure>

The instructions for assembling the “essential components” of Copter on Pixhawk and other autopilots. The instructions for adding other hardware are covered in [Optional Hardware](https://ardupilot.org/copter/docs/common-optional-hardware.html#common-optional-hardware).
