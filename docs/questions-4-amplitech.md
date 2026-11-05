# Questions for AmpliTech — RFP AGMDC0011

Requirement numbers refer to the SOW outline.

## Q1 - SOW revision

Is this SOW Rev 01 or Rev 02? If it is Rev 02, what changed from Rev 01?

**Background:** The file we received is named `Union-software-SOW_Rev02.docx`, but every page footer reads "AmpliTech Proprietary | RFP: AGMDC0011 | Rev 01."

**References:** SOW page footer.

## Q2 - Union

Is "Union" the name of the project, or a separate company we will be working with?

**Background:** "Union" appears only in the SOW title and in the first sentence of the Project Overview ("The Union Embedded Software Development Project consists of…"). Every other party reference in the SOW is to AmpliTech, the end customer, or the vendor.

**References:** SOW title; Project Overview and Objectives.

## Q3 - Decision timeline

When will AmpliTech make choices indicated in the requirements?

**Background:** The SOW requires end-customer approval of the REST authentication scheme, the role scheme, and the factory-reset access privileges, and allows end-customer-approved digital signatures for firmware updates.

**References:** SOW 1.2.2.4, 1.2.2.6, 1.3.3.4, 1.6.4.

## Q4 - Processor part number

Is the processor the XMC7200D-E272K8384AA, and is it the final choice or still a candidate?

**Background:** Hardware and Target Environment names the "XMC7200-E272K8384AA." The Infineon link in the References section is for the XMC7200D-E272K8384AA, with a "D." The SOW also says alternate processors can be considered.

**References:** SOW Hardware and Target Environment; SOW References.

## Q5 - Hardware documents

When will AmpliTech provide the processor board schematic, the processor pin assignments, and the bill of materials?

**Background:** The Scope of Work includes a schematic review of the processor pin-out. The firmware drivers for SPI, GPIO, and the ADC also depend on these documents.

**References:** SOW Scope of Work; SOW 2.1 through 2.10.

## Q6 - Ethernet PHY connections

Does the target board connect the Ethernet PHY to the processor the same way as the KIT_XMC72_EVK? If not, please describe the differences in the data interface, the processor pins used, the PHY reset line, and the MDIO/MDC management lines.

**Background:** We will develop the Ethernet firmware on the KIT_XMC72_EVK first. On that kit, the XMC7200's ETH1 port connects to the PHY in RGMII mode, using pins P26[0] through P27[4] for data and clocks, and P27[5] and P27[6] for MDIO and MDC.

**References:** SOW Hardware and Target Environment; SOW 2.1.1; KIT_XMC72_EVK user guide (002-34389), Ethernet transceiver and RJ45 connector section.

## Q7 - RF boards 3 and 4

We read the SOW to mean the first release has two RF boards, and that a later processor board revision will add boards 3 and 4. We plan to define the processor pins for boards 3 and 4 now, so the same firmware runs on both board revisions. Is that correct? If so, will the rev 1 processor board leave those pins unused?

**Background:** Each RF board uses 1 status input, 6 attenuator outputs, 3 ADC inputs, and SPI access to an ADF4368 and an ADAR2001.

**References:** SOW Scope of Work; SOW 2.2.1, 2.6.2, 2.8.1, 2.10.1, 2.10.4; SOW Figures 1 and 2.

## Q8a - RF board status pin circuit

Is the weak pull-up on each RF board status pin (2.2.3) the processor's internal pull-up, or a resistor on the processor board? Is the RF board's status output open-drain, or does it actively drive the line high?

**Background:** If the pull-up is the processor's internal one and the status output actively drives high, the firmware can detect whether each board is installed by briefly switching the pin to a pull-down at startup. An absent board would read low and an installed board would read high. If the output is open-drain, both would read low.

**References:** SOW 2.2.1, 2.2.2, 2.2.3.

## Q8b - Expected number of RF boards

How should the firmware know how many RF boards an LNB carries, so that an installed board that has failed is reported as a fault rather than as not installed?

**Background:** We also plan to check each board at startup by reading its ADF4368 identity registers over SPI. On its own, that check cannot tell an empty slot from an installed board that does not answer.

