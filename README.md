# SIO2MKR
Adjustable USB Powered Split Rail Power Supply for Breadboard Prototyping.

## Overview
This PCB design details a device to power an electronics breadboard for non-soldered prototyping. The project was commissioned by the University of Cape Town to allow students to work on electronics practicals from home. The device is powered off a standard 5V micro USB adapter, such as most cellphone adapters, and produces two independently adjustable positive and negatice supplies.

## Features
The device is controlled by:
1. A slide power switch.
2. 2 potentiometers for voltage adjustment.

The device has 1 input: 5V power from a suitable 5W USB power adapter.
The input is protected by a 3A Slow-Blow fuse, and is switched via a PMOS transistor controlled by the power switch.

The device has 4 outputs:
1. Adjustable Positive Voltage.
2. Adjustable Negative Voltage.
3. 5V USB Passthrough.
4. Non-isolated Ground.

The voltage is adjustable from ±1.2V to ±10.5V at a current rating of 40mA (at peak voltage output). Higher currents, up to 100mA, may be achieved at lower voltage. Current set resistors limit the power draw from the USB adapter to 1A, however if additional current is drawn from the USB passthrough is not accounted for. Ground is non-isolated, meaning it is the shared with the USB adapter.

## Contents
The repository has 4 sections:
1. FAB: Contains the PCB fabrication Gerber and drill files.
2. PCBA (JLCPCB SMT Assembly): Pick & Place and BOM files for automated assembly of components.
3. PCBA (No Variations): Same as above but including components not to be assembled by JLCPCB.
4. SRC: Altium Designer source files (can be imported to KiCAD).

## Manufacture
The device may be manufactured and assembled by JLCPCB. The cost per board comes to around $11.00 (10 units).

## Design Description
This design features 2 parallel power conversion circuits, one for the positive rail and one for the negative rail, with very similar operation. The power follows the following path:
USB -> Fuse -> Switch -> Switch Mode Boost Converter -> Linear Regulator
                      -> Switch Mode Inverter -> Linear Regulator
### Fuse
The fuse is a simple 3.0A slow-blow fuse. This is a secondary protection feature, since the Switch Mode Converter and Linear Regulator both have current limiting capability. Note issue #2 below.

### Switch
The switch is in fact a P-MOSFET controlled by slide switch. Note the ESD issue below.

### Switch Mode Converter
The Switch Mode Controller is the MC34063 regulator from ON Semi. Other pin-compatible devices are available from Diodes Inc. The device is a simple controller with an integrated darlington transistor and current sensing capability. Both the boosting and inverting supplies use this controller, and the same peripheral components due to the similar circuit characteristics of boosting and inverting switch mode supplies.

The inductor is rated at a high current value (0.95A) to improve efficiency. A large 400µF Tantalum Capacitor bank is supplied to stabilise the output, but may be increased without bound for improved performance.

The SS34 Schottky diodes are over specified but are generally quite cheap and this eliminates a commonly experienced failure mode. The positive supply features an inrush diode — which charges the capacitor bank to 5V immediately by bypassing the inductor. This should improve the startup performance of the positive supply, however this advantage is not possible with the inverting supply.

The timing capacitors set the switching frequency of the supply. These values are a bit dependent on biasing effects and should be measured in prototyping for exact specifications.

### Linear Regulator
The linear regulator is the universal LM317L and LM337L positive/negative regulator pair.

The linear regulators produce a much more stable output than the switch mode supply, necessary for analogue circuits. The devices are adjustable using the standard 1.25V reference feedback. A 2.7kΩ resistor in parallel with the 5kΩ potentiometer results in a roughly linear adjustment of the operational range of the switch mode supply.

### Tracking Pre-Regulator Feedback
The switch mode supply is configured as a tracking pre-regulator via the feedback network from the linear regulator’s output. The tracking voltage is set to 3V to adequately accommodate the dropout voltage of the LM317L and LM337L and to stiffen the regulator, while maintaining good power efficiency. The 5.6kΩ feedback resistors reduce the gain of the feedback, which should ensure stability, however this is still to be verified in testing.

### LED Indicators
3 LEDs are supplied to indicate USB power, the positive rail and the negative rail. The USB indicator is simple. To improve power efficiency, the split rail indicators are switched on 5V power by sensing the voltage across the 240Ω linear regulator's feedback resistor. This method relies on the gate threshold voltage of the sensing MOSFET, which is not absolutely certain to turn on fully but should be enough to fully turn on the 5V side MOSFET.

## Known Issues
### 1. Computer USB Power
The USB standard specifies that only 100mA may be drawn initially before power negotiation, and thereafter the device is only obliged to supply 500mA. This device does not communicate with its source at all. The power limit may be lowered by changing the 1206 current sense resistors (note under Features), however the initial surge is not yet tested. Powering the device from a PC USB port is therefore not advised, rather use a 5W or greater cellphone charger.
### 2. Fuse Rating
The fuse is rated at 3A, which is quite large. This is due to supply issues, and a 1.0A-2.0A rated fuse is more desirable. The part may be replaced with another 1206 Slow-Blow fuse.
### 3. Capacitor Discharge
Discharging the sizeable 800µF capacitor bank when switched off is not explicitly designed, however this is a low voltage bank and is not considered a safety risk.
### 4. ESD Protection
No ESD protection is provided. The most vulnerable component is the power PMOS transistor. Problems are to be tested with the initial prototype.
### 5. Tracking Pre-Regulator Stability
The tracking pre-regulator feedback network presents the possibility of destabilising the system. Although this can be simulated or otherwise modelled, the tolerances of the components are quite large and the exercise involved modelling the MC34063. Instead then, this will be stress tested during prototyping.

## Disclaimer
Please note the disclaimers detailed within the design files. The author assumes no liability in the use of this device, nor guarantees any of its function or availability.

© LD Stanton 2021
