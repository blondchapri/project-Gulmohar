# Project Gulmohar

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



















### <a href="https://github.com/blondchapri/project-Gulmohar">Project Gulmohar</a> © 2026 by <a href="https://github.com/blondchapri">shlok shukla</a> is licensed under <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International</a>
# <img src="https://mirrors.creativecommons.org/presskit/icons/cc.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/by.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/nc.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/sa.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;">
