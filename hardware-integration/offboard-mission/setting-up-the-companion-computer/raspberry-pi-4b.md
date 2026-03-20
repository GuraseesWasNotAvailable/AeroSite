# Raspberry Pi 4B

## **Setting up the flight controller**

Connect to the flight controller with a ground station (i.e. Mission Planner) and set the following parameters:

* SERIAL2\_PROTOCOL = 2 (the default) to enable MAVLink 2 on the serial port.
* SERIAL2\_BAUD = 921 so the flight controller can communicate with the RPi at 921600 baud.
* LOG\_BACKEND\_TYPE = 3 if you are using APSync to stream the data flash log files to the RPi

## **Configure the serial port (UART)**

If the Raspberry Pi’s serial port (UART) is not already configured, it must be enabled. The Raspberry Pi configuration utility can help with this.

Type:

`sudo raspi-config`

And in the utility, select “Interfacing Options”:

_RasPiConfiguration Utility_

<figure><img src="../../../.gitbook/assets/Raspberry Pi Serial 1.png" alt=""><figcaption></figcaption></figure>

And then “Serial”:

<figure><img src="../../../.gitbook/assets/Raspberry Pi Serial 2.png" alt=""><figcaption></figcaption></figure>

When prompted, select `no` to “Would you like a login shell to be accessible over serial?”.

When prompted, select `yes` to “Would you like the serial port hardware to be enabled?”.

Reboot the Raspberry Pi when you are done.

The Raspberry Pi’s serial port will now be usable on `/dev/serial0`.

## **Wiring**

#### **Serial connection**

First wire up the serial connection between the RPi and Ardupilot that is to be used for offboard control.

This setup connects the Pixhawk `TELEM2` port, which is generally recommended for offboard control. It is initially configured in Ardupilot to use with MAVLink, which we will change later when setting up ROS 2. Pixhawk ports can be located anywhere on the flight controller, but are almost always well labeled, and should be obvious on your particular [**flight controller**](https://docs.px4.io/main/en/flight_controller/).

Connect the Pixhawk `TELEM2` `TX`/`RX`/`GND` pins to the complementary `RXD`/`TXD`/`Ground` pins on the RPi GPIO board:

| **Pixhawk TELEM2 Pin** | **RPi GPIO Pin**       |
| ---------------------- | ---------------------- |
| UART5\_TX (2)          | RXD (GPIO 15 - pin 10) |
| UART5\_RX (3)          | TXD (GPIO 14 - pin 8)  |
| GND (6)                | Ground (pin 6)         |

The diagram shows Pixhawk `TELEM2` port pins on the left and RPi GPIO board pins on the right. The pins on the `TELEM2` port are normally numbered right-to-left as shown.

| **`TELEM2`** | **RPi GPIO** |
| ------------ | ------------ |

<div><figure><img src="../../../.gitbook/assets/Connecting RaspberryPi to Pixhawk.png" alt=""><figcaption></figcaption></figure> <figure><img src="../../../.gitbook/assets/Connecting RaspberryPi to Pixhawk (1).png" alt=""><figcaption></figcaption></figure></div>

**INFO**

Almost all recent Pixhawk boards, such as the Pixhawk-6C, use the same connectors and pin numbers for corresponding ports, as defined in the Pixhawk Connector Standard. You can check the specific board documentation to confirm the pin layout.

The standard `TELEM2` pin assignments are shown below.

| **Pins**  | **Signal**       | **Voltage** |
| --------- | ---------------- | ----------- |
| 1 (Red)   | VCC              | +5V         |
| 2 (Black) | UART5\_TX (out)  | +3.3V       |
| 3 (Black) | UART5\_RX (in)   | +3.3V       |
| 4 (Black) | UART5\_CTS (in)  | +3.3V       |
| 5 (Black) | UART5\_RTS (out) | +3.3V       |
| 6 (Black) | GND              | GND         |

#### Connections for Pixhawk 2.4.8

<figure><img src="../../../.gitbook/assets/Connecting RaspberryPi to Pixhawk (3).png" alt=""><figcaption></figcaption></figure>

<mark style="background-color:yellow;">​</mark>:warning:  _<mark style="background-color:yellow;">**It's better to use a direct 5V connection from the battery with a UBEC(Buck Convertor), which converts the voltage to +5V.**</mark>_
