---
description: >-
  This article shows how to use the Electro Permanent Magnet Gripper (OpenGrab
  EPM v3) with ArduPilot.
---

# Electro Permanent Magnet Gripper

### Overview

The _OpenGrab EPM v3_ (from NicaDrone.com) is a small (less than 4cm x 4cm x 2cm) Electro Permanent Magnet gripper capable of 200N of force meaning it can hold about 15kg of cargo securely. The gripping and releasing of the cargo is accomplished by delivering a short pulse to an electromagnet which reverses it’s field. Objects that it grips onto should be metal or have the included [thin steel target square](http://nicadrone.com/index.php?id_product=15\&controller=product) attached to them.

The device accepts a standard PWM servo input or DroneCAN and uses a 5V power supply. A significant amount of power is only required for about 1 second when it first grips or releases the cargo.

### Useful links

* [Documentation](https://wiki.zubax.com/en/public/fluxgrip/opengrab-epm-v3)
* [PCB / Schematic](https://upverter.com/ctech4285/b9557d6903c36f55/OpenGrab-EPM-V3R4B/) (Revision is printed on PCB).
* [Source code](https://github.com/Zubax/opengrab_epm_v3)
* [Store](http://nicadrone.com/)
* [Report problems/issues](mailto:Andreas%40NicaDrone.com?Subject=Problems/issues)

### Connecting to a Pixhawk via DroneCAN

<figure><img src="https://ardupilot.org/plane/_images/OpenGrabEPMV3_2.jpg" alt=""><figcaption></figcaption></figure>

Notes:

* When Vin of the EPM drops below 5V the EPM will go into low power mode. Slowing down switching to prevent crashing the power rail.
* When Vin drops below 4.5V the green status LED will blink fast indicating an error condition.
* The Pixhawk DroneCAN connector power rail voltage is < 5V.
* External powering of the EPM is recommended (see image above).
* EPM DroneCAN cable set is required. [Clickmate -> DF13](http://nicadrone.com/index.php?id_product=69\&controller=product).
* DroneCAN requires termination, terminator included in DroneCAN cable set.

### Parameter setup

1. You need to enable the DroneCAN. The [DroneCAN setup page is here](https://ardupilot.org/plane/docs/common-uavcan-setup-advanced.html#common-uavcan-setup-advanced). You also may check the configurations of the parameters [GRIP\_CAN\_ID](https://ardupilot.org/plane/docs/parameters.html#grip-can-id) and [GRIP\_TYPE](https://ardupilot.org/plane/docs/parameters.html#grip-type) to set gripper ID and type.
2. Save the configurations and restart the boards. After about 5 seconds the RED CAN LED on the EPM will start blinking. This indicates that CAN frames are being received.
3.  Set CH7\_OPT: Channel 7 option = 19 (EPM) (Channel 7 - 12 maybe used)



    <figure><img src="https://ardupilot.org/plane/_images/OpenGrabEPMV3_4.jpg" alt=""><figcaption></figcaption></figure>

All done! The EPM can be controlled via channel 7 or AUTO mission or MAVLink command.

### Connecting to a Pixhawk via PWM header

Connect the EPM to the Pixhawk the same as a [servo](https://ardupilot.org/plane/docs/common-servo.html#common-servo).

<figure><img src="https://ardupilot.org/plane/_images/OpenGrabEPMV3_5.jpg" alt=""><figcaption></figcaption></figure>

### Parameter setup

Set CH7\_OPT: Channel 7 option = 19 (EPM) (Channel 7 - 12 maybe used).

<figure><img src="https://ardupilot.org/plane/_images/OpenGrabEPMV3_6.jpg" alt=""><figcaption></figcaption></figure>

All done. The EPM can be controlled via channel 7 or AUTO Mission or MAVLink command.

### Auto mission

Add a _Do Gripper_ command in your mission.

<figure><img src="https://ardupilot.org/plane/_images/OpenGrabEPMV3_7.jpg" alt=""><figcaption></figcaption></figure>

### Magnetic interference and board voltage check

The EPM can cause magnetic interference especially while gripping or releasing the cargo or if left in the “on” state with no cargo attached. Magnetic interference is minimal at a distance of 10 cm

Testing the EPM impact on the compass is highly recommend when mounted close then 10 cm distance from flight computer

The EPM’s impact on the compass can be tested in real-time by connecting with the Mission Planner. Go to the Flight Data screen and click on the “Tuning” checkbox at the bottom, middle. Double click on the graph that appears on the top right and when the “Graph This” window appears select “magfield”.

Turning the EPM on and off and ensure the magfield length does not change by more than 10%.

<figure><img src="https://ardupilot.org/plane/_images/mag_field.jpg" alt=""><figcaption></figcaption></figure>
