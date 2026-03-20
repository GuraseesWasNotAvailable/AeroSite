---
description: >-
  This article explains how a Pozyx system based on the DWM1000 can be used as a
  short-range substitute for a GPS allowing position control modes like Loiter,
  PosHold, RTL, Auto indoors.
---

# Ultra-wideband (UWB) transceiver for Non-GPS Navigation

### Required Hardware

* 4 [Pozyx anchors](https://www.pozyx.io/store/detail/5) and 1 [Pozyx tag](https://www.pozyx.io/store/detail/4). Note the [Ready to Localize](https://www.pozyx.io/store/detail/2) or [Developer Kits](https://www.pozyx.io/store/detail/3) are more economical than buying individual tags and anchors.
* one [Arduino Uno R2 or R3](https://www.arduino.cc/en/Main/ArduinoBoardUno)
* 4 [portable chargers](https://www.amazon.com/s/ref=nb_sb_noss_2?url=search-alias%3Daps\&field-keywords=portable+charger\&rh=i%3Aaps%2Ck%3Aportable+charger) with type A USB ports to power the anchors (optional)
* 4 [tripods](https://www.amazon.com/s/ref=nb_sb_noss_1?url=search-alias%3Daps\&field-keywords=tripod\&rh=i%3Aaps%2Ck%3Atripod) to mount the anchors (optional)

### Placing the anchors

The anchors should be placed in a rectangular shape. The anchor with the lowest ID (IDs are printed in hexidecimal on each device) will act as the “origin” and should be placed at the lower-left corner of the rectangle. Each of the remaining three anchors should be placed in a corner so that the anchor IDs increase as you travel an “N” pattern within the rectangle. The configuration is slightly simpler if the line from the origin anchor to the 2nd anchor is due North but it is not required as the [BCN\_ORIENT\_YAW](https://ardupilot.org/rover/docs/parameters.html#bcn-orient-yaw) parameter can be used to account this difference.

<figure><img src="https://ardupilot.org/rover/_images/pozyx-anchor-layout.png" alt=""><figcaption></figcaption></figure>

### Preparing the tag

<figure><img src="https://ardupilot.org/rover/_images/pozy-pixhawk-uno.png" alt=""><figcaption></figcaption></figure>

* connect one of the autopilot’s telemetry connections to the UNO. Telem1 is shown above but any SERIAL port will work equally well.
* autopilot’s UART port GND should be connected to one of the UNO’s GND pins.
* autopilot’s UART port TX should be connected to the UNO’s pin 10.
* autopilot’s UART port RX should be connected to the UNO’s pin 11.
* autopilot’s UART port VCC may be connected to the UNO’s 5V connector to provide power to the Uno/Pozyx from the autopilot.

The Arduino IDE should be used to load [IndoorLoiter sketch](https://github.com/ArduPilot/ardupilot/blob/master/Tools/Pozyx/IndoorLoiter/IndoorLoiter.ino) onto the Uno after first changing the anchor tags [found here](https://github.com/ArduPilot/ardupilot/blob/master/Tools/Pozyx/IndoorLoiter/IndoorLoiter.ino#L12) to match the IDs of the tags in your setup.

At this point you may wish to test the tag’s ability to communicate with the tags by:

* powering on all the anchors.
* connect the UNO’s Type B USB to your PC.
* connect with the Arduino IDE’s serial monitor (set to baud 115200) and check no “Beacon Configuration failed” messages appear. If they do, check that the IDs have been set correctly in the IndoorLoiter2.ino sketch (above).

Mount the Pozyx tag on the top of the Arduino Uno and then mount on the vehicle.

### Configuration through the Ground Station

Connect with a Ground Station and set the following parameters:

* set [BCN\_TYPE](https://ardupilot.org/rover/docs/parameters.html#bcn-type) to 1 (means using Pozyx system)
* set [BCN\_LATITUDE](https://ardupilot.org/rover/docs/parameters.html#bcn-latitude), [BCN\_LONGITUDE](https://ardupilot.org/rover/docs/parameters.html#bcn-longitude) and [BCN\_ALT](https://ardupilot.org/rover/docs/parameters.html#bcn-alt) to match your actual location. Getting these values exactly correct is not particularly important although getting it close is required in order for the compass’s declination to be correctly looked up from the small database held within ArduPilot.
* set [BCN\_ORIENT\_YAW](https://ardupilot.org/rover/docs/parameters.html#bcn-orient-yaw) to the heading from the origin anchor to the 2nd anchor. One way to capture this value is to stand at the origin holding the vehicle so that its nose points towards the second beacon. Read the vehicle’s heading from the HUD and enter this value into [BCN\_ORIENT\_YAW](https://ardupilot.org/rover/docs/parameters.html#bcn-orient-yaw)
* set [EK3\_SRC1\_POSXY](https://ardupilot.org/rover/docs/parameters.html#ek3-src1-posxy) to 4 (Beacon)
* set [EK3\_SRC1\_VELXY](https://ardupilot.org/rover/docs/parameters.html#ek3-src1-velxy) to 0 (None)
* set [EK3\_SRC1\_VELZ](https://ardupilot.org/rover/docs/parameters.html#ek3-src1-velz) to 0 (None)
* set [EK3\_SRC1\_POSZ](https://ardupilot.org/rover/docs/parameters.html#ek3-src1-posz) to 4 (Beacon)
* set [EK3\_SRC1\_YAW](https://ardupilot.org/rover/docs/parameters.html#ek3-src1-yaw) to 1 (Compass) since a compass is required for use with this device.
* set [ARMING\_CHECK](https://ardupilot.org/rover/docs/parameters.html#arming-check) to -9 to disable the GPS arming check
* set [SERIAL1\_BAUD](https://ardupilot.org/rover/docs/parameters.html#serial1-baud) to 115 to set SERIAL1 port’s baud rate to 115200 (if using a different SERIAL port set its baud rate to 115, instead)
* set [SERIAL1\_PROTOCOL](https://ardupilot.org/rover/docs/parameters.html#serial1-protocol) to 13 (Beacon) to enable reading the IndoorLoiter2 protocol (If using a different port, set its protocol to 13 instead)
* set [BRD\_SER1\_RTSCTS](https://ardupilot.org/rover/docs/parameters.html#brd-ser1-rtscts) to 0 to ensure telem1 does not use flow control (If using Telem2 set [BRD\_SER2\_RTSCTS](https://ardupilot.org/rover/docs/parameters.html#brd-ser2-rtscts) instead, not required if using a SERIAL port without flow control)

### Ground Testing

* Connect the Autopilot to a ground station. You may need to connect the Lipo battery as well because some computers are unable to provide enough power through their USB port for the combined autopilot+Pozyx+Uno.
* Press the UNO’s white or red reset button which can be found next to the USB port (see image above)
* After about 1 minute the vehicle’s position should jump to the lattitude, longitude you input during the configuration step (above). If it does not, connect a USB cable to the UNO’s USB port and open the Arduino IDE’s serial monitor and look for errors.
* Check that the vehicle’s position is relatively stable (i.e. moving around less than one meter)
* Walk the vehicle around between the anchors and ensure that its position on the map updates correctly

### Flight testing

Setup the transmitter with Stabilize, AltHold and Loiter flight modes. Take off in AltHold mode and maintain a stable hover. Switch to Loiter but be ready to switch back to AltHold or Stabilize if the vehicle’s position or altitude becomes unstable.

### DataFlash logging

The distance to the beacons can be found in the dataflash log’s BCN message’s D0, D1, D2, D3 fields.

<br>
