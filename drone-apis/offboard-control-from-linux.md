# Offboard Control from Linux

WARNING

[Offboard control](https://docs.px4.io/main/en/flight_modes/offboard.html) is dangerous. It is the responsibility of the developer to ensure adequate preparation, testing and safety precautions are taken before offboard flights.

The idea behind off-board control is to be able to control the PX4 flight stack using software running outside of the autopilot. This is done through the MAVLink protocol, specifically the [SET\_POSITION\_TARGET\_LOCAL\_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED) and the [SET\_ATTITUDE\_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET) messages.

### Offboard Control Firmware Setup[​](https://docs.px4.io/main/en/ros/offboard_control.html#offboard-control-firmware-setup) <a href="#offboard-control-firmware-setup" id="offboard-control-firmware-setup"></a>

There are two things you want to setup on the firmware side before starting offboard development.

#### Enable RC Override[​](https://docs.px4.io/main/en/ros/offboard_control.html#enable-rc-override) <a href="#enable-rc-override" id="enable-rc-override"></a>

In _QGroundControl_ you can set the [COM\_RC\_OVERRIDE](https://docs.px4.io/main/en/advanced_config/parameter_reference.html#COM_RC_OVERRIDE) parameter to automatically switch from offboard mode (or any mode) to Position mode if the RC sticks are moved. This is the best way to ensure that an operator can easily take control of the vehicle and switch to the safest flight mode.

#### Map an RC switch to offboard mode activation[​](https://docs.px4.io/main/en/ros/offboard_control.html#map-an-rc-switch-to-offboard-mode-activation) <a href="#map-an-rc-switch-to-offboard-mode-activation" id="map-an-rc-switch-to-offboard-mode-activation"></a>

In _QGroundControl_ you can set the [RC\_MAP\_OFFB\_SW](https://docs.px4.io/main/en/advanced_config/parameter_reference.html#RC_MAP_OFFB_SW) parameter to the RC channel that will be used to activate offboard mode. This can be used to switch between offboard mode and the mode set by the mode switch ([RC\_MAP\_MODE\_SW](https://docs.px4.io/main/en/advanced_config/parameter_reference.html#RC_MAP_MODE_SW)). You can also switch into offboard mode using a GCS/MAVLink so this is not "mandatory".

Note also that this mechanism is not as "safe" as using [RC Override](https://docs.px4.io/main/en/ros/offboard_control.html#enable-rc-override) to switch out of offboard mode, because the mode you switch to is unpredictable.

#### Enable the companion computer interface[​](https://docs.px4.io/main/en/ros/offboard_control.html#enable-the-companion-computer-interface) <a href="#enable-the-companion-computer-interface" id="enable-the-companion-computer-interface"></a>

Enable MAVLink on the serial port that you connect to the companion computer (see [Companion Computers](https://docs.px4.io/main/en/companion_computer/)).

### Hardware setup[​](https://docs.px4.io/main/en/ros/offboard_control.html#hardware-setup) <a href="#hardware-setup" id="hardware-setup"></a>

Usually, there are three ways of setting up offboard communication.

#### Serial radios[​](https://docs.px4.io/main/en/ros/offboard_control.html#serial-radios) <a href="#serial-radios" id="serial-radios"></a>

1. One connected to a UART port of the autopilot
2.  One connected to a ground station computer

    Example radios include:

    * [Lairdtech RM024](http://www.lairdtech.com/products/rm024)
    * [Digi International XBee Pro](http://www.digi.com/products/xbee-rf-solutions/modules)

[![Mermaid graph: mavlink channel](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGduZFtHcm91bmQgU3RhdGlvbl0gLS1NQVZMaW5rLS0-IHJhZDFbR3JvdW5kIFJhZGlvXTtcbiAgcmFkMSAtLVJhZGlvUHJvdG9jb2wtLT4gcmFkMltWZWhpY2xlIFJhZGlvXTtcbiAgcmFkMiAtLU1BVkxpbmstLT4gYVtBdXRvcGlsb3RdOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGduZFtHcm91bmQgU3RhdGlvbl0gLS1NQVZMaW5rLS0-IHJhZDFbR3JvdW5kIFJhZGlvXTtcbiAgcmFkMSAtLVJhZGlvUHJvdG9jb2wtLT4gcmFkMltWZWhpY2xlIFJhZGlvXTtcbiAgcmFkMiAtLU1BVkxpbmstLT4gYVtBdXRvcGlsb3RdOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)

#### On-board processor[​](https://docs.px4.io/main/en/ros/offboard_control.html#on-board-processor) <a href="#on-board-processor" id="on-board-processor"></a>

A small computer mounted onto the vehicle, connected to the autopilot through a serial port or Ehthernet port. There are many possibilities here and it will depend on what kind of additional on-board processing you want to do in addition to sending commands to the autopilot. Some examples are provided in [Companion Computers](https://docs.px4.io/main/en/companion_computer/#companion-computer-options).

[![Mermaid diagram: Companion mavlink](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGNvbXBbQ29tcGFuaW9uIENvbXB1dGVyXSAtLU1BVkxpbmstLT4gdWFydFtVQVJUIEFkYXB0ZXJdO1xuICB1YXJ0IC0tTUFWTGluay0tPiBBdXRvcGlsb3Q7IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGNvbXBbQ29tcGFuaW9uIENvbXB1dGVyXSAtLU1BVkxpbmstLT4gdWFydFtVQVJUIEFkYXB0ZXJdO1xuICB1YXJ0IC0tTUFWTGluay0tPiBBdXRvcGlsb3Q7IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

#### On-board processor and wifi link to ROS (_**Recommended**_)[​](https://docs.px4.io/main/en/ros/offboard_control.html#on-board-processor-and-wifi-link-to-ros-recommended) <a href="#on-board-processor-and-wifi-link-to-ros-recommended" id="on-board-processor-and-wifi-link-to-ros-recommended"></a>

A small computer mounted onto the vehicle connected to the autopilot through a UART to USB adapter while also having a WiFi link to a ground station running ROS. This can be any of the computers from the above section coupled with a WiFi adapter.

[![Mermaid graph: ROS](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVERcbiAgc3ViZ3JhcGggR3JvdW5kICBTdGF0aW9uXG4gIGduZFtST1MgRW5hYmxlZCBDb21wdXRlcl0gLS0tIHFnY1txR3JvdW5kQ29udHJvbF1cbiAgZW5kXG4gIGduZCAtLU1BVkxpbmsvVURQLS0-IHdbV2lGaV07XG4gIHFnYyAtLU1BVkxpbmstLT4gdztcbiAgc3ViZ3JhcGggVmVoaWNsZVxuICBjb21wW0NvbXBhbmlvbiBDb21wdXRlcl0gLS1NQVZMaW5rLS0-IHVhcnRbVUFSVCBBZGFwdGVyXVxuICB1YXJ0IC0tLSBBdXRvcGlsb3RcbiAgZW5kXG4gIHcgLS0tIGNvbXAiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVERcbiAgc3ViZ3JhcGggR3JvdW5kICBTdGF0aW9uXG4gIGduZFtST1MgRW5hYmxlZCBDb21wdXRlcl0gLS0tIHFnY1txR3JvdW5kQ29udHJvbF1cbiAgZW5kXG4gIGduZCAtLU1BVkxpbmsvVURQLS0-IHdbV2lGaV07XG4gIHFnYyAtLU1BVkxpbmstLT4gdztcbiAgc3ViZ3JhcGggVmVoaWNsZVxuICBjb21wW0NvbXBhbmlvbiBDb21wdXRlcl0gLS1NQVZMaW5rLS0-IHVhcnRbVUFSVCBBZGFwdGVyXVxuICB1YXJ0IC0tLSBBdXRvcGlsb3RcbiAgZW5kXG4gIHcgLS0tIGNvbXAiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)
