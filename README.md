# Simple-PWM-Fan-controller_v2
Temperature controlled, PWM-steered Voltage-Fan with small Temperature-Range
  
**New Circuit using only few cheap parts: 1 CMOS-Schmitt-Trigger, 2 Diodes, 2 Resistors, a 100k-NTC and a Timing-Capacitor**.

The first version of this Fan-controller had a problem with the NTC-bandwidth (too few resistance-variation, varying temperatures between small bandwidth (here 25°C to 35°C), resulting to output too small and ineffective PWM percentage-variations (about 50% to 60%) to get really usable wide Fan RpM-variations.  
So to get a PWM-variation from 50% to 100% within the targeted small Temperature-Bandwidth Input (10°C difference) it needs a few circuit-modifications.

General Comments
----------------
Many (cheap) fans used for cooling electronic circuits are quite noisy, working at its nominal voltage (mostly 12V), but get quietly reducing its Supply-Voltage. Most of them can start-up and work with down to half of its normal Supply-Voltage. So generally there is no need to operate the fan the whole time with full voltage. "Full-Power" only is needed to cool down peak-temperatures – which may be done with a temperature-dependent variable voltage, using either PWM (Pulse Width Modulation of a DC-Voltage), passing (positive) Voltage-Pulses with variable time-width to the fan-motor or using modern DC-DC-Voltage converters, discarding here old analog converters, which only eliminates upper voltages into heat.  

One disadvantage of a simple PWM-Rectangle-Voltage is - it produces hearable resonances in motor-windings, thus producing more noise as before - if not choosing lower or higher frequency-ranges, which may be either below 30Hz or above 20kHz (resp. non-hearable frequencies...).  
A second disadvantage: PWM-pulses at high frequencies can produce electromagntic disturbances in combination with motor windings (EMC).  
But great advantages of PWM are its simplicity to obtain lower voltages from higher DC-Voltages - together with its heat-preventing principle. So I choosed this principle to generate adaptable voltages, here as a temperature controlled voltage to operate cooling-fans.

As Temperature-Sensor I used a NTC-3950 Thermistor with 100kOhm (at 25°C), which often is used as Temperature-Sensor in 3D-Printers.

The used TC4S-584F IC is a single CMOS Schmitt-Trigger in a small SOT23-5 Package, supporting voltages up to 18V, although it only provides about 4mA maximum Output-Power (in contrast to newer SN74-chips which mostly are faster and "stronger", but can't be run above 5.5V Supply-Voltage) to steer Power-Mosfets.  
This Schmitt-Trigger produces the needed PWM-frequencies in combination with resistor R3 and capacitor C3, modified with resistors R1 and R2. PWM-frequencies vary with temperature, ranging from about 30Hz at Room-Temperature (25°C) to 0Hz (=full-DC-Power) at ~35°C and above. The low PWM-frequency-bandwidth in combination with half-fan-speed results as relatively quiet to noiseless. Nevertheless frequencies can be modified also independently only with time-capacitor C3.

With a small Power-Mosfet in SOT23 package (P-Mosfet IRLML6402 with 20V, 4A, 65mOhm) the PWM-pulses in this frequency-range are steep enough, so heat-losses on this small output stage are negligible.

For greater fans with more Power-consumption (consuming more than about 2 Amps) I suggest to add a N-Mosfet-Stage -as outlined in the schematic- with 1kOhm Gate-Resistor to GND, f.ex. the IRLR2905 with DPAK-Package (55V, **42A**, 27mR and 48nC Gate-Charge).

New circuit Files for DIY-purpose:
----------------------------------
- Schematic and Board files are layouted with Eagle v7.2 Layout-Program,  
- The .png file contains both layout-files as grafical preview: schematic and layout,  
- BOM = Bill o Materials (in html-format) 
- .xln and .dri files contain the drill points for the Board in Excellon-33mm format  
- .nc = gcode drill-file, used to CNC-drill the board out of a blank 2-layer 1,5mm-35my-Cu-Epoxi-Board with ø0,6mm drill,  
   cutting outlines (manually) with about +2mm space to the board-dimension-edges, f.ex. with a fine Proxxon diamond-blade (ø50mm).  
- **TonerDirect:** .pdf file (DIN-A4) for DIY-Board-etching, to transfer both layers per Toner-Transfer-Paper to the drilled (blank) board:  
   using the 4 edge-drills to align the transfer-paper over the predrilled board with 4x ø0,5mm needles / 0,5mm-Cu-wire + adhesive-tape,
   transferring the layout with a modified Laminator @ ~180-200°C with 3 passes to the board. (="Direct Toner-Transfer" method),
   then etching the board with Sodium Persulfate @ ~80°C (5 minutes, if shaking and lifting the board steadily).
   Now straighten edges with the diamond-blade to dimension-outlines. Finally tinning both sides with Solder-paste + flat 80W-soldering-iron
   @ ~360°C + Copper-braid, and insert short strings (~3mm) of head-bent ø0,5mm-Cu-wire in vias, as shown in fotos.

Function of the circuit:
------------------------
Target: At 25°C (room-temperature) the fan shall rotate with 50% PWM-width, increasing with higher ambient-temperature and going up to 100% PWM (=DC) at 35°C +above.

Problem:
--------
Normal NTC-Thermistors are not changing their resistance enough within a small temperature-range (as postulated above with only 10°C difference) to reach a bandwidth changing from 50% PWM-width to 100% (=DC), although the lookup-table of a 100k NTC-3950-Thermistor shows the variation of its resistance in this temperature-range varying from about 100kOhm to 65kOhm, so -at a first glance- this seems to be near enough to the desired PWM-range...

On the other side PWM-circuits with a "Two-Path" Schmitt-Trigger-Feedback uses mostly the Negative-Feedback Line from ST-Output to process the Input, divided with 2 Diodes and going through 2 Resistors, to create the desired two different (+-)PWM-Pulses of a full PWM-Wave. So we have TWO resistors, and each is forming ONE of the two PWM Rectangle-Pulses (+ OR -). So the NTC-Resistor is determining the time-length of ONLY ONE Pulse-rectangle of the PWM-Wave. Our resulting PWM-percentage therefore is determined by the relation of the NTC-resistor to THE SUM OF BOTH  (X per/cent).  
For example if we have 25°C ambient-temperature and the NTC therefore has 100k resistance to form the upper Time-Rectangle, this upper PWM-time should be 50% of one complete PWM-Wave length. Therefore we need the same Resistor-Value (=100k) for the lower PWM-Pulse to get the desired 50% PWM-time. Now the sum of both resitors is 200k.

Calculation of Pulse-Width Percentage (presumed NTC-temperature at 25°C):
-------------------------------------

(100k / 200k) * 100% = 50%.

If the (ambient-)temperature changes to 35°C, the NTC-resistance changes from 100k to 65k, and the sum of both (upper and lower Resistor) = 165k, so the PWM-percentage results as:

(65k / 165k) * 100% = 40%  (=shorter than before?)

Yes, shorter! Because its only the calculated "lower" 40%-Pulse-Width/length on the ST-INPUT. The ST inverts this pulse on his OUTPUT, with same time-length, but as a HIGH Output-"Off"-Pulse (on P-Mosfet Q1), so for the "On"-part of the PWM we have to take the complementary LOW (Gate)-"On-Time"-length of the ST-Output - which appears at same time as HIGH-Output on the Mosfet-Stage-Output, this beeing the "active", width- or length-changing part of our measured, increasing NTC-Temperature (=decreasing NTC-Resistance!). So to calculate this length we substract resulted 40% from the total of 100%-Pulse-width, getting the 60% "active" (high)-Pulse-time-length, provided to the fan.  
So the new (+)Pulse-Width has changed from 50% to 60% of total length. But this is only a small change of 10% of the complete (100%) PWM-Wave-time (length), but not the desired change from 50% to 100% PWM (to make continuous +DC-Voltage out of the initial 25°C / 50%-Pulse-Width)!

This "percentage"-problem also persists in other circuit-principles, because most Schmitt-Triggers have fixed built-in hysteresis-levels, mostly at *about* 1/3 and 2/3 of their Supply-Voltage. So also the often used "good old" NE555 (also beeing a "Schmitt-Trigger"!). But on the other hand no NTC can change from "100%" to "0" (to that within a small temperature-range as targeted) - to get a 50% to 100% PWM-bandwidth on a simple ST-Output...

But how do we solve this problem - getting out a 50% PWM ***VARIATION*** having only a small Temperature-Range on the Sensor?

Considered Solutions:
---------------------
1. increasing/transducing the NTC-Voltage-"Bandwidth" - f.ex. with an additional OP-Amplifier,  
2. using an OPAmp to design a Schmitt-Trigger with smaller hysteresis-limits as the common fixed ones,  
3. decreasing (fixed) Hysteresis of a generic Schmitt-Tigger - f.ex. with Resistors *),  
4. using a commercial IC, made to steer fans with a temperature-dependent PWM (f.ex. the TC648),  
5. using a uController, "mapping"(=calculating) measured NTC-voltages to defined (greater) PWM-output ranges,  
6. using other Temperature-Sensors...

*) I choosed the third way because of its simplicity, as demonstrated in the schematic, using a simple CMOS Schmitt-Trigger in conjunction with the inverting stage of the Output-Mosfet, used as "positive-feedback booster", this generating opposite signals to the ST-Output, which (virtually) decreases the original hysteresis-bandwidth.  
This "POSITIVE-feedback" Signal-Path now contains the NTC and the compensating "50%-low" timing-resistor - in opposite to the normally used NEGATIVE-feedback-path from ST, containing the two timing-resistors.
So both, R1 and R2, now used as positive-feedback-resistors, are acting in opposite direction to the ST-Output-Signal, this created in conjunction with R3, the "pilot"-timing-resistor and capacitor C3, so R1 and R2 are forming **two Voltage-Dividers** in conjunction with R3, one for each Pulse-direction.  
Both Voltage-Dividers are "adding" their opposite, substracting-current to the ST's Output-current (on R3), so the resulting new (virtual) threshold-levels are nearer together as the original fixed ones, getting (virtually) decreased hysteresis-limits.