**References:** SOW 2.2.1, 2.5.1.

## Q9 - LT80603 regulators

Which LT80603 part number is used, and which of its pins provides the status signal to the processor?

**Background:** The SOW lists one status signal from each LT80603, up to 12, as open drain and high for normal operation. The LT80603 is the only named part without a data sheet link in the References section.

**References:** SOW 2.3.1; Analog Devices LT80602/LT80603 data sheet.

## Q10 - PoE controller variant

Is the MAX5969A the intended part, rather than the MAX5969B?

**Background:** The SOW References cite IEEE 802.3at. The MAX5969A/MAX5969B data sheet lists a "legacy UVLO at 36V (MAX5969A)" and an "IEEE 802.3af/at-compliant, 40V UVLO (MAX5969B)." UVLO (undervoltage lockout) is the input voltage below which the controller keeps the board turned off.

**References:** SOW 2.3.2; SOW References; Maxim MAX5969A/MAX5969B data sheet (19-5008).

## Q11a - PoE status signal

Which MAX5969A output is the status signal in 2.3.2: PG (power good) or 2EC (Type 2 PSE detection)?

**Background:** The MAX5969A has two open-drain status outputs. PG holds the board's regulators off until PoE power is good, so the processor cannot be running to read PG low. 2EC goes low when the LNB is powered by a Type 2 (802.3at) PSE, which is a normal condition rather than a fault.

**References:** SOW 2.3.2; Maxim MAX5969A/MAX5969B data sheet (19-5008).

## Q11b - LNB power draw

Will the LNB draw more than 12.95 W? If so, what should the firmware do when it is powered by a Type 1 PSE?

**Background:** Under IEEE 802.3at, a powered device must stay below 12.95 W until it recognizes a Type 2 PSE. The MAX5969A's 2EC output reports that recognition.

**References:** SOW 2.3.2; SOW References (IEEE 802.3at-2009); Maxim MAX5969A/MAX5969B data sheet (19-5008).

## Q11c - Power sources

Is PoE the LNB's only power source, or does the board also have an external DC input?

**Background:** Figures 1 and 2 show power arriving only through the RJ45 and the PoE controller. The MAX5969A also supports a wall-adapter input, which would let the processor stay powered when PoE fails.

**References:** SOW Figures 1 and 2; Maxim MAX5969A/MAX5969B data sheet (19-5008).

## Q12 - 1000BASE-T or 100BASE-T

Does the end customer require 1000BASE-T, or may 100BASE-T be used? When will that decision be made?

**Background:** Requirement 1.1 specifies a single 1000BASE-T interface. Hardware and Target Environment says 100BASE-T is being considered to simplify both hardware and software, and asks vendors for a cost and schedule comparison.

**References:** SOW 1.1; SOW Hardware and Target Environment.

## Q13 - REST and SNMPv3 interfaces

Is the requirement that the firmware implement just one M&C protocol, either REST or SNMPv3? If so, which one? Or must it implement both, and allow AmpliTech to configure each LNB to use just one of them?

**Background:** Requirement 1.2.1 says the LNB "shall implement M&C functions using either a RESTful or Simple Network Management Protocol (SNMP)." Requirements 1.2.2 and 1.2.3 then set the minimum specifications for "Sellers implementing" each one. The SOW does not say whether both interfaces must be available on a single unit.

**References:** SOW 1.2.1, 1.2.2, 1.2.3.

## Q14 - Existing interface specification

Must the M&C interface match an existing end-customer interface specification or MIB, or will we define the interface in the Interface Control Document?

**Background:** Requirement 1.5.1.1 refers to an "external M&C master." OpenAPI 3.1.1 defines how a REST interface is described, not its content, so the end customer's system must be built against whichever interface is used. For SNMPv3, the end customer's manager must support SHA-512 authentication (1.2.3.4).

**References:** SOW 1.2.2.1, 1.2.3.4, 1.5.1.1; SOW Deliverables (SDRL 02, SDRL 04).

## Q15 - Interface definition files

