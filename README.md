
<img src="images/PJ102-front.jpg" style="display: inline-block; margin: 0 auto;" width="500">

## PJ102
The PJ102 module is a 29x17mm open-source, stand-alone, programmable temperature and humidity sensor based on HDC1080 and ATtiny85 with [PJON](https://github.com/gioblu/PJON/) over [PJDL](https://github.com/gioblu/PJON/blob/master/src/strategies/SoftwareBitBang/specification) networking. It needs only 3 pins (PJDL, 9v, GND) to operate transmitting samples and receiving incoming configuration on the same PJON IO pin. PJ102 consumes around 0.162w when supplied with 9v.

```cpp  
 _______   _______   _______
|       | |       | |       |
| PJ102 | | PJ102 | | PJ102 |
|_______| |_______| |_______|
____|||_______|||_______|||______ SoftwareBitBang bus
_____||________||________||______ +9v
______|_________|_________|______ GND
```
Multiple PJ102s can be connected in parallel on the same [SoftwareBitBang](https://github.com/gioblu/PJON/tree/master/src/strategies/SoftwareBitBang) bus sharing the same power, ground and data connection. Each PJ102 is running an instance of the PJON protocol stack with its own configurable device id. Up to 255 PJ102s can communicate using the same wire. As demonstrated by the [LANC video camera control](http://jda.tel/pdf/lanc_video_camera_control.pdf) academic study the maximum range of the protocol is 2000m over CAT5 in a real world networking setup.  

[PJ102](https://www.pjon-technologies.com/collections/pj100-hardware/products/pjon-sensor-102) has been engineered with the strong feeling that in the future "smart homes" will not necessarily host an embedded real-time operative system in whatever "thing" and expose vulnerabilities out of their physical boundaries. Otherwise, more probably, they will host many less power-hungry microcontrollers connected to a wired communication bus.

### Software
A basic example program is proposed to let users easily configure the PJ102s using only a series of commands on its PJON [SoftwareBitBang](https://github.com/gioblu/PJON/tree/master/src/strategies/SoftwareBitBang) bus to avoid flashing the chip multiple times. PJ102 accepts incoming requests if sent by `PJON_MASTER_ID` and its configured recipient id.

| Command                          | Parameter     | Effect                                                            |
| -------------------------------- | --------------| ----------------------------------------------------------------- |
|  `?` Info                        | none          | Transmits module name and software version                        |
|  `C` Calibration                 | `int8_t`      | Sets calibration value (expressed in tenth of degrees Celsius)    |
|  `E` Execute                     | none          | Reads a new sample from the DTH and transmits it to the recipient |
|  `G` Get                         | none          | Transmits the latest sample to the recipient                      |
|  `S` Save                        | none          | Reads a new sample and saves it in memory                         |
|  `I` Device id                   | `uint8_t`     | Sets device id                                                    |
|  `R` Recipient device id         | `uint8_t`     | Sets recipient id                                                 |
|  `T` Sleep then transmit         | `uint16_t`    | Sleep for n milliseconds and then transmit reading                |
|  `Q` Configuration block         | none          | Blocks incoming configuration                                     |
|  `X` Configuration reset         | none          | Sets configuration to default                                     |

- `Q` For security reasons it is possible to block incoming configuration, although further configuration is possible flashing the [PJ102](software/PJ102/PJ102.ino) sketch on the ATtiny85 using an ISP programmer.

### How to upload the software
The software is relatively easy to install:
- Follow [High-Low Tech tutorial](http://highlowtech.org/?p=1695) by David Mellis and install the required software  
- Locate the Arduino IDE `boards.txt` file (in Windows is in `C:\Users\your-user-name\AppData\Local\Arduino15\packages\attiny\hardware\avr\1.0.1`)
- Add the code below at the end of the file and save it
```
attiny.menu.clock.external16BOD=PJON PJ100 modules
attiny.menu.clock.external16BOD.bootloader.low_fuses=0xfe
attiny.menu.clock.external16BOD.bootloader.high_fuses=0xdc
attiny.menu.clock.external16BOD.bootloader.extended_fuses=0xff
attiny.menu.clock.external16BOD.build.f_cpu=16000000L
```
- Open the Arduino IDE
- Program an Arduino with the ArduinoISP example
- Connect the Arduino to the ATtiny85 as described in the [High-Low Tech tutorial](http://highlowtech.org/?p=1695)
- Connect a 16MHz oscillator to the ATtiny85 to program it successfully
- Select Tools->Board->ATtiny
- Select Tools->Processor->ATtiny85
- Select Tools->Clock->PJON PJ100 modules
- Select Tools->Burn bootloader
- Open `PJ102.ino`
- Upload the program
- Insert the ATtiny85 in the socket

[PJ102](https://www.pjon-technologies.com/collections/pj100-hardware/products/pjon-sensor-102) should be ready for use

### How to configure it
With the [Console](software/Console/Console.ino) example and an Arduino compatible device it is possible to input configuration and request samples with an easy to use console interface:

- Program an Arduino with `Console.ino`
- Connect the [PJ102](https://www.pjon-technologies.com/collections/pj100-hardware/products/pjon-sensor-102) with the arduino
- Open the serial monitor

You should now be able to communicate with [PJ102](https://www.pjon-technologies.com/collections/pj100-hardware/products/pjon-sensor-102) using the commands described in the software section.

### Schematic
The circuit is quite simple and can be tested quickly on a breadboard. It is composed by few components such as the MCU, its clock, the voltage regulator, a couple of resistors, capacitors and obviously the HDC1080.

![PJ102 schematic](schematics/PJ102.pdf)

### License
All software included in this repository is experimental and it is distributed "AS IS" without any warranty, use it at your own risk. [Licensed](https://github.com/gioblu/PJON/blob/master/LICENSE.md) under the Apache License, Version 2.0. PJON® and its brand are registered trademarks, property of PJON Technologies srl

### Safety warning
When installing or maintaining a PJON network, extreme care must be taken to avoid any danger. If devices are connected to AC power you are exposed to a high chance of being electrocuted if hardware is not installed carefully and properly. If you are not experienced enough ask the support of a skilled technician and consider that many countries prohibit uncertified installations. When a [SoftwareBitBang](https://github.com/gioblu/PJON/tree/master/src/strategies/SoftwareBitBang) bus is installed [interference mitigation](https://github.com/gioblu/PJON/wiki/Mitigate-interference) and [protective circuitry](https://github.com/gioblu/PJON/wiki/Protective-circuitry) guidelines must be followed. When working with an [AnalogSampling](https://github.com/gioblu/PJON/tree/master/src/strategies/AnalogSampling) LED or laser based setup safety glasses must be worn and transceivers must be operated cautiously to avoid potential eye injuries. When connecting a local bus to the internet all devices must be considered potentially compromised, manipulated or remotely actuated against your will. It should be considered a good practice not to connect to the internet systems that may create a damage (fire, flood, data-leak) if hacked.