**The trick hereby is the Voltage-Divider** between NTC R1 and the leading timing-resistor R3. R3 subtracts / eliminates the unneeded (virtually "persistent" or "inactive") part of NTCs total Resistance-Value, while the resting small "active", temperature-changing part of this NTC appears on the ST's Input as a WHOLE (voltage-) RANGE between "0" and "100", so the (fixed) ST-hysteresis traduces this range in corresponding PWM-time-percentages of 50% to 100%, this now beeing proportional to the given small input-temperature-range!  

Function:
---------
If the NTC is warming up, its resistance lessens down, so (through Diode D1) it pulls the middle of its Voltage-Divider R1/R3 with attached capacitor C3 high, but on the other side R3 pulls C3 a bit more down as NTC R1, because of its "stronger" (lower) resistance-value, so the resulting voltage on C3 anyhow goes slowly down, till reaching the lower ST-threshold-line. This threshold abruptly changes the status of the ST-OUTPUT from low to high and the Mosfet-Inverter (the Power-Stage) from high to low. So now the second Voltage-Divider with R2 and R3 becomes active (through Diode D2) - while Diode D1 is blocking this direction, so NTC is inactive: C3 is pulled slowly high till reaching the upper ST-threshold-level. There again both Outputs change their Status and the process begins from start, as described.  
So both directions are forming their own Output-Pulse length and Puls direction. The lenghts are formed with the time-difference resulting from each initial Voltage-Divider-LEVELs (=after each direction-change) in conjunction with C3 going slowly to its opposite ST-threshold-level.  
But in case that the NTC becomes hotter than 35°C, its Voltage-Divider-point (R1/R3) can't pull C3 anymore sufficiently down to the lower threshold-line, remainig above this line, so no Output changes: The ST-Output remains on "Zero" (DC-Voltage), the Mosfet-Output stays at +VCC and so the fan at "full-power" (as desird).  

