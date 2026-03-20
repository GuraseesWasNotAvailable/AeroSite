# Grippers | Ardupilot

Copter supports a number of different grippers which can be useful for delivery applications and bottle drops.

Follow the links below (or in sidebar) for configuration information based upon your set-up.

![../\_images/gripper\_landing\_page\_image.png](https://ardupilot.org/plane/_images/gripper_landing_page_image.png)

* [Electro Permanent Magnet v3 (EPMv3)](https://ardupilot.org/plane/docs/common-electro-permanent-magnet-V3.html)
* [Electro Permanent Magnet v1 (EPM688)](https://ardupilot.org/plane/docs/common-electro-permanent-magnet-gripper.html)
* [Servo Gripper](https://ardupilot.org/plane/docs/common-gripper-servo.html)

<mark style="background-color:green;">Tip</mark>

<mark style="background-color:green;">Grippers can be configured to release in a failsafe situation using</mark> [<mark style="background-color:green;">FS\_OPTIONS</mark>](https://ardupilot.org/rover/docs/parameters.html#fs-options) <mark style="background-color:green;">(ArduCopter only)</mark>

### Common Gripper Parameters

* [GRIP\_ENABLE](https://ardupilot.org/plane/docs/parameters.html#grip-enable): Enables use of gripper and shows following parameters.
* [GRIP\_TYPE](https://ardupilot.org/plane/docs/parameters.html#grip-type): Type of gripper - 0:None(disables without hiddening parameters), 1:Servo, 2:EMP
* [GRIP\_AUTOCLOSE](https://ardupilot.org/plane/docs/parameters.html#grip-autoclose): Time in secs (0 disables) to re-grip payload after releasing. Used for payload replacement after delivery.
* [GRIP\_GRAB](https://ardupilot.org/plane/docs/parameters.html#grip-grab): PWM in uS to grab payload
* [GRIP\_NEUTRAL](https://ardupilot.org/plane/docs/parameters.html#grip-neutral): PWM in uS to neither grab nor release payload
* [GRIP\_RELEASE](https://ardupilot.org/plane/docs/parameters.html#grip-release): PWM in uS to release payload
* [GRIP\_REGRAB](https://ardupilot.org/plane/docs/parameters.html#grip-regrab): (EMP only) Time interval in seconds (0 disables) to regrab payload to assure EM hold has not decayed.
* [GRIP\_CAN\_ID](https://ardupilot.org/plane/docs/parameters.html#grip-can-id): (EMP only) Refer to [https://docs.zubax.com/opengrab\_epm\_v3#UAVCAN\_interface](https://docs.zubax.com/opengrab_epm_v3#UAVCAN_interface)

The autopilot output channel which is attached to the gripper is selected by setting its `SERVOx_FUNCTION` = “28”(Gripper).

The gripper is controlled by using the MAVLink Command [MAV\_CMD\_DO\_GRIPPER](https://ardupilot.org/plane/docs/common-mavlink-mission-command-messages-mav_cmd.html#mav-cmd-do-gripper) or, configuring and RC channel switch to `RCx_OPTION` = “19(Gripper).