For REST, should we deliver an OpenAPI 3.1.1 definition file as part of the Interface Control Document? For SNMP, will AmpliTech provide the enterprise identifier (OID) under which the LNB's MIB objects are defined?

**Background:** Requirement 1.2.2.1 requires REST to comply with OpenAPI 3.1.1. An SNMP MIB needs an enterprise identifier for its objects. SDRL 02 and SDRL 04 call for the Interface Control Document.

**References:** SOW 1.2.2.1, 1.2.3; SOW Deliverables (SDRL 02, SDRL 04).

## Q16 - The HMI

What is the "HMI" in 1.4.1.7? Is it an end-customer system, or are we expected to provide a web page or application?

**Background:** Requirement 1.4.1.7 says the event log "shall be operator downloadable via the HMI." No other section defines an HMI.

**References:** SOW 1.4.1.7.

## ~~Q17 - Dropped~~

## ~~Q18 - Dropped~~

## Q19 - IP address settings

Should the IP address be set statically, by DHCP, or both? What is the factory-default address? Is IPv6 required?

**Background:** Requirement 1.3.1.5 says only "Set IP address: Included."

**References:** SOW 1.3.1.5.

## Q20 - Factory-reset address

What aliased static IP address should the factory-reset interface use, and must it be active at all times?

**Background:** Requirement 1.3.3.1 calls for "an aliased static IP address" but does not give the address or say when it is available.

**References:** SOW 1.3.3.1, 1.3.3.3.

## Q21 - What a factory reset clears

We plan for a factory reset to clear network settings, user accounts, roles, and credentials, and to keep calibration data, the model number, serial number, hardware revision, operating hours, and the event log. Is that correct?

**Background:** Requirement 1.3.3 defines how a factory reset is requested, but not what it clears.

**References:** SOW 1.3.3, 1.3.1.12, 1.4, 1.7, 2.8.4, 2.9.5.

## ~~Q22 - Factory-reset TLS version

~~We plan to use TLS 1.3 for the factory-reset connection, the same version 1.2.2.3 requires for REST. Is that acceptable?

~~**Background:** Requirement 1.3.3.2 says the factory-reset connection "shall utilize TLS" but does not give a version.

~~**References:** SOW 1.2.2.3, 1.3.3.2.

## Q23 - User roles

What user roles are needed, and what may each role do? Are predefined roles acceptable, or must users be able to define their own?

**Background:** Requirement 1.2.2.6 allows either and requires end-customer approval of the role scheme.

**References:** SOW 1.2.2.5, 1.2.2.6, 1.3.3.4.

## Q24 - REST authentication

Which authentication method will the end customer approve for the REST interface, for example user name and password, or client certificates?

**Background:** Requirement 1.2.2.4 requires "an authentication scheme approved by end customer" but does not name one.

**References:** SOW 1.2.2.4.

## Q25 - SNMP access groups

Does "a minimum of 15 groups" mean the LNB must support at least 15 access groups that users can configure, or that it must ship with 15 groups already defined?

**Background:** VACM (View-based Access Control Model) is the SNMPv3 mechanism that controls which users can read or change which values. Requirement 1.2.3.6 sets the minimum group count.

**References:** SOW 1.2.3.5, 1.2.3.6.

## Q26 - SNMPv3 authentication protocol

Please confirm that the SHA-512 authentication in 1.2.3.4 means usmHMAC384SHA512AuthProtocol as defined in RFC 7860.

**Background:** The only SHA-512 authentication protocol defined for SNMPv3 is usmHMAC384SHA512AuthProtocol. The SOW's References list RFC 7630, which has been replaced by RFC 7860.

**References:** SOW 1.2.3.4; SOW References; RFC 7630; RFC 7860.

## Q27 - Certificates and credentials

Who creates and installs the TLS certificates and initial login credentials on each LNB, and how are they replaced during the unit's life?

**Background:** The SOW requires TLS for REST and for factory reset, and authenticated SNMP users, but does not say how keys and credentials are provided.

**References:** SOW 1.2.2.3, 1.2.3.2, 1.3.3.2.

## Q28 - Security policies

Are there end-customer rules for passwords, failed logins, or session time-outs that the firmware must enforce?