Changing the (default) PWM-Frequency-Range with C3:
---------------------------------------------------
Of course PWM-Frequencies of this circuit, with two ADDED resistances (R1 and R2) substracting current to R3, are resulting in lower frequencies as without Volage-Divider, with only one resistor (R3) as leading time-resistor.  
But if needed, the resulting (default) "25°C"-Frequency may be adjusted, changing only the value of timing-capacitor C3. With higher C3-capacity, frequency goes lower and viceversa.  
I choosed a low 40-to-0Hz-range, because high PWM-frequency ranges (above 20kHz and more) may cause severe EMV-Problems in conjunction with motor-windings...

Changing the Temperature-Range:
-------------------------------
If the **temperature**-range (here choosen between 25°C and 35°C) results as too small, the virtually "persistent" resistance-part of our NTC may be increased, so f.ex. adding a 10k-resistor (as outlined, here R5 in schematics)...  
**But Warning**: the resulting Signal-Amplification of this circuit is relatively high (as calculated above), so values for R3, together with R2 and  
NTC R1 + the 2 direction-Diodes (with its 0,7V threshold voltage) are relatively critical, considering ST's threshold-lines in correlation to our 
two R-Dividers "virtual thresholds" (mid-point voltages) on C3. If one of both ST-thresholds are not reached anymore, PWM is going down to either 0V or VCC.
