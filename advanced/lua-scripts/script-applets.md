# Script Applets

These are scripts that require no user editing before use, and can be found [here](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_Scripting/applets) . Each of these has a .md file of the same name detailing its capabilities, use, and setup.

Below is a list of applets and their use as of this writing. More are being added continually.

| Applet Name                                   | Description                                                                                                  |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| BattEstimate.lua                              | Battery state of charge estimator                                                                            |
| Gimbal\_Camera\_Mode.lua                      | Select auto or manual gimbal/camera control mode                                                             |
| Heli\_IM\_COL\_Tune.lua                       | Traditional IM\_COL\_TUNE tuning applet                                                                      |
| Heli\_idle\_control.lua                       | Allows manual or automatic rpm control while in ground idle condition                                        |
| Hexsoon LEDs.lua                              | Hexsoon Multicopter LED control script                                                                       |
| MissionSelector.lua                           | Selects between one of three missions on boot depending on switch state                                      |
| ONVIF\_Camera\_Control.lua                    | CNIF Camera Control                                                                                          |
| Param\_Controller.lua                         | Allows switch selection of parameter sets at boot                                                            |
| QuadPlane\_Low\_Alt\_FW\_mode\_prevention.lua | Prevents changing to FW at low altitude                                                                      |
| RockBlock.lua                                 | Rockblock modem                                                                                              |
| Script\_Controller.lua                        | Allows selection of multiple scripts and missions on SD Card                                                 |
| SmartAudio.lua                                | Smart Audio control                                                                                          |
| UniversalAutoLand.lua                         | Allow easy, unpre-planned operation at any location with the protection of a do-land-start autoland sequence |
| VTOL-quicktune.lua                            | VTOL-quicktuning applet                                                                                      |
| advance-wp.lua                                | Advance the current mission waypoint via an RC switch                                                        |
| ahrs-set-origin.lua                           | Sets the AHRS/EKF origin to a specified Location                                                             |
| ahrs-source-extnav-optflow.lua                | Help vehicles automatically switch between ExternalNav and optical flow                                      |
| camera-change-setting.lua                     | Allows changing some camera settings that are not normally used by the autopilot                             |
| copter-deadreckon-home.lua                    | Flies towards home on loss of GPS                                                                            |
| copter-slung-payload.lua                      | Reduce a slung payload’s oscillation                                                                         |
| copter\_terrain\_brake.lua                    | Useful when flying in LOITER mode in steep terrain                                                           |
| follow-target-send.lua                        | Sends the FOLLOW\_TARGET mavlink message to allow other vehicles to follow this one                          |
| forward\_flight\_motor\_shutdown.lua          | Forward flight motor shutdown                                                                                |
| leds\_on\_a\_switch.lua                       | Control NTF\_LED\_BRIGHT value using an AUX switch                                                           |
| motor\_failure\_test.lua                      | Motor failure testing script                                                                                 |
| mount-poi.lua                                 | Displays the location (lat, lon and altitude) that the gimbal is currently pointing towards                  |
| net\_webserver.lua                            | This implements a web server for boards that have networking support                                         |
| pelco\_d\_antennatracker.lua                  | Maps AntennaTracker outputs to corresponding Pelco-D messages                                                |
| plane\_aerobatics.lua(in Aerobatics subdir)   | Autonomous trajectory precise aerobatics                                                                     |
| plane\_package\_place.lua                     | Quadplane payload place script                                                                               |
| plane\_precland.lua                           | Precision landing/loitering system for QuadPlanes                                                            |
| plane\_ship\_landing.lua                      | Script to automate moving vehicle landing with a beacon                                                      |
| repl.lua                                      | Interactive REPL (read-evaluate-print-loop) for the Lua scripting                                            |
| revert\_param.lua                             | Switch allows you toevert all the parameter changes to the values from startup                               |
| rover-quicktune.lua                           | QuicK tuning applet for Rovers                                                                               |
| runcam\_on\_arm.lua                           | Starts/stops video recording on arm/disarm                                                                   |
| video-stream-information.lua                  | Populate the VIDEO\_STREAM\_INFORMATION mavlink message                                                      |
| sport\_aerobatics.lua(in Aerobatics subdir)   | Autonomous rate based aerobatics                                                                             |
| winch-control.lua                             | Allows the winch to be deployed or retracted using an auxiliary switch                                       |
| x-quad-cg-allocation.lua                      | Scriptable control allocation matrix to request asymmeterical front and back thrust                          |
