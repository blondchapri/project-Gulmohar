Gulmohar is a safety and general control circuit used for safe and reliable operation of the vehicle  
everything in this project is designed according the rules provided in the 2027 [SAE BAJA Rule Book](https://www.bajasaeindia.org/upload/Resource/BAJA%20SAEINDIA%20RULEBOOK%202027_1782183585.pdf).<br/>
This project is heavily inspired/derived from [Michael Ruppe](https://github.com/michaelruppe/FSAE/tree/master/Precharge)'s work. Please go and show him some support he is literally the [GOAT](https://youtu.be/cnhg-fT3LRs?si=Fe791WXtpYPyJvJ2) <br/>
this poject was developed for [Team Pegasus Racing](https://www.instagram.com/team_pegasus_racing/)
<img width="1600" height="859" alt="image" src="https://github.com/user-attachments/assets/0f57c978-0606-4547-b101-d16efeb751ed" />

# Basic Overview 
After the startup sequence (check the rulebook for that), the board closes the precharge relay and begins reading voltage inputs from the main tractive accumulator and from the positive line after the precharge resistor.<br/>

Closing the precharge relay starts charging ("energizing") the motor controller's empty/de-energized capacitors through the precharge resistor, which prevents large inrush currents.

When the accumulator voltage and the tractive line voltage reach 95% of each other, the precharge relay opens and the AIR (Accumulator Isolation Relay) closes. This puts the buggy into Ready to Drive mode, triggers the TSAL, and plays the RTDS sound through the speaker.

If a fault is detected during this process such as the precharge taking too long or completing too early the board sends error signals over the CAN bus and turns the fault LED on the board ON. In this case, the buggy does not enter Ready to Drive mode.
## Components used
* BLUEPILL(STM32F103C8T6)
* DFR0299 
* xt60W 
* 4N35
* SN65HVD230
* MP2338GTL-Z
* SRD-05VDC-SL-C
* LM331N
* LM2904
### the circuit takes 4 inputs <br/>
- **12v lead acid battery** used for powering the board <br/>
- **negative terminal of the main accumulator**<br/> 
- **positive terminal of the main accumulator**<br/>
- **positive line of the tractive system after the precharge resistor** <br/>

### and has 5 outputs <br/>
* **de- energizer circuit trigger**
* **precharge relay trigger**
* **AIR trigger**
* **TSAL trigger** <br/>
* **SPEAKER OUT** for tractive system active sound
* **CAN bus** for communication with other system in the vehicle <br/>

# Operation
this section goes over what each subsystem does in the board 
## Main MCU
<img width="1244" height="1120" alt="image" src="https://github.com/user-attachments/assets/3f2d1e88-a15e-48af-af63-2e948920c732" />
The Blue Pill (STM32F103C8T6) was selected as the microcontroller for this board over alternatives such as the Arduino Nano/Uno and the STM32 Nucleo/Discovery development boards.  

Compared to Arduino-based boards, the Blue Pill offers a faster clock speed, more GPIO pins, and native CAN bus support (via its bxCAN peripheral), which is essential for communicating with the rest of the vehicle's control system without relying on an external add-on module.  
Compared to Nucleo/Discovery boards, the Blue Pill is significantly cheaper and smaller, making it more practical for integration onto a custom PCB rather than being used as a standalone development board. 

While the Blue Pill has known drawbacks such as inconsistent quality control across clone manufacturers and a USB implementation that often requires rework for reliable use these were considered acceptable trade-offs given the team's budget constraints, the large amount of open-source documentation and community troubleshooting resources available, and the ease of sourcing replacement units quickly if a board fails.
## speaker controler 

<img width="1683" height="815" alt="image" src="https://github.com/user-attachments/assets/6665681f-55f6-488e-8baf-0fefb92062f3" />
Similar to the Blue Pill, the DFR0299 (DFRobot DFPlayer Mini MP3 module) was chosen because it is cheap and easy to source. It also gives the team the ability to play custom sounds for the RTDS (Ready-to-Drive Sound) rather than being limited to a simple buzzer tone, and additionally allows the team to play music through the same speaker during the off-season.

The module is connected to the MCU via UART for serial communication, along with four buttons two for volume control (up/down) and two for sound selection (next/previous sound). The speaker output is connected using an XT60 connector.
## can transceiver
<img width="1618" height="953" alt="image" src="https://github.com/user-attachments/assets/1d7e57a3-7c40-46bc-bdb7-1df127c7aa4c" />
The SN65HVD230 was selected as the CAN transceiver for the board. It interfaces between the Blue Pill's CAN controller (bxCAN) and the physical CAN bus, converting the microcontroller's logic-level TX/RX signals into the differential CAN_H/CAN_L signaling required by the bus. It was chosen for its low cost, wide availability, and 3.3V logic compatibility, which matches the Blue Pill's native voltage level without requiring additional level-shifting circuitry.

## trigger/control relayes 
<img width="1559" height="555" alt="image" src="https://github.com/user-attachments/assets/61065512-766e-4305-a6d4-c2ecf8f2757c" />
This subsystem is what actually controls and enables the high power components on the buggy. The SRD-05VDC-SL-C relays were selected for this role because they are cheap, widely available, and simple to work with, which made them a practical choice given the team's budget and timeline.

The AIR (Accumulator Isolation Relay), TSAL (Tractive System Active Light), and precharge relay all operate on a 12V supply. Rather than switching the 12V supply side of each component directly, the board's relays are wired to complete each component's path to ground. In other words, each 12V component is permanently connected to its 12V source, and it only activates once its ground return path is completed through the relay's normally-open contact. This is commonly referred to as low-side switching.

Each relay coil itself is a 5V coil, so it is powered from a separate 5V rail rather than directly from the Blue Pill's 3.3V logic. Since the Blue Pill's GPIO pins cannot supply enough current to drive the relay coil directly, each coil is switched using a BC847 NPN transistor. The MCU's GPIO pin drives the base of the BC847 (through a current-limiting resistor), which allows the transistor to switch the relay coil's ground connection on and off. When the GPIO output goes high, the BC847 turns on, completing the coil's circuit to ground, energizing the coil, and closing the relay contact — which in turn completes the 12V component's ground path and switches it on. A flyback diode is placed across each relay coil to protect the BC847 from the voltage spike generated when the coil is de-energized.

In this approach low-side switching with an NPN transistor was chosen over high-side switching because it is simpler and cheaper to implement. High-side switching (controlling the 12V supply side instead) would require a PNP transistor or a MOSFET along with additional level-shifting circuitry to interface safely with the 3.3V logic of the Blue Pill, adding unnecessary cost and complexity.
## 5V PSU

<img width="722" height="491" alt="image" src="https://github.com/user-attachments/assets/ac51ceff-ce60-4279-a4d3-6c31f56fe6ca" />
The 5V rail used to power the relay coils (and any other 5V logic on the board) is generated using an MP2338GTL-Z, a buck converter IC that steps the 12V battery input down to a regulated 5V output, capable of supplying up to 3A.

A buck converter was chosen over a simple linear regulator (e.g., a 7805) for efficiency. A linear regulator dropping 12V to 5V would waste significant power as heat, especially under the combined current draw of multiple relay coils switching simultaneously, whereas a switching buck converter handles this same conversion far more efficiently, with less wasted power and less heat to manage on the board.

The 3A output capacity also provides sufficient headroom to reliably power all relay coils simultaneously, along with any other 5V loads on the board, without the converter running near its limit.




















### <a href="https://github.com/blondchapri/project-Gulmohar">Project Gulmohar</a> © 2026 by <a href="https://github.com/blondchapri">shlok shukla</a> is licensed under <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International</a>
# <img src="https://mirrors.creativecommons.org/presskit/icons/cc.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/by.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/nc.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/sa.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;">
