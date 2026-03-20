# Lua Scripts

ArduPilot has introduced support for [Lua](https://www.lua.org/) scripting. Scripting provides a safe, “sandboxed” environment for new behaviors to be added to the autopilot without modifying the core flight code. Scripts are stored on the SD card and run in parallel with the flight code.

This page describes how to setup scripts on your autopilot, the scripting API, scripting applets,and some examples to help get started.

While scripting normally requires at least some LUA programming and editing, there are some [Applets](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/applets) available which require no user editing and are ready to use. See links below.

### Getting Started

* Ensure your autopilot has at least 2 MB of flash and 80 kB of memory. High powered autopilots like the CubePilot Cube Orange and HolyBro Durandal will certainly work well but check the specifications of your [autopilot](https://ardupilot.org/rover/docs/common-autopilots.html#common-autopilots). Scripting is not available in F4 based autopilots. Autopilots must have an SD card to store the script unless the user builds his own firmware and embeds it directly in the firmware (see [OEM Customization](https://ardupilot.org/dev/docs/common-oem-customizations.html#common-oem-customizations)).
* Set [SCR\_ENABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-enable) to 1 to enable scripting (refresh or reboot to see all `SCR_` parameters).
*   Upload scripts (files with extension .lua) to the autopilot’s SD card’s `APM/scripts` folder.

    * If this folder does not exist, you can create it by setting [SCR\_ENABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-enable) to 1 and rebooting. This folder can also be created manually on the SD card.
    * If using Mission Planner, this can be done using MAVFTP.
    * If using a simulator, the `scripts` folder is in the directory the simulator was started from. MAVFTP will work in this case as well.



    <figure><img src="https://ardupilot.org/dev/_images/scripting-MP-mavftp.png" alt=""><figcaption></figcaption></figure>
* **Sample/Example Scripts** can be found [here for stable Plane](https://github.com/ArduPilot/ardupilot/tree/ArduPlane-stable/libraries/AP_Scripting/examples), [Copter](https://github.com/ArduPilot/ardupilot/tree/ArduCopter-stable/libraries/AP_Scripting/examples) and [Rover](https://github.com/ArduPilot/ardupilot/tree/APMrover2-stable/libraries/AP_Scripting/examples). The latest development scripts can be found [here](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/examples).
* **Applets** which are scripts that require no user editing before use, can be found [here](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/applets) for the “latest” firmware branch, and may require code extensions included in that branch to function properly. Each of these has a .md file of the same name detailing its capabilities, use, and setup. For example, there is a script to allow a user to change a SmartAudio capable video transmitter’s output power level from a transmitter channel and set its power-up value via parameter. See [Script Applets](https://ardupilot.org/dev/docs/common-scripting-applets.html#common-scripting-applets).
* **Drivers** Lua scripts can actually provide hardware drivers for new peripheral hardware that is not directly supported in the ArduPilot firmware, such a new EFIs or Gimbals. Examples of these can be found [here](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/drivers) for the “latest” firmware branch, and may require code extensions included in that branch to function properly.

Note

To download from the github locations, first click the script name, then select “raw” in upper right corner, then right mouse click to “Save Page as” a text file with the “.lua” file extension

* Up to 8 RC channels can be assigned as scripting inputs/controls using the\`\`RCX\_OPTION\`\` = “300-307” options to be used by scripts. In addition, four dedicated script parameters are available: [SCR\_USER1](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-user1) thru [SCR\_USER4](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-user4) and are accessed with the same method as any other parameter, but these are reserved for script use. Scripts can also generate their own parameters (see [Accessing/Adding Parameters via Scripts](https://ardupilot.org/dev/docs/common-scripting-parameters.html#common-scripting-parameters))to be used within the scripts.
* When the autopilot is powered on it will load and start all scripts. By default it will look in the ROMFS file system for scripts included in the firmware image by a manufacturer, and the APM/scripts directory on the SD Card (or if a SITL simulation, the base directory where the simulation was started.) This can be modified by used the [SCR\_DIR\_DISABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-dir-disable) parameter.
* Messages and errors are sent to the ground station and, if using Mission Planner, can be viewed in the Data screen’s “Messages” tab.
* [SCR\_HEAP\_SIZE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-heap-size) can be adjusted to increase or decrease the amount of memory available for scripts. The default , which varies from 43K to 204.8K depending on cpu being used, is sufficient at its smallest (43K) for small scripts, but many will require more (some applets now need 300K). The autopilot’s free memory depends highly upon which features and peripherals are enabled. If this parameter is set too low, scripts may fail to run and give an out of memory pre-arm error. If set too high other autopilot features such as Terrain Following or even the EKF may fail to initialize. On autopilots with a STM32F4 microcontroller, Smart RTL (Rover, Copter) and Terrain Following (Plane, Copter) need to be nearly always disabled. These features are usually enabled by default, set [SRTL\_POINTS](https://ardupilot.org/rover/docs/parameters.html#srtl-points) = 0, [TERRAIN\_ENABLE](https://ardupilot.org/plane/docs/parameters.html#terrain-enable) = 0). See also [RAM Limitations](https://ardupilot.org/rover/docs/common-limited-firmware.html#ram-limitations) section.

### Step by Step Setup and Use Examples

* [Script Setup and Use Examples](https://ardupilot.org/dev/docs/common-scripting-step-by-step.html)

### What Scripts Can Do

* Multiple scripts can be run at once
* Monitor the vehicle state
* Start to manipulate vehicle state
* Execute MAVLink commands

Lua is a garbage collected, imperative programming language. Scripts are run at a low priority on the system, which ensures that the core flight code will continue to execute even if a script is taking a long time to process. In order to allow multiple scripts to be run, each script is allocated a fixed time chunk (measured in VM instructions) in which it is expected to complete it’s processing. The script is allowed to specify a new callback function and time at which it should be called, which allows it to be run continuously. While this serves to bound the worst-case execution time safely, scripts are not guaranteed to be run on a reliable schedule.

Each script is run in its own “sandboxed” environment. This allows each script to manipulate any global variables it wants to without causing other scripts problems, and allows the script to persist state between calls to its callback. A side effect of this however is that heavy memory usage in a script can cause other scripts to be short on memory.

### A Sample Script

The following script is used to change a servo output based on the aircraft’s distance from the home position.

```
 1function update () -- periodic function that will be called
 2  local current_pos = ahrs:get_position() -- fetch the current position of the vehicle
 3  local home = ahrs:get_home()            -- fetch the home position of the vehicle
 4  if current_pos and home then            -- check that both a vehicle location, and home location are available
 5    local distance = current_pos:get_distance(home) -- calculate the distance from home in meters
 6    if distance > 1000 then -- if more then 1000 meters away
 7      distance = 1000;      -- clamp the distance to 1000 meters
 8    end
 9    servo.set_output_pwm(96, 1000 + distance) -- set the servo assigned function 96 (scripting3) to a proportional value
10  end
11
12  return update, 1000 -- request "update" to be rerun again 1000 milliseconds (1 second) from now
13end
14
15return update, 1000   -- request "update" to be the first time 1000 milliseconds (1 second) after script is loaded
```

This script begins by defining the update function, which will be called periodically. This function fetches the vehicles current position and home position, and stores them within the local variables current\_pos and home. Both of these variables now either hold a Location userdata, or are nil, if the information was not currently available from the vehicle. The variables are then checked to ensure that they were actually provided, before calculating the distance between the locations. After a check to ensure that the distance is never measured as more then 1 kilometer away, the distance is used to set the servo position. The final line in the function is used to reschedule this function to be run again in 1000 milliseconds from the current time. The last line of the script is also used to schedule the function to be run for the first time in 1000 milliseconds from the time at which it’s loaded.

### Script Crashes and Errors

If scripts run out of memory (or panic for any reason) all currently running scripts are terminated. If an indivdual script has an errror, it will terminate. If either occurs before arming, a pre-arm failure will be generated. A scripting restart command or reboot would be needed to restart the script or scripting as a whole.

In order to prevent arming if a script is missing (ie. SD card ejected or file corrupted) or if a script that is supposed to run once before arming and then terminate, but does not, then either, or both, of two checksum pre-arm checks can be enabled:

* [SCR\_LD\_CHECKSUM](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-ld-checksum) which checks that the checksum of all loaded scripts matches this value
* [SCR\_RUN\_CHECKSUM](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-run-checksum) which checks that the checksum of all running scripts matches this value

These can be set automatically when the scripts have been loaded and the desired ones running before arming, by setting the [SCR\_DEBUG\_OPTS](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-debug-opts) bit 5 which will compute and set these parameter’s values, and then reset bit 5 automatically.

### Scripting and Parameters

* [Accessing/Adding Parameters via Scripts](https://ardupilot.org/dev/docs/common-scripting-parameters.html)

### MAVLink commands

The full set of MAVLink commands can be executed from within a LUA script. An [example is shown here](https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/examples/command_int.lua).

### API Documentation

The API documentation described here is not a complete list, but rather some examples. For a full list of the methods currently available, the LUA auto-generated document file is a complete list. [It can be found here](https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/docs/docs.lua) and lists all the available bindings and their parameters.

Note

If you use VScode for your editor, by installing [this](https://marketplace.visualstudio.com/items?itemName=sumneko.lua) lua extension (version 2.4.1 only as of now), you can get this information integrated as suggestions and autocomplete in the editor.

If you are still getting “Undefined global” errors and no completions, you may need to set path in your local copy of `ardupilot` repository in the [settings](https://code.visualstudio.com/docs/getstarted/settings), for example:`"Lua.workspace.library": [ "~/repos/ardupilot/libraries/AP_Scripting/docs" ]`

For complete information on how a binding works the source can be found in the [binding generator descriptions](https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/generator/description/bindings.desc) .

See [Binding Description Method Syntax](https://ardupilot.org/dev/docs/common-lua-binding-syntax.html#common-lua-binding-syntax) for more information on how to decode the methods shown there. ArduPilot uses Lua version 5.3.5.

#### Location

Location is a userdata object that holds locations expressed as latitude, longitude, altitude. The altitude can be in several different frames, relative to home, absolute altitude above mean sea level, or relative to terrain. To create a new Location userdata you can call Location() to allocate an empty location object, or call a method that returns one to you.

A Location userdata object supports the following calls:

* `lat( [new_lat] )` - If called with no arguments, returns the current latitude in degrees \* 1e7 as an integer. If called with one argument, it will assign the value to the latitude.
* `lng( [new_lng] )` - If called with no arguments, returns the current longitude in degrees \* 1e7 as an integer. If called with one argument, it will assign the value to the longitude.
* `alt( [new_alt] )` - If called with no arguments, returns the current altitude in cm as an integer. If called with one argument, it will assign the value to the altitude.
* `relative_alt( [is_relative] )` - If called with no arguments returns true if the location is planned as relative to home. If called with a boolean value this will set the relative altitude.
* `terrain_alt( [is_terrain] )` - If called with no arguments returns true if the location is planned as relative to terrain. If called with a boolean value this will flag if the altitude should be relative to terrain.
* `origin_alt( [is_origin] )` - If called with no arguments returns true if the location is planned in absolute altitude relative to mean sea level. If called with a boolean value this will set the altitude to be relative to mean sea level.
* `loiter_xtrack( [should_xtrack] )` - If the location is used for a loiter location this flags if the aircraft should track from the center point, or from the exit location of the loiter.
* `get_distance( other_Location )` - Given a Location this calculates the horizontal distance between the two locations in meters.
* `offset( offset_north ,  offset_east )` - Translates this Location by the specified number of meters.
* `get_vector_from_origin_NEU()` - Returns nil or Vector3f that contains the offset from the EKF origin to this location. This will return nil if the EKF origin wasn’t available at the time this was called.
* `get_bearing( other_Location )` - Given a Location this calculates the relative bearing to the location in radians
* `get_distance_NED( other_Location )` - Returns nil or Vector3f that contains the 3D vector in meters to the other Location.
* `get_distance_NE( other_Location )` - Returns nil or Vector2f that contains the 2D vector in meters to the other Location.

#### Vector2f

Vector2f is a userdata object that holds a 2D vector. The components are stored as floating point numbers. To create a new Vector2f you can call Vector2f() to allocate a new one, or call a method that returns one to you.

A Vector2f object supports the following calls:

* `x( [new_x] )` - If called with no arguments, return the currently set X component as a floating point number. If called with one argument it will assign the value to the X component.
* `y( [new_y] )` - If called with no arguments, return the currently set Y component as a floating point number. If called with one argument it will assign the value to the Y component.
* `length()` - Returns the length of the vector as a floating point number.
* `normalize()` - Normalizes the vector to be a unit vector.
* `is_nan()` - Returns true if the vector contains any NaN members.
* `is_inf()` - Returns true if the vector contains any infinity members.
* `is_zero()` - Returns true if all the vector fields are 0.

The following operators are supported on a Vector2f:

* `+` - Adds the two vectors by components.
* `-` - Subtracts the two vectors by components.

#### Vector3f

Vector3f is a userdata object that holds a 3D vector. The components are stored as floating point numbers. To create a new Vector3f you can call Vector3f() to allocate a new one, or call a method that returns one to you.

A Vector3f object supports the following calls:

* `x( [new_x] )` - If called with no arguments, return the currently set X component as a floating point number. If called with one argument it will assign the value to the X component.
* `y( [new_y] )` - If called with no arguments, return the currently set Y component as a floating point number. If called with one argument it will assign the value to the Y component.
* `z( [new_z] )` - If called with no arguments, return the currently set Z component as a floating point number. If called with one argument it will assign the value to the Z component.
* `length()` - Returns the length of the vector as a floating point number.
* `normalize()` - Normalizes the vector to be a unit vector.
* `is_nan()` - Returns true if the vector contains any NaN members.
* `is_inf()` - Returns true if the vector contains any infinity members.
* `is_zero()` - Returns true if all the vector fields are 0.

The following operators are supported on a Vector3f:

* `+` - Adds the two vectors by components.
* `-` - Subtracts the two vectors by components.

#### AHRS (ahrs:)

The ahrs library represents the Attitude Heading Reference System computed by the autopilot. It provides estimates for the vehicles attitude, and position.

* `get_roll()` - Returns the current vehicle roll angle in radians.
* `get_pitch()` - Returns the current vehicle pitch angle in radians.
* `get_yaw()` - Returns the current vehicle yaw angle in radians.
* `get_position()` - Returns nil or Location userdata that contains the vehicles current position. Note: This will only return a Location if the system considers the current estimate to be reasonable.
* `get_home()` - Returns a Location that contains the vehicles current home waypoint.
* `get_gyro()` - Returns a Vector3f containing the current smoothed and filtered gyro rates (in radians/second)
* `get_hagl()` - Returns nil, or the latest altitude estimate above ground level in meters
* `wind_estimate()` - Returns a Vector3f containing the current wind estimate for the vehicle.
* `groundspeed_vector()` - Returns a Vector2f containing the vehicles velocity in meters/second in north and east components.
* `get_velocity_NED()` - Returns nil, or a Vector3f containing the current NED vehicle velocity in meters/second in north, east, and down components.
* `get_velocity_NE()` - Returns nil, or a Vector2f containing the current NE vehicle velocity in meters/second in north and east components.
* `home_is_set()` - Returns a true if home position has been set.
* `prearm_healthy()` - Returns a true if current pre-arm checks are passing.
* `airspeed_estimate()` - Returns current airspeed estimate in meters/second or nil.

#### Arming (arming:)

The Arming library provides access to arming status and commands.

* `disarm()` - Disarms the vehicle in all cases. Returns false only if already disarmed.
* `is_armed()` -Returns a true if vehicle is currently armed.
* `arm()` - Attempts to arm the vehicle. Returns true if successful.

#### Battery (battery:)

The battery library provides access to information about the currently connected batteries on the vehicle.

* `num_instances()` - Returns the number of battery instances currently available.
* `healthy( instance )` - Returns true if the requested battery instance is healthy. Healthy is considered to be ArduPilot is currently able to monitor the battery.
* `voltage( instance )` - Returns the voltage of the selected battery instance.
* `voltage_resting( instance )` - Returns the estimated battery voltage if it was not under load.
* `current_amps( instance )` - Returns the current (in Amps) that is currently being consumed by the battery, or nil if current monitoring is not available.
* `consumed_mah( instance )` - Returns the capacity (in milliamp hours) used from the battery, or nil if current monitoring is not available.
* `consumed_wh( instance )` - Returns the used watt hours from the battery, or nil if energy monitoring is not available.
* `capacity_remaining_pct( instance )` - Returns the remaining percentage of battery (from 0 to 100)
* `pack_capacity_mah( instance )` - Returns the full pack capacity (in milliamp hours) from the battery.
* `has_failsafed()` - Returns true if any of the batteries being monitored have triggered a failsafe.
* `overpower_detected( instance )` - Returns true if too much power is being drawn from the battery being monitored.
* `get_temperature( instance )` - Returns the temperature of the battery in degrees Celsius if the battery supports temperature monitoring.
* `get_cycle_count( instance )` - Returns cycle count of the battery or nil if not available.

#### GPS (gps:)

The GPS library provides access to information about the GPS’s on the vehicle.

* `num_sensors()` - Returns the number of connected GPS devices. If GPS blending is turned on that will show up as the third sensor, and be reported here.
* `primary_sensor()` - Returns which GPS is currently being used as the primary GPS device.
* `status(instance)` - Returns the GPS fix status. Compare this to one of the GPS fix types (GPS.NO\_GPS, GPS.GPS\_OK\_FIX\_2D, GPS.GPS\_OK\_FIX\_3D GPS.GPS\_OK\_FIX\_3D\_DGPS GPS.GPS\_OK\_FIX\_3D\_RTK\_FLOAT GPS.GPS\_OK\_FIX\_3D\_RTK\_FIXED
* `location( instance )` - Returns a Location userdata for the last GPS position. You must check the status to know if the location is still current, if it is NO\_GPS, or NO\_FIX then it will be returning old data.
* `speed_accuracy( instance )` - Returns nil, or the speed accuracy of the GPS in meters per second, if the information is available for the GPS instance.
* `horizontal_accuracy( instance )` - Returns nil, or the horizontal accuracy of the GPS in meters, if the information is available for the GPS instance.
* `vertical_accuracy( instance )` - Returns nil, or the vertical accuracy of the GPS in meters, if the information is available for the GPS instance.
* `velocity( instance )` - Returns a Vector3f that contains the velocity as observed by the GPS. You must check the status to know if the velocity is still current.
* `ground_speed( instance )` - Returns the ground speed of the vehicle in meters per second. You must check the status to know if the ground speed is still current.
* `ground_course( instance )` - Returns the ground course of the vehicle in degrees. You must check the status to know if the ground course is still current.
* `num_sats( instance )` - Returns the number of satellites that the GPS is currently tracking.
* `time_week( instance )` - Returns the GPS week number.
* `time_week_ms( instance )` - Returns the number of milliseconds into the current week.
* `get_hdop( instance )` - Returns the horizontal dilution of precision of the GPS instance.
* `get_vdop( instance )` - Returns the vertical dilution of precision of the GPS instance.
* `last_fix_time_ms( instance )` - Returns the time of the last fix in system milliseconds.
* `have_vertical_velocity( instance )` - Returns true if the GPS instance can report the vertical velocity.
* `get_antenna_offset( instance )` - Returns a Vector3f that contains the offsets of the GPS in meters in the body frame.
* `first_unconfigured_gps()` - Returns nil or the instance number of the first GPS that has not been fully configured. If all GPS’s have been configured this returns 255 if all the GPS’s have been configured.

#### GCS (gcs:)

* `send_text( severity ,  text )` - Will send the text string with message severity level . Severity level is :

| Severity Level | Type      |
| -------------- | --------- |
| 0              | Emergency |
| 1              | Alert     |
| 2              | Critical  |
| 3              | Error     |
| 4              | Warning   |
| 5              | Notice    |
| 6              | Info      |
| 7              | Debug     |

* `set_message_interval( serial_channel ,  message_type ,  rate )` - Sets the message\_type’s update rate on SERIAL(serial\_channel). For example, gcs:set\_message\_interval(0, 30, 500000) sets SERIAL0 rate for ATTITUDE message stream (30) to 2.0 Hz (500,000 microseconds)

#### Serial LED (serialLED:)

This library allows the control of WS8212B RGB LED strings via an output reserved for scripting and selected by SERVOx\_FUNCTION = 94 thru 109 (Script Out 1 thru 16)

* `set_num_LEDs( output_number ,  number_of_LEDs )` - Sets the number\_of\_LEDs in the string on a servo output. output\_number is servo output number 1-16 that the string is attached to with a string having \<number\_of\_LEDs>.
* `set_RGB( output_number ,  LED_number ,  r , g , b )` - Set the data for LED\_number (1-32) on the string attached servo output\_number (1-16) output to the r,g,b values (0-255)
* `send()` - Sends the data to the LED strings

#### Notify (notify:)

* `play_tune( tune )` - Plays a MML tune through the buzzer on the vehicle. The tune is provided as a string.

An online [tune tester can be found here](https://firmware.ardupilot.org/Tools/ToneTester/)

#### Vehicle (vehicle:)

* `set_mode(mode_number)` - Attempts to change vehicle (in this example Plane) mode to mode\_number. Returns true if successful, false if mode change is not successful.
* `get_mode()` - Returns current vehicle mode by mode\_number.

Mode numbers for each vehicle type can be [found here](https://mavlink.io/en/messages/ardupilotmega.html#PLANE_MODE)

* `get_likely_flying()` - Returns true if the autopilot thinks it is flying. Not guaranteed to be accurate.
* `get_flying_time_ms()` - Returns time in milliseconds since the autopilot thinks it started flying, or zero if not currently flying.
* `start_takeoff(altitude)` - Begins auto takeoff to given altitude above home in meters. Returns false if not available.
* `set_target_location(Location)` - Sets target location using a Location object. Returns false if not successful.
* `get_target_location()` - Returns Location object of the current target location. Returns false if not available.
* `set_target_velocity_NED()` - Sets the target velocity using a Vector3f object. Returns false if not available.

#### Terrain (terrain:)

The terrain library provides access to checking heights against a terrain database.

* `enabled()` - Returns true if terrain is enabled.
* `status()` - Returns the current status of the rangefinder. Compare this to one of the terrain statuses (terrain.TerrainStatusDisabled, terrain.TerrainStatusUnhealthy, terrain.TerrainStatusOK).
* `height_amsl( Location )` - Returns the height (in meters) above mean sea level at the provided Location userdata, or returns nil if that is not available.
*   `height_terrain_difference_home( difference, extrapolate)` - find the difference between home terrain height and the terrain

    height at the current location in meters. Returns false if not available. If extrapolate is true, will return based on last valid terrain data.
* `height_above_terrain()` - Returns the height (in meters) that the vehicle is currently above the terrain, or returns nil if that is not available.

#### Relay (relay:)

The relay library provides access to controlling relay outputs.

* `on(relay_num)` - Turns the requested relay on.
* `off(relay_num)` - Turns the requested relay off.
* `enabled(relay_num)` - Returns true if the requested relay is enabled.
* `toggle(relay_num)` - Toggles the requested relay on or off.

#### Servo Channels (SRV\_Channels:)

* `find_channel(output_function)` - Returns first servo output number -1 of an output assigned output\_function (See `SERVOx_FUNCTION` parameters ). False if none is assigned.
* `get_output_pwm(output_function)` - Returns first servo output PWM value an output assigned output\_function (See `SERVOx_FUNCTION` parameters ). False if none is assigned.
* `set_output_pwm_chan_timeout(channel, pwm, timeout)` - Sets servo channel to specified PWM for a time in ms. This overrides any commands from the autopilot until the timeout expires.

#### RC Channels (rc:)

* `get_pwm()` - Returns the RC input PWM value given a channel number. Note that channel here is indexed from 1. Returns false if channel is not available.

#### Serial (serial:)

The serial library provides access to ArduPilot’s serial port infrastructure and associated devices and drivers. The script can communicate with a device attached to the autopilot using the `find_serial(instance)` function. The script can also itself simulate being a device attached to the autopilot using the `find_simulated_device(protocol, instance)` function and `SCR_SDEV*` parameters.

* `find_serial(instance)` - Returns a serial access object that allows a script to interface with a device over a port set to protocol 28 (Scripting) (e.g. `SERIALx_PROTOCOL`, though CAN/NET/etc ports work as well). Instance 0 is the first such port, instance 1 the second, and so on. If the requested instance is not found, returns `nil`.
* `find_simulated_device(protocol, instance)` - Returns a serial access object that allows a script to simulate a device attached via a specific protocol. The device protocol is configured by the parameter `SCR_SDEVx_PROTO`. Instance 0 is the first such protocol, instance 1 the second, and so on. If the requested instance is not found, or the parameter `SCR_SDEV_EN` is disabled, returns `nil`.

The objects returned by the above functions support the following methods:

* `port:begin(baud_rate)` - Start communication at the given baud rate. Must be called to initialize scripting protocol ports (those from `find_serial`). No effect for device simulation ports (those from `find_simulated_device`).
* `port:write(value)` - Write a single byte. Returns 1 if successful, or 0 if failed (i.e. buffer space full).
* `port:writestring(data)` - Write a string. The number of bytes actually written, i.e. the length of the written prefix of the string, is returned. It may be 0 up to the length of the string.
* `port:read()` - Read a single byte. Returns the byte if successful, or -1 if failed (i.e. none available or some other error).
* `port:readstring(count)` - Read up to `count` bytes and return the bytes read as a string. No bytes may be read, in which case a 0-length string is returned. If there is an error, `nil` is returned.
* `port:available()` - Returns the number of bytes available to read. A read of the given number of bytes may fail on certain errors or if there is another reader (e.g. mavlink passthrough), though these are extremely unlikely occurrences.
* `port:set_flow_control(fcs)` - Set flow control setting for scripting protocol ports (those from `find_serial`). No effect for device simulation ports (those from `find_simulated_device`). `fcs` can be 0 to disable, 1 to enable, or 2 for automatic mode.

#### Barometer (baro:)

* `get_pressure()` - Returns pressure in Pascal.
* `get_temperature()` - Returns temperature in degrees C.
* `get_external_temperature()` - Returns external temperature in degrees C.

#### ESC Telemetry (esc\_telem:)

* `get_usage_seconds(channel)` - Returns an individual ESC’s usage time in seconds, or false if not available.

#### Parameters (param:)

* `get(parameter_name)` - Returns parameter value if available, or nil if not found.
* `set(parameter_name)` - Sets a parameter by name if available. Returns true if the parameter is found.
* `set_and_save(parameter_name)` Sets and saves a parameter by name if available. Returns true if the parameter is found.

#### RPM (RPM:)

* `get_rpm(instance)` - Returns RPM of given instance, or nil if not available.

#### Button (button:)

* `get_button_state(button_number)` - Returns button state if available. Buttons are 1 indexed.

#### Servo Output

This method stands alone and is called directly as shown below.

* `servo.set_output (function_number, PWM)` -Sets servo outputs of type function\_number to a PWM value (typically between 1000 and 2000)

### Examples

See the [code examples folder](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/examples)

### How to Add New Bindings

To give Lua scripts access to more features of ArduPilot the API can be extended by creating new bindings. The process is as follows:

* Find the method or function you would like to expose to Lua. For example if you wanted to expose an additional feature of AHRS you would first find the method within [libraries/AP\_AHRS/AP\_AHRS.h](https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_AHRS/AP_AHRS.h). This can be an already existing method (function) or a method (function) newly added to the code.
* Edit the [libraries/AP\_Scripting/generator/description/bindings.desc](https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/generator/description/bindings.desc) and add a new line in the appropriate section for the method, or add a new section if a new class shall be added by following the examples of the other sections.
* Add the method or function to [libraries/AP\_Scripting/docs/docs.lua](https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/docs/docs.lua).
* For releases before Copter/Rover/Plane 4.1: Open a command line prompt and cd to the [/libraries/AP\_Scripting/generator](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/generator) directory and type “make run”.
* For 4.1 onwards, clean the distribution (./waf distclean) and restart compilation from there as usual.

### Include Lua scripts in firmware directly

Lua scripts may be directly embedded in firmware binaries, rather than being placed on an external SD card. This is useful for manufacturer’s to provide the scripts for included peripherals or when the autopilot does not support an SD card. Instructions to do so are on [this link](https://ardupilot.org/dev/docs/common-oem-customizations.html).

### Further Information

For more background on technical decisions made before this point you can reference the presentation from the 2019 ArduPilot Unconference.

{% embed url="https://youtu.be/ZUNOZMxOwsI" %}

{% embed url="https://youtu.be/3n80dYoJQ60" %}