**Background:** The SOW requires authentication and access control but states no account policies.

**References:** SOW 1.2.2.4, 1.2.2.5, 1.2.3.

## Q29 - Operating hours

We read "unmuted operating hours" in 1.3.1.12 as the total hours the LNB has been powered and operating. Is that correct, or is there a mute function we should implement?

**Background:** No other section of the SOW defines a mute function.

**References:** SOW 1.3.1.12.

## Q30 - Attenuation command range

We plan to accept attenuation commands of 0 to 20 dB in 1 dB steps and reject any other value. Is that correct? Should "read gain" report the last commanded value?

**Background:** Requirement 1.3.1.6.2 limits the range to 0 to 20 dB in 1 dB steps. The HMC425A truth table in 2.6.7 supports 0 to 31.5 dB in 0.5 dB steps.

**References:** SOW 1.3.1.6, 1.3.1.7, 2.6.5, 2.6.7.

## Q31 - Attenuation after restart

Requirement 2.6.4 sets every IF attenuator to 0 dB at power-up and reset. Should the firmware then restore the last commanded setting, or stay at 0 dB until an operator sets it again?

**Background:** If the setting is not restored, a reboot or power loss changes the gain of every channel.

**References:** SOW 1.3.1.6, 1.3.1.8, 2.6.4.

## Q32 - Command response time

What is the longest acceptable response time for an M&C command, and how many management clients may be connected at the same time?

**Background:** The SOW does not set response-time or connection limits.

**References:** SOW 1.3.

## Q33 - Green LED condition

Should the LED turn green only when all status pins are healthy, as 2.4.2.1 states, or also wait until SPI programming and the reference attenuator adjustment are complete?

**Background:** The reference attenuator adjustment starts 5 ±1 seconds after power-up, so the LED could turn green before it finishes.

**References:** SOW 2.4.1.1, 2.4.2.1, 2.5.1.2, 2.5.2.2, 2.7.5.1.

## Q34 - Fault present at power-up

If a status pin shows a fault at power-up, should the LED turn red and the Summary Alarm be set?

**Background:** As written, the LED stays yellow, because the red Fault state is entered only from the Normal state (2.4.3.1). The Summary Alarm is likewise triggered only "from normal operating state" (2.12.1.1).

**References:** SOW 2.4.1.1, 2.4.3.1, 2.12.1.1.

## Q35 - Action on faults

When a fault is detected, should the firmware only report it, or also take action, such as disabling the affected RF board?

**Background:** The SOW defines how faults are reported, not how the LNB responds to them.

**References:** SOW 2.2, 2.3, 2.4, 2.12.

## Q36 - Summary Alarm sources

Does the Summary Alarm cover only status-pin faults, as 2.12.1.1 states, or also temperature alarms and SPI failures?

**Background:** Requirement 2.12.1.1 triggers the Summary Alarm "when any status input pin goes to fault state."

**References:** SOW 1.3.1.1, 2.12.1.1.

## Q37 - Clearing warnings and alarms

Do warnings and alarms clear on their own when the condition ends, or stay set until an operator sends the reset command in 1.3.1.9?

**Background:** The SOW provides a reset command but does not say whether warnings and alarms stay set.

**References:** SOW 1.3.1.9, 1.3.1.10, 1.3.1.11, 2.11, 2.12.

## Q38 - Communication fault

We read "Communication fault: No requirement" in 1.3.1.3 to mean no communication-fault alarm is required. Is that correct?

**References:** SOW 1.3.1.3.

## Q39 - Temperature sensor values

We plan to convert temperature readings using the MCP9700A data sheet values: 500 mV at 0 °C and 10.0 mV/°C. Is the MCP9700A the sensor used on the processor board and RF boards, and does any board circuitry change its output?

**Background:** Requirement 2.10.3.3 gives 400 mV at 0 °C. That is the MCP9701A's value; the MCP9701A's slope is 19.5 mV/°C, not the 10.0 mV/°C in 2.10.3.4.

**References:** SOW 2.10.3.3, 2.10.3.4; Microchip MCP9700/9700A/9701/9701A data sheet, DS20001942.

