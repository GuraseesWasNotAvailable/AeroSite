# Using an Airspeed Sensor | Ardupilot

## Using an Airspeed Sensor

Plane supports the use of an airspeed sensor, which can help in windy conditions, slow flight and autonomous landings. It is not recommended for most new users, however, as it does require additional tuning and adds one more layer of control to set up.

The following sections explain how to wire sensors to the autopilot and set them up. After you install an airspeed sensor don’t forget to [calibrate it](https://ardupilot.org/plane/docs/calibrating-an-airspeed-sensor.html#calibrating-an-airspeed-sensor)!

[![../\_images/BR-0004-03-2T1.jpg](https://ardupilot.org/plane/_images/BR-0004-03-2T1.jpg)](https://ardupilot.org/plane/_images/BR-0004-03-2T1.jpg)

### ARSPD\_USE

[ARSPD\_USE](https://ardupilot.org/plane/docs/parameters.html#arspd-use) enables airspeed use for automatic throttle modes instead of [TRIM\_THROTTLE](https://ardupilot.org/plane/docs/parameters.html#trim-throttle) as the target throttle setting (altered by [TECS (Total Energy Control System) for Speed and Height Tuning Guide](https://ardupilot.org/plane/docs/tecs-total-energy-control-system-for-speed-height-tuning-guide.html#tecs-total-energy-control-system-for-speed-height-tuning-guide) as needed during altitude control) . The autopilot continues to display and log airspeed if set to 0, but only airspeed sensor readings are used for control if set to 1. It will only use airspeed sensor readings for control when throttle is idle, if set to 2 (useful for gliders with airspeed sensors behind propellers).

If an airspeed sensor is used, the throttle stick will set the target airspeed in CRUISE and FBWB, while maintaining altitude target. In AUTO and GUIDED, it will use the [AIRSPEED\_CRUISE](https://ardupilot.org/plane/docs/parameters.html#airspeed-cruise) value unless [THROTTLE\_NUDGE](https://ardupilot.org/plane/docs/parameters.html#throttle-nudge) is enabled and throttle stick is used to alter it, or a MAVLink command to change speed is sent to the vehicle.

### Airspeed Sensor Type

Airspeed sensors can be either analog or digital. The analog sensors connect to an A/D converter input pin on the autopilot, while digital sensors connect to the autopilot’s external I2C bus using the SDA and SCL external digital I/O pins or via DroneCAN. The type is set by the [ARSPD\_TYPE](https://ardupilot.org/plane/docs/parameters.html#arspd-type) parameter. Analog sensors are type 2, DroneCAN sensors as type 8, and supported I2C bus digital sensors by other numbers.

If there is no sensor, be sure to set the [ARSPD\_TYPE](https://ardupilot.org/plane/docs/parameters.html#arspd-type) to 0. ArduPilot calculates an sensor-less airspeed estimate that is used if no sensor is present or fails. [ARSPD\_TYPE](https://ardupilot.org/plane/docs/parameters.html#arspd-type) must be set to zero in order to display this value if no sensor is present.

Warning

Many airspeed sensors are sensitive to light. Unless you are certain that the particular sensor used is not light sensitive, in order to avoid measurement errors, the sensor should be shielded from light.

### Autopilot Airspeed Connection

A list of digital and DroneCAN airspeed sensors are listed [below](https://ardupilot.org/plane/docs/airspeed.html#arspd-sensor-list).

#### I2C

Connect the airspeed sensor to autopilots’s I2C port (or I2C splitter module). The [ARSPD\_BUS](https://ardupilot.org/plane/docs/parameters.html#arspd-bus) parameter must be set for the bus used to connect the sensor. Normally this defaults to “1” , and corresponds to the I2C bus normally designated for connecting the sensor. But if it is attached to another I2C bus (eg. compass, or on some autopilots its been mistakenly assigned) it will need to be changed to “0”. If the sensor fails to initialize (a GCS message will be sent if this is the case), then try changing the bus number and rebooting.

[![../\_images/airspeed\_full\_assembly\_800px.jpg](https://ardupilot.org/plane/_images/airspeed_full_assembly_800px.jpg)](https://ardupilot.org/plane/_images/airspeed_full_assembly_800px.jpg)

To enable the digital airspeed sensor, connect the autopilot to Mission Planner (or APM Planner for OS X), and select the Optional Hardware/Airspeed tab under the CONFIG menu. Using the drop-down box for Type, select your sensor’s type. The Pin dropdown is not used and can be ignored. Check the “Use Airpeed” box to use it in control, or leave it unchecked during in-flight calibration discussed below to check its operation before use.

#### DroneCAN

Attach the sensor to the AutoPilot’s DroneCAN port and select DroneCAN in the above mentioned Type dropdown box and check the “Use Airspeed” box as appropriate.

#### Analog Airspeed sensor

Analog sensors are now largely discontinued. Digital sensors are much more accurate and consistent over temperature. ArduPilot still supports these analog sensors, but they are not preferred. See [Analog Airspeed Sensors](https://ardupilot.org/plane/docs/analog-airspeed-sensors.html#analog-airspeed-sensors).

### Installing the Pitot Tubes

When you place the airspeed sensor in your aircraft, use the pitot tube set in the kit (the kit comes with a single tube to measure both static and total pressure). In the case of the _EasyStar_, you’ll need to push it through the foam in the cockpit so it points straight into the airstream (drill or cut a small hole in the foam first). Make sure the holes in the side of the tube are not covered. They should be at least 1 centimeter out past the nose. First connect the two tubes coming out the back to the airspeed sensor. The tube coming straight out the back should go into the top port and the tube exiting at an angle should connect to the bottom port on the airspeed sensor.

<figure><img src="https://ardupilot.org/plane/_images/pitotinstalled1.jpg" alt=""><figcaption></figcaption></figure>

If you are using Plane in an aircraft with the propeller in the nose, the pitot tube must be mounted out on one wing, at least a foot from the fuselage to be outside the prop flow.

See [Pitot Tube Considerations](https://ardupilot.org/plane/docs/common-pitot-considerations.html#common-pitot-considerations) for more infomation on pitot tubes and placement considerations.

### Checking operation

You can check the airspeed reading with Mission Planner or another ground station. Just blow on the pitot tube or press your finger over it and observe the response. In still air oscillation between zero and small values (2-3) is normal. The airspeed varies with the square root of the pressure, so for differential pressures near zero it varies quite a bit with very small pressure changes, while at flying speeds it takes much greater pressure changes to produce a similar change in speed. If you see mostly 0, 1, 2, with an occasional bounce to 3 or 4, consider it normal. You will not see that sort of variability at flying speeds.

### Calibration

The airspeed sensor reading is automatically zeroed by the autopilot during initialization and value set in the [ARSPD\_OFFSET](https://ardupilot.org/plane/docs/parameters.html#arspd-offset) automatically, so it is good practice during windy conditions to placea loose fitting cover over the pitot tube that shields the front hole and the four small side holes from the wind. This cover should be fitted prior to power on and removed before flight. If you forget to do this, you can always place the cover and repeat the airspeed auto-zero using the Mission Planner’s PREFLIGHT CALIBRATE => Do Action.

Note

the `DLVR` type airspeed sensors do not require calibration at initialization and allow [ARSPD\_SKIP\_CAL](https://ardupilot.org/plane/docs/parameters.html#arspd-skip-cal) to be set to “1”, avoiding the need to cover the pitot during initialization.

The airspeed reading scale factor is adjusted using the [ARSPD\_RATIO](https://ardupilot.org/plane/docs/parameters.html#arspd-ratio) parameter. Plane has an automatic calibration function that will adjust the value of [ARSPD\_RATIO](https://ardupilot.org/plane/docs/parameters.html#arspd-ratio) automatically provided the plane is flown with frequent direction changes. A normal model flying field circuit pattern or loiter will achieve the required direction changes, cross-country flying will not. To enable automatic airspeed sensor calibration, set the value of [ARSPD\_AUTOCAL](https://ardupilot.org/plane/docs/parameters.html#arspd-autocal) to 1. See [Calibrating an Airspeed Sensor](https://ardupilot.org/plane/docs/calibrating-an-airspeed-sensor.html#calibrating-an-airspeed-sensor) for more details.

### Miscalibration Safeguards

In order to help prevent Airspeed sensor use when its been miss-calibrated either during ground static calibration during the power up sequence, or by accidental parameter changes to offset or ratio, three parameters are available. If the ground speed is consistently lower than the reported airspeed for a few seconds by [ARSPD\_WIND\_MAX](https://ardupilot.org/plane/docs/parameters.html#arspd-wind-max), i.e. the apparent wind speed is greater than that amount, the sensor can be disabled to avoid erroneous reporting. It can be allowed to re-enable if the apparent wind falls back below that value. These actions are controlled by [ARSPD\_OPTIONS](https://ardupilot.org/plane/docs/parameters.html#arspd-options).

You can also elect to have a GCS text message sent after the ground static calibration which reports the value of the calibration ([ARSPD\_OFFSET](https://ardupilot.org/plane/docs/parameters.html#arspd-offset)). This value should not change significantly (10-20 points) from power-up to power-up significantly. If it does, then you may have forgotten to cover the pitot during power up and need to recalibrate. This option can be selected in the [ARSPD\_OPTIONS](https://ardupilot.org/plane/docs/parameters.html#arspd-options) bitmask parameter.

You can also send a warning to the Ground Control Station if the apparent wind exceeds [ARSPD\_WIND\_WARN](https://ardupilot.org/plane/docs/parameters.html#arspd-wind-warn). This can be used instead of, or together with the above

### Failure

A failing airspeed sensor can lead to the aircraft stalling or over-speeding, this is something that is hard for ArduPilot to detect. Likewise, accidentally miscalibrating the offset during ground initialization can occur if the pitot tube is not covered to prevent wind upsetting the calibration, and can result in wildly inaccurate readings. The parameters below can be used to help detect these conditions and warn of, and/or disable, a failed sensor.

[ARSPD\_WIND\_MAX](https://ardupilot.org/plane/docs/parameters.html#arspd-wind-max) can be used to set the maximum expected wind speed the vehicle should ever see. This is then be used in combination with the GPS ground speed to detect a airspeed sensor error. [ARSPD\_WIND\_WARN](https://ardupilot.org/plane/docs/parameters.html#arspd-wind-warn) can be set to a lower speed to give some warning to the operator before the airspeed sensor is disabled. [ARSPD\_OPTIONS](https://ardupilot.org/plane/docs/parameters.html#arspd-options) can be set to allow sensors to be disabled based on this wind speed metric, a second option bit allows then to be re-enabled if the speed error is resolved.

[AHRS\_WIND\_MAX](https://ardupilot.org/plane/docs/parameters.html#ahrs-wind-max) sets the maximum allowable airspeed and ground speed difference that will ever be used for navigation.

By default, these functions are disabled.

[EKF3 affinity and lane switching](https://ardupilot.org/plane/docs/common-ek3-affinity-lane-switching.html#common-ek3-affinity-lane-switching) is another option for dealing with airspeed sensor failure.

#### Balloon Drop

In the special case of a high altitude balloon drop, the EKF will declare the airspeed sensor faulty since there is gps speed but no airspeed due to air density. This can result in TECS miss-applying pitch. In this special case , the [AHRS\_OPTIONS](https://ardupilot.org/plane/docs/parameters.html#ahrs-options) bit 2 can be set to prevent EKF from declaring the airspeed sensor as faulty and continue to use it.

### Airspeed sensors available from ArduPilot Partners:

#### I2C

* 4525DO
  * [CUAV](https://store.cuav.net/shop/airspeed-sensor/)
  * [Holybro](https://shop.holybro.com/digital-air-speed-sensor_p1029.html)
  * [Matek 4525DO](http://www.mateksys.com/?portfolio=aspd-4525)
  * [mRobotics](https://store.mrobotics.io/mRo-I2C-Airspeed-Sensor-JST-GH-p/m10030a.htm)
* ASP5033
  * [Qiotek ASP5033](https://www.qio-tek.com/index.php/product/qiotek-asp5033-airspeed-sensor-and-pitot-tube)
* DLVR
  * [Matek DLVR](http://www.mateksys.com/?portfolio=aspd-dlvr)

#### DroneCAN

* DLVR
  * [Foxtech AEROFOX Airspeed/Compass](https://www.foxtechfpv.com/foxtech-aerofox-can-airspeed-compass.html)
  * [Holybro DroneCAN DLVR](https://holybro.com/products/high-precision-dronecan-airspeed-sensor-dlvr)
  * [Matek DroneCAN DLVR](https://ardupilot.org/plane/docs/common-matek-uavcan-dlvr.html#common-matek-uavcan-dlvr)
* ASP5033
  * [Qiotek DroneCAN 5033](https://www.qio-tek.com/index.php/product/qiotek-asp5033-dronecan-airspeed-and-compass-module)
* 6897
  * [Foxtech AEROFOX Airspeed/Compass](https://www.foxtechfpv.com/foxtech-aerofox-can-airspeed-compass.html)
* MS5611
  * [Avionics Anonymous Airspeed + Temp](https://ardupilot.org/plane/docs/common-avanon-adc.html#common-avanon-adc)
* MS4525
  * [MakeFlyEasy DroneCAN Airspeed](https://en.makeflyeasy.com/index.php/mfe-can-airspeed-meter/)

### Other Topics

* [Calibrating an Airspeed Sensor](https://ardupilot.org/plane/docs/calibrating-an-airspeed-sensor.html)
* [Pitot Tube Considerations](https://ardupilot.org/plane/docs/common-pitot-considerations.html)
* [Mocking an Airspeed Sensor for Bench Testing](https://ardupilot.org/plane/docs/mocking-an-airspeed-sensor-for-bench-testing.html)
* [Analog Airspeed Sensors](https://ardupilot.org/plane/docs/analog-airspeed-sensors.html)
