# Internal Combustion Engines (ICE)

Canberra UAV’s petrol powered tricopter. [See blog post here](https://discuss.ardupilot.org/t/petrol-boosted-tricopter/17823)

Internal combustion engines can increase flight times but care must be taken to handle the extra complexity and increased vibration.

### What to Buy

* Gas motor and ignition module
* RC Switch for controlling power to ignition module like [this one from milehighrc.com](http://milehighrc.com/switch.html)
* Optionally an electric starter like [this one from milehighrc.com](http://milehighrc.com/EME_E_Start.html)

### Connection and Configuration

The engine’s ignition power switch and (optional) starter should be connected to the autopilot’s servo outputs, `Ignition` and `Starter` (see ICE section in [Autopilot Output Functions](https://ardupilot.org/plane/docs/common-rcoutput-mapping.html#common-rcoutput-mapping)).

ArduPilot 4.6 and laterArduPilot prior to 4.6

* Set [ICE\_ENABLE](https://ardupilot.org/plane/docs/parameters.html#ice-enable) = 1 to enable the ICE feature (you may need to reload parameters after setting this in order to see below parameters).
* Set an RC auxiliary switch (`RCx_OPTION` = 179) be used to start the engine. This channel can start the engine or stop the engine via the `Ignition` and `Starter` motor/servo outputs (see [Autopilot Output Functions](https://ardupilot.org/plane/docs/common-rcoutput-mapping.html#common-rcoutput-mapping)). Normally, the “kill” PWM value is anything below 1300us, but this can be changed using the [ICE\_STARTCHN\_MIN](https://ardupilot.org/plane/docs/parameters.html#ice-startchn-min) parameter. Setting up this channel and its RC control is required for operation in order to provide a “kill” function on the throttle, even if there is no ignition or starter control.
* the PWM value sent to the ignition power switch when the engine should be running or stopped is set by the `SERVOx_MIN` and `SERVOx_MAX` value of the output used for the _Ignition\`_ function.

If using an onboard starter, it is important to configure an RPM sensor for the engine. This will allow the ArduPilot to detect an in-flight engine failure and attempt to restart the engine. ArduPilot supports generic pulse tachometers connected to GPIO pins (such as the Aux servo pins on a PixHawk or Cube). A tachometer may be made using a simple hall effect switch IC. Alternatively, some ignition modules support a tachometer output that can be connected directly to the GPIO pins. Desert Aircraft Electronic Ignition V2 modules support tachometer output on the signal pin of the power input connector. Note that when connecting an RPM sensor to an AUX pin, it is important to make sure that this pin is not configured to output a PWM value and, instead, be a GPIO pin. See [GPIOs](https://ardupilot.org/plane/docs/common-gpios.html#common-gpios).

In order to configure an RPM sensor, the following parameters must be set:

* Set [RPM1\_TYPE](https://ardupilot.org/plane/docs/parameters.html#rpm1-type) to 2 for a standard GPIO input pin.
* Set [RPM1\_PIN](https://ardupilot.org/plane/docs/parameters.html#rpm1-pin) to the appropriate value for the auxiliary pin you are using.
* Set the remaining ‘RPM\_\*’ parameters as appropriate for your system.
* Set [ICE\_RPM\_CHAN](https://ardupilot.org/plane/docs/parameters.html#ice-rpm-chan) to 1.

Throttle control for an ICE engine is similar to controlling a standard brushless ESC. The throttle servo can be connected to any servo output with a `SERVOx_FUNCTION` set to 70 (Channel 3 is configured this way by default). It is important to set the Min and Max PWM values on this servo output to be within the mechanical limits of your throttle assembly (using `SERVOx_MIN` and `SERVOx_MAX`). While doing this, also verify that the servo moves in the correct direction with respect to manual throttle input. Note that the throttle servo will not move unless the vehicle is armed. It is recommended to arm the vehicle with ignition power disconnected in order to test the throttle servo.

After configuring the limits of your throttle servo, the following parameters must be set:

* Set [THR\_MIN](https://ardupilot.org/plane/docs/parameters.html#thr-min) to the desired setting at idle. This will be found empirically during engine testing.
* Set [THR\_SLEWRATE](https://ardupilot.org/plane/docs/parameters.html#thr-slewrate) to a value appropriate for your engine. 20%/s is a good starting point.
* Set [THR\_MAX](https://ardupilot.org/plane/docs/parameters.html#thr-max) if you would like to prevent your engine from reaching full throttle.

If you are using a quadplane and would like the ICE engine to be disabled during a VTOL descent to reduce the risk of prop strikes, set [Q\_LAND\_ICE\_CUT](https://ardupilot.org/plane/docs/parameters.html#q-land-ice-cut) to 1.

### Advanced Starter Configuration

A variety of parameters are available for configuring the engine start routine. The auto-start functionality will attempt to start the engine any time the vehicle is armed, the engine is enabled, and the measured RPM is below the [ICE\_RPM\_THRESH](https://ardupilot.org/plane/docs/parameters.html#ice-rpm-thresh). If the engine is not successfully started within a configurable amount of time, the program will wait for a configurable delay before attempting to start again. It is important to remember that the starter will run in pulses. DO NOT approach the engine between failed start attempts as the starter will attempt to start again if the engine is still enabled.

* [ICE\_START\_PCT](https://ardupilot.org/plane/docs/parameters.html#ice-start-pct) overrides the throttle setting during start.
* [ICE\_START\_TIME](https://ardupilot.org/plane/docs/parameters.html#ice-starter-time) controls the maximum amount of time the starter will run in each start attempt.
* [ICE\_START\_DELAY](https://ardupilot.org/plane/docs/parameters.html#ice-start-delay) sets a delay between start attempts. This can be useful when your starter has a limited duty cycle.
* [ICE\_RPM\_THRESH](https://ardupilot.org/plane/docs/parameters.html#ice-rpm-thresh) sets the minimum RPM reading for the engine to be considered running. This should be set to a value below your idle RPM.
* [ICE\_STRT\_MX\_RTRY](https://ardupilot.org/plane/docs/parameters.html#ice-strt-mx-rtry) if set to a value greater than 0 then the engine will retry starting the engine this many times before giving up.

To allow the pilot to directly control the ignition and (optional) starter from the transmitter, RC pass-through should be set up:

* Set `SERVOx_FUNCTION` (where “x” is the autopilot’s output channel connected to the ignition or starter) to `RCINy` (where “y” is the transmitter channel). For example set [SERVO8\_FUNCTION](https://ardupilot.org/plane/docs/parameters.html#servo8-function) = 59/”RCIN9” to allow the transmitter’s channel 9 to control the autopilot Output 8

Be sure to check the engine’s behavior when the transmitter is turned off to simulate what will happen during an RC failsafe.

### Starting and Stopping the Motor

When using the ArduPilot ICE library to control an engine, the engine can be enabled or disabled using an RC switch, via MAVLink command, or via a mission command. Typically, a 3 position switch on an RC transmitter is used to control the engine. This switch has the following three positions:

* Low: Force the engine to be disabled. This ignores MAVLink commands and mission items that attempt to control the engine’s state.
* Mid: Keep the current state of the engine, but allow MAVLink commands and mission items to change the state of the engine.
* High: Force the engine to be enabled. This ignores MAVLink commands and mission items that attempt to control the engine’s state.

If an `ICE_START_CHAN` is not configured, the behavior will be the same as when the switch is in the middle position.

To start the motor with RC control:

* Arm the vehicle
* Raise the transmitter’s starter switch to start the motor
* If the engine does not include a starter motor, use a hand-held starter motor to start it

To stop the motor with RC control:

* Lower the transmitter’s starter switch
* Disarm the vehicle

QuadPlanes can also automatically stop the engine once the final phase of a VTOL landing is entered by setting the [Q\_LAND\_ICE\_CUT](https://ardupilot.org/plane/docs/parameters.html#q-land-ice-cut) parameter to 1.

Note

The engine can be started and stopped in autonomous missions by using the `DO_ENGINE_CONTROL` mission command. This useful preceding NAV\_VTOL\_TAKEOFF or NAV\_VTOL\_LAND to start or stop the engine to prevent prop strikes while landing in wind with a low clearance prop. See [Mission Commands](https://ardupilot.org/plane/docs/common-mavlink-mission-command-messages-mav_cmd.html#common-mavlink-mission-command-messages-mav-cmd) section.

### Idle and redline governors

These features rely on having a source for engine RPM. See [RPM Measurement](https://ardupilot.org/plane/docs/common-rpm.html#common-rpm) for more information.

The idle governor allows the autopilot to adjust the throttle to maintain an RPM value when the commanded throttle is low. This can be useful when the engine is waiting for takeoff and reduces the workload on the pilot during that time. Increasing the throttle command will give the expected throttle response.

* [ICE\_IDLE\_PCT](https://ardupilot.org/plane/docs/parameters.html#ice-idle-pct) This is the minimum percentage throttle output while running, this includes being disarmed, but not while outputs are disabled by the safety switch.
* [ICE\_IDLE\_RPM](https://ardupilot.org/plane/docs/parameters.html#ice-idle-rpm) This configures the RPM that will be commanded by the idle governor. Set to -1 to disable.
* [ICE\_IDLE\_DB](https://ardupilot.org/plane/docs/parameters.html#ice-idle-db) This configures the RPM deadband that is tolerated before adjusting the idle setpoint.
* [ICE\_IDLE\_SLEW](https://ardupilot.org/plane/docs/parameters.html#ice-idle-slew) This configures the slewrate used to adjust the idle setpoint in percentage points per second.

The redline governor will slowly reduce the throttle if the RPM remains above the given RPM value. Generally, this RPM value is provided by the manufacturer of the engine. If the commanded throttle drops faster or lower than the point the governor started, then the governor will be released and normal control of the throttle will return to normal throttle response. A warning message will appear on the GCS. Setting [ICE\_OPTIONS](https://ardupilot.org/plane/docs/parameters.html#ice-options) bit 1 allows disabling the throttle actions of the redline governor, but still displays the GCS warning message.

* [ICE\_REDLINE\_RPM](https://ardupilot.org/plane/docs/parameters.html#ice-redline-rpm) Maximum RPM for the engine provided by the manufacturer. A value of 0 disables this feature.

### Options

Several options are provided using the [ICE\_OPTIONS](https://ardupilot.org/plane/docs/parameters.html#ice-options) bitmask parameter:<br>