## ~~Q40 - Dropped~~

## ~~Q41 - Dropped~~

## ~~Q42 - Dropped~~

## ~~Q43 - Dropped~~

## Q44 - Temperature warning and alarm hysteresis

We propose the following for each temperature sensor. Is this acceptable?

- The Temperature Warning sets when a sensor reads above 105 °C for 2 consecutive readings, and clears when it reads below 100 °C for 2 consecutive readings.
- The Over Temperature Alarm sets when a sensor reads above 125 °C for 2 consecutive readings, and clears when it reads below 120 °C for 2 consecutive readings.
- A reading at or near 0 V, or at the ADC's full-scale value, is reported as a sensor fault rather than as a temperature.

**Background:** Requirements 2.11.1.1 and 2.12.2.1 give trip points but no clearing points, so a reading near 105 °C or 125 °C could switch the warning or alarm on and off repeatedly. At 4 measurements per second, 2 consecutive readings take 0.5 seconds.

**References:** SOW 1.3.2.4, 2.10, 2.11.1.1, 2.12.2.1.

## Q45 - Reference attenuator failure

If the 100 MHz reference attenuator cannot reach -1 dBm ±1 dB, how many adjustment attempts should the firmware make, and what should it report? Does the 1000 ms wait in 2.7.5.4 apply after every adjustment step?

**Background:** Requirement 2.7.5.4 says "If not re-adjust attenuator" but sets no limit on attempts.

**References:** SOW 2.7.5.

## Q46 - Watchdog and recovery

Should the firmware use a watchdog to restart itself if it stops responding, and should it report the cause of each restart?

**Background:** The SOW requires remote reboot and rollback after a failed update, but no other recovery behavior.

**References:** SOW 1.3.1.8, 1.6.5.

## Q47 - Power measurement accuracy

Does the 2.0 dB uncertainty in 1.3.2.2 include the coupler, ADC, and calibration-equipment errors? Over what frequency and temperature range must it hold?

**Background:** The SOW gives the uncertainty limit without conditions.

**References:** SOW 1.3.2.2, 2.8.4, 2.8.6.

## ~~Q48 - Dropped~~

## Q49 - Measurement rate and averaging

Does the rate of at least 4 measurements per second in 1.3.2.4 apply to each IF channel, and to the reference power and temperature readings? Must the firmware average readings over time, beyond the RMS averaging in the ADL5906, and if so, how quickly must a reading follow a real change in power?

**Background:** Requirement 1.3.2.4 appears under the IF power values. With four RF boards there are 14 analog readings: 4 IF power, 1 reference power, 5 MCP9700A temperatures, and 4 ADL5906 temperatures. The SOW does not mention filtering or averaging of readings over time.

**References:** SOW 1.3.2.4, 2.8.1, 2.9.1, 2.10.1, 2.10.2, 2.10.4; Analog Devices ADL5906 data sheet.

## Q50 - IF calibration points

Must each IF detector be calibrated at every combination of the three power levels, three frequencies, and three temperatures, which is 27 points per detector? What modulated signal should be used?

**Background:** Requirement 2.8.4.1 lists three of each and calls for a "modulated IF signal" without naming one.

**References:** SOW 2.8.4.1, 2.8.4.1.2.1.

## Q51 - Temperature used for calibration

We plan to use each RF board's ADL5906 temperature output to select the calibration values for the temperature interpolation in 2.8.5, since it measures the detector itself. Is that correct, or should the RF board's MCP9700A be used?

**Background:** The ADL5906 data sheet describes using its temperature output to correct the detector's temperature drift.

**References:** SOW 2.8.5, 2.10.1, 2.10.4; Analog Devices ADL5906 data sheet.

## Q52 - ADL5906 slope

Is the ADL5906 output scaled on the RF board?

**Background:** Requirement 2.8.4.1.4 gives a typical slope of 42 to 52 mV/dB. The ADL5906 data sheet gives a typical slope of 55 mV/dB.

**References:** SOW 2.8.4.1.4; Analog Devices ADL5906 data sheet.

## Q53 - 20 dB coupler

