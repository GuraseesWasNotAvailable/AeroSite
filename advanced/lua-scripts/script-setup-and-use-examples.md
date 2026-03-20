# Script Setup and Use Examples

### Testing a Script in SITL

In Linux:

1. Assuming you have setup the build and simulation environment, run the simulation. See [Using SITL](https://ardupilot.org/dev/docs/using-sitl-for-ardupilot-testing.html#using-sitl-for-ardupilot-testing). For example:

```
sim_vehicle.py -v plane -f quadplane
```

Mavproxy ground control station will automatically be started. You can also start QGC (using the QGroundControl.AppImage) and/or Mission Planner (using WISE to run under Linux). Any or all three can be used simultaneously.

1. From your GCS, enable scripting by setting the [SCR\_ENABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-enable) parameter to “1”. Restart the simulation.
2. A “scripts” sub-directory will be created in the directory you started the simulation, if it did not already exist. Place the script you downloaded into this directory. Again, restart the simulation.
3. If you get a “Scripting: out of memory” GCS message then you will need to increase [SCR\_HEAP\_SIZE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-heap-size) parameter and try to restart the simulation.

In Mission Planner (Windows):

1. Start the simulation in Mission Planner as normal (see [Mission Planner Simulation](https://ardupilot.org/planner/docs/mission-planner-simulation.html#mission-planner-simulation))
2. In the CONFIG->Full Parameter List, set the [SCR\_ENABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-enable) parameter to “1”. Restart the simulation.
3. The ‘scripts’ subdirectory will be located in your Documents->Mission Planner->sitl directory.Place the script you downloaded into this directory. Again, restart the simulation.
4. If you get a “Scripting: out of memory” GCS message then you will need to increase the [SCR\_HEAP\_SIZE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-heap-size) parameter and try to restart the simulation.

### Running a script on your Autopilot

1. Connect your GCS and navigate to its parameters screen (CONFIG->Full Parameter List in Mission Planner, Vehicle Setup->Parmeters in QGC) . Enable [SCR\_ENABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-enable) =1. Disconnect and reconnect the autopilot.
2. On your SD card, place the script in the APM/scripts directory. This directory will be created on the SD card when you have enabled the [SCR\_ENABLE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-enable) via your GCS. If it does not exist, create it and load the script. You can use Mission Planner’s CONFIG->MAVFTP tab or take out the SD card and use a PC to write the script onto the card.
3. Restart the autopilot with the SD card in and the script(s) on it will load and execute.
4. If you get a “Scripting: out of memory” GCS message then you will need to increase [SCR\_HEAP\_SIZE](https://ardupilot.org/dev/docs/AP_Periph-Parameters.html#scr-heap-size) parameter and try to restart the simulation. How much memory is needed is dependent on the script and the configuration of peripherals and features in use on the autopilot.

### Once Script is running

Many scripts will require parameters created by the script to be setup or RC switches configured. This should be explained in the script’s README.md file co-located with the script in the ArduPilot repo.<br>
