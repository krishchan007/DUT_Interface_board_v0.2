# Design details
- Rpi pico section is referenced from mini DCU and Picoprobe design by shawn hymel.
For RP2040, default part from kicad library is used.
- DNP resistor by shawn hymel is not used since it interferes wit the operation of crystal (based on practical experience).
- 2.54mm pitch JST connectors is going to be used as SWD interface connector which enables use of both dupont connectors (jumper wires) and JST for SWD interface with a picoprobe.
GPIO25 is used for Inbuilt_led similiar to the default configuration.
- LP5907MFX-3.3/NOPB is used as 3.3v LDO, part is considered due it's low profile (SOT-23) and availability in evelta.
- Almost all decoupling capacitors used are 0402 to reduce the PCB adrea and increase ESR performance.
- Even though the flash MX25R1635FZUIL0 is quite small in size and has under side pads, ith can be hand soldered. So fit is an option.
- Crystal being used is FY1200108 - 12MHz. Footprint of a similiar part number (FY1200127 ) is used.
- Maybe the new parts can be used in another version, try to stick with the previous parts that way the uncertainities can be avoided. If it is a connector or switch that is understandable, but with crystals, flash etc. being active components, any thing can go wrong. So maybe leave some footprints and see whether it works, otherwise go with the safe tested parts.
- What if we use a USB B connector with it?
- Use a precision shunt for ADC reference for MCU.
- Use light sesnor TCS34725 for measuring RGB light, https://www.youtube.com/watch?v=dCnjwxkWZ-w&ab_channel=learnelectronics
- I2C colour sensor can also be connected to RPi pinout. Leave some jumpers for that and at the MCU side too.
- RF programmer and SWD interface for pogo pins are added, pinout and footprint wll be similiar to ones in the ISv2 design.
- Hongfa HFD4/5 0.5A 5VDC is going to be used for power supply switching applications of DUT.
- Relay_DPDT_AXICOM_IMSeries_Pitch5.08mm footprint from Relay THT library is used after thorough cross verification with datasheet.
- Since dig_10 and dig_11 is NRF_RST, in ISv2_J2, only dig10 is being selected for RF programmer pinout.
- TXS0104EDR is used as level translators to avoid any 5v logic signal damaging the RP2040. Also It has enough ESD protection.
- dig22 is not connected since that in is not used in IsV2 and IsV4, also to save one PCB footprint space of level translator.
-An I2C buffer is added to interface with external I2C devices such as colour sensor.
- Extra I2C connectro is provided for any future use.
- 

# changes for v0.2

## Design decisions
- Through hole conncetor is used for RPi to enable future proofing the header for any experimental or testing purpose.
- AD2 is being moved to a side since there is enough space in the PCB
- Instead of micro USB conncetors, header pins are provided to enable usage of adapter pcb, since USB is not required for MCU in the run time.
- Rpi connector is placed in sucha way that the USB is facing front of the box and there is access to all DSI and CSI conectors without removing Pi from assembly.
Also the availability of headers is facing to an accessible bottom position.

### Schematic changes
- SS (Slave select) for SPI bus is connected to RP2040 GPIO inorder to enable slave select function by AD2 or EE101. A seperate module has to be progammed in RP2040 to enable SPI slave mapping.
- ~~SS is added as bus_8~~
- ~~Connect 3.3VA to 3.3V supply through a ferrite bead~~
- ~~For all the level translators, no decoupling is provided at high voltage side. This may cause issues. Next time make sure it does't happen.~~
- ~~Alert of ADC is connected to RP2040 and is mapped to RPi IRQ in firmware.~~
- ~~Add N-channel drving to P-channel MOSFET of DUT PSU section~~
- Tap out unused GPIOs in MCUs.
- Work out level translator voltage issues from secondary to primary with respect to power rails used.
- ~~Remove I2C address selectors tied to SDA and SCL pins in ADC. Same with INA219 too~~
- Verify RF_programmer connector with comparing pinout to physical TC-2050 connector.
- Level translator sides needs to be switched, high voltage to Rpi pico and low voltage to nRF side, this prevents damage to nRF and also translates the 3V well.
- New bus for nRF side power supply, derived from 3.3V PSU by connecting two schottky diodes in series. This is to satisfy minimum operation requirments of TXS0104EDR level translator.
If the drop is more acros two diodes it can be compensated by removing one.

### Routing changes
- don't put vias to gnd layer in small pads such as 0402 (since 4 layer board). Thermal mass cause issues while soldering. Make it as far as possible.
- Move C28 capacitor, the clearance is very less with flash and is shorting 3v3 and GND.
- 
- 
- Give labels for SWD connectors. Needs only one SWD connector (check feasibility). 
- Supply voltage for servos are currently derived from rpi header, change that to VSYS.

### Footprint changes
- ~~ Footprint of flash needs to be wide. ~~
- Right angle header is added for EE101.
- EE101 jumper uses straight 2 line header instead of single jumpers.
- Buzzer footprint needs to be changed, current one pitch is too small.
- Change footprint of USB-LC6 or entirely remove it, since we don't have this part in stock. (can be bought from sunrom).
- On U13 corrcet the labels in high and low side.
- Labels are absent for EN and Output for U3. 

### Part changes
- Instead of 0E, 10E is use for UART bridges.
- Crystal caps used is 22pf from assortment
- R34 and R36 is 1K and R35 and R37 is 2K(ratio is too high, so didn't work, never mind)
- R8 is 220 ohms and R9 is 470 ohms
- 100R series resistors with SWD pins as per "debug-connector-specification" document.
- FCM 1608KF-601T03 is used for ferrites (from IGV4 stash).
- Add ESD protection and series resitor short protection (22E) for all proramming pins.
- ~~Add reset header jumpers, for selective programming.~~
- Add board side 90deg female header for EE101.
- Series resistors in digital to digital pins, especially clk signals to prevent EMC issues.
### Miscelleaneous
- Before sending the board, check all the inventory, match footprints with BOM.
- Breadboard the 3.6v power control section. And verify before layout. Currently control is only possible by high-Z state. Driving using BJT an option.
- Ensure correct Spacing beteween RPi conector and mounting holes.
- How to mount tagconnet 2050 for ISv4?
- Make GPIO budget for Both MCUs.

### Stuff to procure
- MIcro USB PCB adapter
- Procure IDC connector