We read 2.8.6 to mean the calibration in 2.8.4 already relates the detector voltage to the actual IF output power, so no separate 20 dB correction is added. Is that correct?

**Background:** Requirement 2.8.6 is marked "for reference only." Calibration in 2.8.4.1 is defined in terms of IF output power levels.

**References:** SOW 2.8.4.1, 2.8.6.

## Q54 - Reference calibration path

What is the net gain of the LNA and filter between the 100 MHz reference input and the ADL5904? Our reading is about +8 dB.

**Background:** The calibration inputs of -18 and +10 dBm map to -22 and +6 dBm at the detector, a net change of -4 dB. With the attenuator at 12 dB, the rest of the path must add about +8 dB. Figure 2 shows an LNA and a filter in that path.

**References:** SOW 2.9.5.1; SOW Figure 2.

## Q55 - ADL5904 threshold pins

Are the ADL5904's threshold-detection pins (Q, QB, and RST) connected to the processor?

**Background:** The SOW uses only the ADL5904's RMS output. The part also has a comparator that latches when the signal exceeds a set level and holds until a reset pulse is applied to RST.

**References:** SOW 2.9; Analog Devices ADL5904 data sheet.

## ~~Q56 - Dropped~~

## Q57a - Register values for RF boards 1 and 2

When will AmpliTech provide Appendices A and B, the ADF4368 and ADAR2001 register values for RF boards 1 and 2?

**Background:** Both appendices are TBD. The firmware writes these values at power-up and after reset, and the RF boards cannot be brought up without them.

**References:** SOW 2.5.1.2, 2.5.1.6, 2.5.2.2, 2.5.2.6; SOW Appendices A and B.

## Q57b - Register values for RF boards 3 and 4

The firmware will have table space for the register values of RF boards 3 and 4, but under 2.5.1.5 and 2.5.2.5 those values must be defined in the firmware and cannot be set over Ethernet. We propose adding the boards 3 and 4 values to the firmware source when those boards exist, and releasing them as a firmware update. Is that acceptable? Will boards 3 and 4 use the same LO frequencies as boards 1 and 2?

**Background:** Appendices A and B describe values only for RF boards 1 and 2. Each board's ADF4368 frequency settings and ADAR2001 band and filter settings depend on its LO frequency.

**References:** SOW 2.5.1.4, 2.5.1.5, 2.5.1.6, 2.5.2.4, 2.5.2.5, 2.5.2.6; SOW Appendices A and B; SOW Figure 1.

## Q58 - Read-only and write-only registers

Should "write and read all registers" in 2.5.1.3 and 2.5.2.3 apply only to registers that support both operations?

**Background:** Some device registers can be read but not written, or written but not read back.

**References:** SOW 2.5.1.3, 2.5.2.3.

## Q59 - ADAR2001 temperature sensor

Should the firmware read and report the ADAR2001's on-chip temperature sensor?

**Background:** The ADAR2001 data sheet shows an on-chip temperature sensor. The SOW does not mention it.

**References:** SOW 2.5.2, 2.10; Analog Devices ADAR2001 data sheet.

## ~~Q60 - Dropped~~

## Q61 - Event record contents

What information must each state record contain? We read "prior to" a spontaneous fault (1.4.1.4) as the most recent entry from the continual log described in 1.4.1. Is that correct?

**Background:** Requirement 1.4.1 describes continual logging that is saved permanently when the state changes. A record taken before an unpredictable fault can only come from that continual log.

**References:** SOW 1.4.1, 1.4.1.1 through 1.4.1.4.

## Q62 - Event log size

How many records, or how many days of history, must the event log keep? What should happen when storage is full? Is the RFC 5424 format required, or preferred?

**Background:** Requirement 1.4.1.5 says the log "should" follow RFC 5424. No retention size is given.

**References:** SOW 1.4.1, 1.4.1.5.

## Q63 - Time without NTP

How long must the LNB keep time within 1 second if the NTP server is unavailable? Is NTP version 3 acceptable for delivery, or must we implement version 4?

**Background:** Requirement 1.5.1 sets NTP version 4 as the objective and version 3 as the threshold, with time error of 1 second or less.

**References:** SOW 1.5.1.3, 1.5.1.4, 1.5.1.5, 1.5.1.6.

## Q64 - UART use

Is the UART used only during factory test, or must it stay active in production units?

**Background:** Requirement 2.13.1 says the UART is "to be used during factory test."

**References:** SOW 2.13.

## Q65 - SOW cross-references

Please confirm two corrections we made in our copy of the SOW, and answer one question:

1. The references to "section 2.12.3 for more information on the UART requirements" should point to 2.13.
2. The reference in 2.12.1.1 to "sections 2.1 and 2.3" should be 2.2 and 2.3.
3. 2.9.2 and 2.9.3 have the same title. Is something missing from 2.9.2?

**Background:** Requirement 2.12.3 refers to itself; the UART requirements are in 2.13. Section 2.1 is the Ethernet PHY; the status inputs are in 2.2 and 2.3. Requirements 2.9.2 and 2.9.3 are both titled "ADL5904 power detector analog voltage output," and only 2.9.3 has sub-items.

**References:** SOW 2.2.4, 2.3.3, 2.4.4, 2.5.1.7, 2.5.2.7, 2.6.8, 2.7.7, 2.8.7, 2.9.2, 2.9.3, 2.9.4, 2.9.8, 2.10.6, 2.11.2, 2.12.1.1, 2.12.3, 2.13.

## Q66 - Fleet updates

We read 1.6.2 to mean the LNB must accept an unattended firmware update through its M&C interface, so the end customer can script updates across all units with standard tools. We do not plan to deliver a fleet-management tool. Is that correct?

**Background:** Requirement 1.6.2 says the facility "is intended as the ability to perform an unattended remote update," and 1.6.3 says updates must not require "special or Seller specific tools."

**References:** SOW 1.6.1, 1.6.2, 1.6.3.

## ~~Q67 - Dropped

## ~~Q68 - Dropped

## ~~Q69 - Dropped

## Q70 - Data kept across updates

Which settings and data must survive a firmware update and a rollback?

**Background:** Calibration data, identity values, operating hours, user accounts, network settings, and the event log are all stored on the unit.

**References:** SOW 1.3.1.12, 1.4, 1.6, 1.7, 2.8.4, 2.9.5.

## ~~Q71 - Dropped~~

## Q72 - Test equipment

Which test equipment and fixtures will AmpliTech provide, and which must we supply? For example: RF signal sources, power meters, a thermal chamber, and a PoE switch.

**Background:** The SOW requires automated tests covering all requirements, including calibration at -40, +23, and +55 °C.

**References:** SOW Test; SOW 2.8.4.1.3.

## ~~Q73 - Dropped~~

## ~~Q74 - Dropped~~

## ~~Q75 - Dropped~~

## Q76 - Schedule calendar

Which holidays does the schedule exclude?

**Background:** The 60-day board-delivery task runs from November 9, 2026 to February 11, 2027, which spans 69 weekdays. The schedule therefore appears to exclude 9 weekdays.

**References:** SOW Tentative Schedule.

## Q77 - Final documents at Demo 1

May SDRL 03 and SDRL 04 be revised after testing on the target boards?

**Background:** SDRL 03 (Final Software Functional Description Document) and SDRL 04 (Final Software Interface Control Document) are due at Progress Demonstration 1, before the target boards arrive.

**References:** SOW Deliverables; SOW Milestones.

## ~~Q78 - Dropped

## Q79 - Commercial software

May we use commercially licensed software, such as a TLS or SNMP library, if its license cost is included in our price?

**Background:** The SOW does not allow GPL or AGPL code without written consent. The open-source SNMP agents we have reviewed either lack SHA-512 authentication or are licensed under the GPL.

**References:** SOW Contract; SOW 1.2.3.4.

## Q80 - Software inventory format

Is a list of third-party components with versions and license types sufficient, or is a specific SBOM (Software Bill of Materials) format required?

**Background:** The Contract section asks for "an inventory list of all pre-existing or third-party code used in the project including exact version numbers and license types."

**References:** SOW Contract.
