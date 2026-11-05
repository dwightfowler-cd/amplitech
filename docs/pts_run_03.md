Identifiers with a letter prefix are CD-assigned; they do not appear in the SOW.

# Primary Technical Specification — Run 03

Source: `union-sow_rev02.3.md`

Date: 2026-11-04

Status: Run 03 — extracted baseline; open items unresolved.

## Source notes

CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

CD note: Open item: Confirm this supplied source annotation: In 2.12.1.1, "sections 2.1 and 2.3" was corrected to "sections 2.2 and 2.3".

CD note: Open item: Confirm this supplied source annotation: The References entry "Open API Specification v3.1.1" is not in the Word file. Requirement 1.2.2.1 is the only place the Word file names it.

CD note: Open item: Confirm this supplied source annotation: The Word file's footer reads "AmpliTech Proprietary | RFP: AGMDC0011 | Rev 01" even though the filename says Rev02.

## Unnumbered SOW content

### Project Overview and Objectives

OV-1 The Union Embedded Software Development Project consists of software architecture, design, and implementation to support command and control of a Low Noise Block (LNB) Downconverter for use in satellite communications ground stations.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: none

OV-2 The command and control interface to the end customer is an Ethernet interface.

- Type: Firmware – driver/hardware interface
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: none stated
- Verification: Test
- Open items: none

OV-3 The command and control interface to internal components is SPI.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Test
- Open items: none

OV-4 There are a number of GPIO inputs and outputs to/from the processor.

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

OV-5 There are a number of analog inputs to the processor.

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: none

OV-6 There is a UART output for board level test.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: none stated
- Verification: Test
- Open items: none

### Hardware and Target Environment

HW-1 The candidate processor is Infineon XMC7200-E272K8384AA.

- Type: Hardware support
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Analysis
- Open items: Which processor is selected, given "candidate processor" and "Alternate processors can be considered"?

HW-2 The candidate Ethernet PHY is Texas Instruments DP83869HMRGZR.

- Type: Hardware support
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Analysis
- Open items: Which PHY is selected, given "candidate Ethernet PHY" and "100 Base-T is also being considered"?

HW-3 The candidate POE controller is Analog Devices MAX5969A.

- Type: Hardware support
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Analysis
- Open items: Is the "candidate POE controller" the final selection required by 2.3.2?

HW-4 Alternate processors can be considered if all requirements defined in this document are met.

- Type: Hardware support
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Analysis
- Open items: Who approves "Alternate processors" and when must selection be finalized?

HW-5 Requirement 1.1 specifies 1000 Base-T.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: Does "Requirement 1.1 specifies 1000 Base-T" remain the baseline while HW-6 considers 100 Base-T?

HW-6 100 Base-T is also being considered to simplify both hardware and software requirements.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: Is "100 Base-T is also being considered" an alternative to the "Single 1000 Base-T" requirement in 1.1?

HW-7 Vendors are requested to provide a high level description of cost and schedule differences for 1000 Base-T vs 100 Base-T.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: When is the requested "high level description of cost and schedule differences" due?

### Scope of Work

SCOPE-1 Development of embedded software architecture

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: none

SCOPE-2 Definition of protocol for Ethernet command and control based on requirements in this document

- Type: Firmware – protocol
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1
- Verification: Inspection
- Open items: Which protocol is to be defined under "Definition of protocol", given the alternatives in 1.2.1?

SCOPE-3 Embedded software design and implementation

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: none stated
- Verification: Test
- Open items: none

SCOPE-4 Embedded software documentation

- Type: Heading

SCOPE-5 Software functional description document

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SCOPE-6 Interface control document for Ethernet interface command and control

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SCOPE-7 Test software design and implementation

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Test
- Open items: none

SCOPE-8 Embedded software test and requirements verification

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Test
- Open items: none

SCOPE-9 Support of AmpliTech engineers in hardware development. This includes schematic review of processor pin-out.

- Type: Hardware support
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Inspection
- Open items: none

SCOPE-10 Initial development will use manufacturer evaluation boards for components as needed

- Type: Hardware support
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Inspection
- Open items: Which "manufacturer evaluation boards" are needed and who supplies them?

SCOPE-11 The target application board(s) will be provided to the software developer as soon as possible, tentatively 10 weeks after the effective date of contract (EDC)

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: How does "tentatively 10 weeks" for target-board availability align with the "60 days" ending "Thu 2/11/27" in SCH-9?

SCOPE-12 Final test and requirements verification shall be completed using the target application board(s)

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Test
- Open items: none

### References

REF-1 IEEE 802.3-2018, IEEE Standard for Ethernet, 14 June 2018.

- Type: Documentation deliverable
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-2 IEEE 802.3at-2009, IEEE Standard for Information technology-- Local and metropolitan area networks-- Specific requirements-- Part 3: CSMA/CD Access Method and Physical Layer Specifications Amendment 3: Data Terminal Equipment (DTE) Power via the Media Dependent Interface (MDI) Enhancements, 11 September 2009.

- Type: Documentation deliverable
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-3 Open API Specification v3.1.1

- Type: Documentation deliverable
- Work package: WP-02 M&C protocol: REST option
- Depends on: none stated
- Verification: Inspection
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: The References entry "Open API Specification v3.1.1" is not in the Word file. Requirement 1.2.2.1 is the only place the Word file names it.

REF-4 RFC 1305, Network Time Protocol (Version 3) Specification, Implementation and Analysis, March 1992.

- Type: Documentation deliverable
- Work package: WP-12 Time keeping (NTP)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-5 RFC 3413, Simple Network Management Protocol (SNMP) Applications, December 2002.

- Type: Documentation deliverable
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-6 RFC 3414, User-based Security Model (USM) for version 3 of the Simple Network Management Protocol (SNMPv3), December 2002.

- Type: Documentation deliverable
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-7 RFC 3415, View-based Access Control Model (VACM) for the Simple Network Management Protocol (SNMP), December 2002.

- Type: Documentation deliverable
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-8 RFC 3826, The Advanced Encryption Standard (AES) Cipher Algorithm in the SNMP User-based Security Model, June 2004.

- Type: Documentation deliverable
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-9 RFC 5424, The Syslog Protocol, March 2009.

- Type: Documentation deliverable
- Work package: WP-11 Event recorder
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-10 RFC 5905, Network Time Protocol Version 4: Protocol and Algorithms Specification, June 2010.

- Type: Documentation deliverable
- Work package: WP-12 Time keeping (NTP)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-11 RFC 7630, HMAC-SHA-2 Authentication Protocols in the User-based Security Model (USM) for SNMPv3, October 2015.

- Type: Documentation deliverable
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-12 Datasheet and additional reference documents, Infineon XMC7200-E272K8384AA Processor <https://www.infineon.com/part/XMC7200D-E272K8384AA>

- Type: Documentation deliverable
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-13 Datasheet and additional reference documents, Texas Instruments DP83869HMRGZR Ethernet Phy <https://www.ti.com/product/DP83869HM/part-details/DP83869HMRGZR>

- Type: Documentation deliverable
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-14 Datasheet, Analog Devices MAX5969A POE controller <https://www.analog.com/en/products/max5969a.html>

- Type: Documentation deliverable
- Work package: WP-19 Hardware support
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-15 Datasheet, Analog Devices ADF4368 Synthesizer <https://www.analog.com/en/products/adf4368.html>

- Type: Documentation deliverable
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-16 Datasheet, Analog Devices ADAR2001 Multiplier <https://www.analog.com/en/products/adar2001.html>

- Type: Documentation deliverable
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-17 Datasheet, Analog Devices HMC425A Step Attenuator <https://www.analog.com/en/products/hmc425a.html>

- Type: Documentation deliverable
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-18 Datasheet, Analog Devices ADL5906 Detector <https://www.analog.com/en/products/adl5906.html>

- Type: Documentation deliverable
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-19 Datasheet, Analog Devices ADL5904 Detector <https://www.analog.com/en/products/adl5904.html>

- Type: Documentation deliverable
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-20 Datasheet, Analog Devices HMC472A Step Attenuator <https://www.analog.com/en/products/hmc472a.html>

- Type: Documentation deliverable
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Inspection
- Open items: none

REF-21 Datasheet, Microchip MCP9700A Temperature Sensor <https://www.microchip.com/en-us/product/mcp9700a>

- Type: Documentation deliverable
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Inspection
- Open items: none

## 1 Ethernet Interface Requirements

- Type: Heading

1.1 Physical electrical interface: Single 1000 Base-T Ethernet interface. For clarity, all management and operator functions shall use a single common Ethernet interface.

- Type: Firmware – driver/hardware interface
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: none stated
- Verification: Test
- Open items: Is "Single 1000 Base-T" the selected baseline, given "100 Base-T is also being considered" in HW-6?

### 1.2 Monitor and Control (M&C)

- Type: Heading

1.2.1 M&C protocol: The LNB shall implement M&C functions using either a RESTful or Simple Network Management Protocol (SNMP).

- Type: Firmware – protocol
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.1
- Verification: Test
- Open items: Which of "either a RESTful or Simple Network Management Protocol (SNMP)" is selected for implementation?

1.2.2 Restful: Sellers implementing a RESTful interface shall comply with the minimum specifications below.

- Type: Heading

1.2.2.1 Protocol: Restful protocols shall comply with Open API Specification v3.1.1.

- Type: Firmware – protocol
- Work package: WP-02 M&C protocol: REST option
- Depends on: 1.2.1
- Verification: Test
- Open items: What project-specific API definition is required by "comply with Open API Specification v3.1.1"?
- CD note: Open item: Confirm this supplied source annotation: The References entry "Open API Specification v3.1.1" is not in the Word file. Requirement 1.2.2.1 is the only place the Word file names it.

1.2.2.2 Bearer: The RESTful bearer shall be HyperText Transfer Protocol Secure (HTTPS).

- Type: Firmware – protocol
- Work package: WP-02 M&C protocol: REST option
- Depends on: 1.1
- Verification: Test
- Open items: none

1.2.2.3 Transport security: HTTPs shall use Transport Security Layer (TLS) version 1.3.

- Type: Firmware – protocol
- Work package: WP-02 M&C protocol: REST option
- Depends on: 1.2.2.2
- Verification: Test
- Open items: none

1.2.2.4 Authentication: The RESTful M&C interface shall implement an authentication scheme approved by end customer.

- Type: Firmware – protocol
- Work package: WP-02 M&C protocol: REST option
- Depends on: 1.2.1
- Verification: Test
- Open items: Which "authentication scheme approved by end customer" is required?

1.2.2.5 Access control (authorization): The RESTful M&C interface shall utilize Role Based Access Control (RBAC).

- Type: Firmware – protocol
- Work package: WP-02 M&C protocol: REST option
- Depends on: 1.2.2.4
- Verification: Test
- Open items: none

1.2.2.6 RBAC: M&C roles may be predefined, or user defined with granular user management. End customer must approved the Seller role scheme.

- Type: Firmware – protocol
- Work package: WP-02 M&C protocol: REST option
- Depends on: 1.2.2.5
- Verification: Test
- Open items: Which roles and permissions satisfy "End customer must approved the Seller role scheme"?

1.2.3 SNMP: Sellers implementing SNMP shall only implement SNMP version 3 with the minimum specifications outlined in below.

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.2.1
- Verification: Test
- Open items: none

1.2.3.1 M&C bearer: UDP over Ethernet.

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.1
- Verification: Test
- Open items: none

1.2.3.2 SNMP USM: Implemented/Included.

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.2.3
- Verification: Test
- Open items: none

1.2.3.3 SNMP USM Encryption: AES-128 Cipher Feedback Block (CFB).

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.2.3.2
- Verification: Test
- Open items: none

1.2.3.4 SNMP USM Authentication: SHA-512.

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.2.3.2
- Verification: Test
- Open items: none

1.2.3.5 SNMP VACM Implemented/Included.

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.2.3
- Verification: Test
- Open items: none

1.2.3.6 SNMP VACM Groups: A minimum of 15 groups shall be supported.

- Type: Firmware – protocol
- Work package: WP-03 M&C protocol: SNMPv3 option
- Depends on: 1.2.3.5
- Verification: Test
- Open items: What permissions and membership rules are required for the "minimum of 15 groups"?

### 1.3 M&C Reporting and Operations

- Type: Heading

1.3.1 M&C functions: The item shall include the operational M&C functions described below.

- Type: Heading

1.3.1.1 Summary alarm: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.12.1.1
- Verification: Test
- Open items: none

1.3.1.1.1 See section 2.12 for alarm criteria

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.12
- Verification: Inspection
- Open items: none

1.3.1.2 Over temperature alarm: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.12.2.1
- Verification: Test
- Open items: none

1.3.1.2.1 See section 2.12 for alarm criteria

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.12
- Verification: Inspection
- Open items: none

1.3.1.3 Communication fault: No requirement.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: none stated
- Verification: Inspection
- Open items: none

1.3.1.4 Critical internal temperatures: The LNB shall monitor and report via the M&C critical internal temperatures.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.10
- Verification: Test
- Open items: Which temperatures are "critical internal temperatures", given the sensor sets in 2.10?

1.3.1.4.1 See section 2.10 for information on temperature sensors

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.10
- Verification: Inspection
- Open items: none

1.3.1.5 Set IP address: Included.

- Type: Firmware – driver/hardware interface
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: 1.2.1, 1.1
- Verification: Test
- Open items: What address configuration, persistence, and activation behavior is required by "Set IP address: Included"?

1.3.1.6 Set gain (attenuator): Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.6.2, 2.6.7
- Verification: Test
- Open items: none

1.3.1.6.1 One attenuator on each RF board, up to 4 RF boards

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.6.1
- Verification: Test
- Open items: none

1.3.1.6.2 Range of attenuator is 0 to 20 dB, increments of 1 dB

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: none stated
- Verification: Test
- Open items: none

1.3.1.6.3 See section 2.6 for more information on attenuator control

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.6
- Verification: Inspection
- Open items: none

1.3.1.7 Read gain (attenuator): Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.6
- Verification: Test
- Open items: Does "Read gain (attenuator)" return the commanded setting or an independently measured value?

1.3.1.8 LNB Reboot: Included.

- Type: Firmware – infrastructure
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1
- Verification: Test
- Open items: What reset scope and completion behavior is required by "LNB Reboot: Included"?

1.3.1.9 Alarm & warning reset: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.11, 2.12
- Verification: Test
- Open items: What does "Alarm & warning reset" clear when a triggering condition remains, and how does reset interact with the states in 2.11 and 2.12?

1.3.1.10 Read all warnings: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.11
- Verification: Test
- Open items: none

1.3.1.10.1 See section 2.11 for warning criteria

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.11
- Verification: Inspection
- Open items: none

1.3.1.11 Read all alarms: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.12
- Verification: Test
- Open items: none

1.3.1.11.1 See section 2.12 for alarm criteria

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 2.12
- Verification: Inspection
- Open items: none

1.3.1.12 Read accumulated operating hours: Included. This function reports the number of unmuted operating hours.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1
- Verification: Test
- Open items: What defines "unmuted operating hours" and what accumulation, resolution, and persistence behavior is required?

1.3.1.13 Read LNB model: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 1.7.1.1
- Verification: Test
- Open items: none

1.3.1.13.1 To be set at test, see section 1.7

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.7
- Verification: Inspection
- Open items: none

1.3.1.14 Read LNB serial number: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 1.7.1.2
- Verification: Test
- Open items: none

1.3.1.14.1 To be set at test, see section 1.7

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.7
- Verification: Inspection
- Open items: none

1.3.1.15 Read hardware LNB revision: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 1.7.1.3
- Verification: Test
- Open items: none

1.3.1.15.1 To be set at test, see section 1.7

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.7
- Verification: Inspection
- Open items: none

1.3.1.16 Read LNB firmware version: Included.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 1.3.1.16.1
- Verification: Test
- Open items: none

1.3.1.16.1 To be set in source code

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: none stated
- Verification: Inspection
- Open items: What firmware-version format is required by "To be set in source code"?

1.3.2 M&C Parametric values: The Item shall include capabilities to measure the internal parametric values described below and retrieve these values via the M&C interface.

- Type: Firmware – application logic
- Work package: WP-04 M&C functions and parametric reporting
- Depends on: 1.2.1, 2.8.5
- Verification: Test
- Open items: none

1.3.2.1 Output IF signal power level: With a minimum range of -33 dBm to +12 dBm.

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 2.8.5, 2.8.6
- Verification: Test
- Open items: none

1.3.2.2 IF detector uncertainty (upper): ≤ 2.0 dB.

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 2.8.4.1, 2.8.5
- Verification: Test
- Open items: Under what conditions and acceptance method is "IF detector uncertainty (upper): ≤ 2.0 dB" verified?

1.3.2.3 IF detector type: RMS.

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 2.8.1
- Verification: Test
- Open items: none

1.3.2.4 Measurement rate: ≥ 4 measurements/second.

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 2.8.1, 2.8.5
- Verification: Test
- Open items: Does "Measurement rate: ≥ 4 measurements/second" apply collectively or separately to each reported value?

1.3.3 Factory Reset: The Item shall include a Factory Reset function implemented as detailed below

- Type: Firmware – infrastructure
- Work package: WP-14 Factory reset
- Depends on: none stated
- Verification: Test
- Open items: Which values and states must the "Factory Reset" restore, retain, or clear?

1.3.3.1 Factory reset interface: Internet Protocol over Ethernet with an aliased static IP address.

- Type: Firmware – infrastructure
- Work package: WP-14 Factory reset
- Depends on: 1.1
- Verification: Test
- Open items: What is the "aliased static IP address" and how is it configured?

1.3.3.2 Factory reset interface security: The factory reset connection shall utilize TLS.

- Type: Firmware – infrastructure
- Work package: WP-14 Factory reset
- Depends on: 1.3.3.1
- Verification: Test
- Open items: Which TLS version is required by "shall utilize TLS" for factory reset?

1.3.3.3 Factory reset interface limitations: Only a factory reset shall be possible over the factory reset interface.

- Type: Firmware – infrastructure
- Work package: WP-14 Factory reset
- Depends on: 1.3.3.1
- Verification: Test
- Open items: none

1.3.3.4 Factory reset access: Only users with specific access privileges shall be able to execute a factory reset. Seller shall obtain end customer approval for the access privilege schema.

- Type: Firmware – infrastructure
- Work package: WP-14 Factory reset
- Depends on: 1.3.3.1
- Verification: Test
- Open items: Which users and privileges satisfy the "end customer approval" of the "access privilege schema"?

### 1.4 Event Recorder

- Type: Heading

1.4.1 Event record: The Item shall include an event recorder with the capabilities described below. An event recorder captures the state Item’s internal state in a continual logging operation and persists (semi-permanently logs) the state when the Item’s state changes.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: none stated
- Verification: Test
- Open items: Which fields, retention period, capacity, and persistence behavior define the "state" and "semi-permanently logs" operation?

1.4.1.1 Operator initiated changes: The Item shall log a state record prior to and following all Operator initiated changes.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: none stated
- Verification: Test
- Open items: Which actions count as "all Operator initiated changes" and how do they overlap 1.4.1.2?

1.4.1.2 M&C change: The Item shall log a state record prior to and after all M&C initiated changes.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: 1.2.1
- Verification: Test
- Open items: Which actions count as "all M&C initiated changes" and how do they overlap 1.4.1.1?

1.4.1.3 Firmware update: The Item shall log a state record prior to and after all firmware updates.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: 1.6
- Verification: Test
- Open items: What record is required "after all firmware updates" when an update fails and rolls back under 1.6.5?

1.4.1.4 Warnings, Alarms & Faults: The Item shall log a state record prior to and following all detected internal warnings, alarms, and faults.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: 2.11, 2.12
- Verification: Test
- Open items: Which "faults" beyond 2.11 and 2.12 must be detected and recorded?

1.4.1.5 Event recorder format: The Event Recorder log should follow RFC 5424.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: none stated
- Verification: Test
- Open items: Is "should follow RFC 5424" mandatory for acceptance or an objective?

1.4.1.6 Human readable: The Event Recorder log shall be human readable.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: none stated
- Verification: Demonstration
- Open items: none

1.4.1.7 HMI access: The Event Recorder log shall be operator downloadable via the HMI.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: none stated
- Verification: Demonstration
- Open items: What "HMI" and download interface are required?

1.4.1.8 Timestamps: All recorded events shall include a timestamp complying with ISO 8601-1 to the nearest millisecond.

- Type: Firmware – application logic
- Work package: WP-11 Event recorder
- Depends on: 1.5
- Verification: Test
- Open items: Which edition and timestamp representation of "ISO 8601-1" are required, including behavior before network time is obtained?

### 1.5 Time

- Type: Heading

1.5.1 Item time: The Item shall incorporate time keeping (clock) capabilities described below

- Type: Heading

1.5.1.1 Time source: The Item shall obtain time via a network time server specified by the operator or external M&C master.

- Type: Firmware – infrastructure
- Work package: WP-12 Time keeping (NTP)
- Depends on: 1.1
- Verification: Test
- Open items: How is the time server "specified by the operator or external M&C master" configured, and what is required when it is unavailable?

1.5.1.2 Time exchange protocol: Network Time Protocol.

- Type: Firmware – infrastructure
- Work package: WP-12 Time keeping (NTP)
- Depends on: none stated
- Verification: Test
- Open items: none

1.5.1.3 NTP protocol version (objective): NTP version 4.

- Type: Firmware – infrastructure
- Work package: WP-12 Time keeping (NTP)
- Depends on: 1.5.1.2
- Verification: Test
- Open items: Is "NTP version 4" an acceptance requirement or an optional objective relative to the version 3 threshold in 1.5.1.4?

1.5.1.4 NTP protocol version (threshold): NTP version 3.

- Type: Firmware – infrastructure
- Work package: WP-12 Time keeping (NTP)
- Depends on: 1.5.1.2
- Verification: Test
- Open items: none

1.5.1.5 Time error: ≤ 1 second.

- Type: Firmware – infrastructure
- Work package: WP-12 Time keeping (NTP)
- Depends on: 1.5.1.1
- Verification: Test
- Open items: Under what synchronization and operating conditions must "Time error: ≤ 1 second" hold?

1.5.1.6 Time precision: ≤ 1 millisecond.

- Type: Firmware – infrastructure
- Work package: WP-12 Time keeping (NTP)
- Depends on: 1.5.1.1
- Verification: Test
- Open items: What measurement and operating conditions define "Time precision: ≤ 1 millisecond"?

### 1.6 Firmware Update

- Type: Heading

1.6.1 Remote firmware & software updates: All Item firmware and software shall be remotely updateable via the Ethernet interface.

- Type: Firmware – infrastructure
- Work package: WP-13 Firmware update and rollback
- Depends on: 1.1
- Verification: Test
- Open items: What update transport, image format, and authorization are required by "remotely updateable via the Ethernet interface"?

1.6.2 Firmware & software automation: The Item shall include a facility for the end user to remotely update firmware and software across their entire Item population. For clarity, this is intended as the ability to perform an unattended remote update.

- Type: Firmware – infrastructure
- Work package: WP-13 Firmware update and rollback
- Depends on: 1.6.1
- Verification: Test
- Open items: What population-management interface and update result reporting are required for an "unattended remote update"?

1.6.3 Special firmware update tools: The Item shall not require special or Seller specific tools to conduct and manage firmware updates.

- Type: Firmware – infrastructure
- Work package: WP-13 Firmware update and rollback
- Depends on: 1.6.1
- Verification: Test
- Open items: none

1.6.4 Firmware & software integrity: Firmware and software updates shall be authenticated during the update process using strong cryptographic methods such as SHA-2 or SHA-3 HASH functions or end customer approved digital signature methods.

- Type: Firmware – infrastructure
- Work package: WP-13 Firmware update and rollback
- Depends on: 1.6.1
- Verification: Test
- Open items: Which integrity and authentication method is selected by "such as SHA-2 or SHA-3 HASH functions or end customer approved digital signature methods"?

1.6.5 Firmware & software update failure: In the event of a firmware or software update failure, the Item shall automatically roll back to the most recent functioning firmware and software version.

- Type: Firmware – infrastructure
- Work package: WP-13 Firmware update and rollback
- Depends on: 1.6.1
- Verification: Test
- Open items: What conditions define "update failure" and a "functioning firmware and software version"?

1.6.6 Firmware & software roll back: The end user shall be able to remotely roll back the firmware and software to prior versions.

- Type: Firmware – infrastructure
- Work package: WP-13 Firmware update and rollback
- Depends on: 1.6.1
- Verification: Test
- Open items: How many "prior versions" must be retained and what rollback compatibility rules apply?

### 1.7 Set at Test Variables

- Type: Heading

1.7.1 The following alphanumeric variables will be set at test by the operator using the Ethernet interface.

- Type: Firmware – application logic
- Work package: WP-15 Set-at-test variables
- Depends on: 1.1
- Verification: Test
- Open items: What write authorization, persistence, and later-edit policy applies to variables "set at test"?

1.7.1.1 LNB Model Number

- Type: Firmware – application logic
- Work package: WP-15 Set-at-test variables
- Depends on: none stated
- Verification: Test
- Open items: none

1.7.1.2 LNB Serial Number

- Type: Firmware – application logic
- Work package: WP-15 Set-at-test variables
- Depends on: none stated
- Verification: Test
- Open items: none

1.7.1.3 Hardware LNB revision

- Type: Firmware – application logic
- Work package: WP-15 Set-at-test variables
- Depends on: none stated
- Verification: Test
- Open items: none

1.7.2 Each variable is a maximum of 32 characters and shall be leading zero padded.

- Type: Firmware – application logic
- Work package: WP-15 Set-at-test variables
- Depends on: none stated
- Verification: Test
- Open items: Does "leading zero padded" mean padding every variable to 32 characters, and what character encoding is required?

1.7.3 Dashes (-) are allowed.

- Type: Firmware – application logic
- Work package: WP-15 Set-at-test variables
- Depends on: none stated
- Verification: Test
- Open items: none

## 2 Internal LNB Requirements

- Type: Heading

### 2.1 Ethernet PHY Interface

- Type: Heading

2.1.1 The processor shall interface with the Ethernet PHY IC to meet all requirements in this document.

- Type: Firmware – driver/hardware interface
- Work package: WP-01 Ethernet interface, PHY, and network config
- Depends on: 1.1
- Verification: Test
- Open items: Which processor and PHY are selected to "meet all requirements", given the candidate devices and speed alternatives in HW-1, HW-2, and HW-6?

### 2.2 Status Pin GPIO Inputs from RF board(s)

- Type: Heading

2.2.1 One status signal from each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.2.2 Status signals are high for no faults; low indicates a fault

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.2.1
- Verification: Test
- Open items: none

2.2.3 Weak pull up on each status pin so that status is high when RF boards are not connected

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.2.1
- Verification: Test
- Open items: What pull-up strength is required by "Weak pull up"?

2.2.4 Status pin changes shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.2.1, 2.2.2
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.3 Status Pin GPIO inputs from on-board power regulators

- Type: Heading

2.3.1 One status signal from each LT80603, up to 12

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: Component facts needed: exact device identity and status interface for "LT80603".

2.3.1.1 Open drain output with pullup to 3.3 V

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.3.1
- Verification: Test
- Open items: Is "pullup to 3.3 V" provided by the board or a processor configuration?

2.3.1.2 High for normal operation, low for fault state

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.3.1
- Verification: Test
- Open items: none

2.3.2 One status signal from the POE controller, MAX5969A

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.3.2.1 Open drain output with pullup to 3.3 V

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.3.2
- Verification: Test
- Open items: Is "pullup to 3.3 V" provided by the board or a processor configuration?

2.3.2.2 High for normal operation, low for fault state

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.3.2
- Verification: Test
- Open items: none

2.3.3 Status pin changes shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.3.1, 2.3.2
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.4 LED control

- Type: Heading

2.4.1 Initializing state: Yellow LED

- Type: Heading

2.4.1.1 Enter this state on power up, and on reset. Hold this state until all input status pins are in “no fault” state.

- Type: Firmware – application logic
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.2, 2.3
- Verification: Test
- Open items: none

2.4.1.2 Set GPIO output for Yellow LED high (3.3 VDC), must source up to 6 mA

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.4.1.3 Set GPIO outputs for Green LED and Red LED low (GND)

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.4.2 Normal Operating state: Green LED

- Type: Heading

2.4.2.1 Enter this state from Initializing state and from Fault state after all status input pins are in normal operating state

- Type: Firmware – application logic
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.2, 2.3
- Verification: Test
- Open items: none

2.4.2.2 Set GPIO output for Green LED high (3.3 VDC), must source up to 6 mA

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.4.2.3 Set GPIO outputs for Yellow LED and Red LED low (GND)

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.4.3 Fault state: Red LED

- Type: Heading

2.4.3.1 Enter this state from normal operating state when any status input pin goes to fault state

- Type: Firmware – application logic
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.2, 2.3
- Verification: Test
- Open items: none

2.4.3.2 Set GPIO output for Red LED high (3.3 VDC), must source up to 6 mA

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.4.3.3 Set GPIO outputs for Green LED and Yellow LED low (GND)

- Type: Firmware – driver/hardware interface
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: none stated
- Verification: Test
- Open items: none

2.4.4 LED state changes shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.4.1, 2.4.2, 2.4.3
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.5 SPI bus I/O

- Type: Heading

2.5.1 ADF4368 synthesizer register write and read

- Type: Heading

2.5.1.1 One ADF4368 synthesizer on each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Test
- Open items: none

2.5.1.2 The registers shall be written and read on power up and after reset

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: 2.5.1.3, 2.5.1.6
- Verification: Test
- Open items: none

2.5.1.3 The processor shall have the capability to write and read all registers defined in the ADF4368 data sheet.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Test
- Open items: Component facts needed: register definitions and read/write behavior for "all registers defined in the ADF4368 data sheet".

2.5.1.4 The processor shall have the capability to write unique values into the ADF4368 registers for each unique RF board.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: 2.5.1.1, 2.5.1.3
- Verification: Test
- Open items: none

2.5.1.5 The register values shall be defined in the firmware and shall not be controllable through the Ethernet interface.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: 2.5.1.6
- Verification: Test
- Open items: none

2.5.1.6 Register values to be used for this project for RF board 1 and RF board 2 are defined in Appendix A.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Inspection
- Open items: Appendix A is "TBD" despite "Register values" being stated as defined there, and values for RF boards 3 and 4 are not supplied.

2.5.1.7 When the registers are written and/or read a list of the registers and values shall be output on the UART interface. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.5.1.3
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.5.2 ADAR2001 Multiplier register write and read

- Type: Heading

2.5.2.1 One ADAR2001 multiplier on each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Test
- Open items: none

2.5.2.2 The registers shall be written and read on power up and after reset

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: 2.5.2.3, 2.5.2.6
- Verification: Test
- Open items: none

2.5.2.3 The processor shall have the capability to write and read all registers defined in the ADAR2001 data sheet.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Test
- Open items: Component facts needed: register definitions and read/write behavior for "all registers defined in the ADAR2001 data sheet".

2.5.2.4 The processor shall have the capability to write unique values into the ADAR2001 registers for each unique RF board.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: 2.5.2.1, 2.5.2.3
- Verification: Test
- Open items: none

2.5.2.5 The register values shall be defined in the firmware and shall not be controllable through the Ethernet interface.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: 2.5.2.6
- Verification: Test
- Open items: none

2.5.2.6 Register values to be used for this project for RF board 1 and RF board 2 are defined in Appendix B.

- Type: Firmware – driver/hardware interface
- Work package: WP-10 SPI devices (ADF4368, ADAR2001)
- Depends on: none stated
- Verification: Inspection
- Open items: Appendix B is "TBD" despite "Register values" being stated as defined there, and values for RF boards 3 and 4 are not supplied.

2.5.2.7 When the registers are written and/or read a list of the registers and values shall be output on the UART interface. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.5.2.3
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.6 HMC425A IF Step Attenuator Control

- Type: Heading

2.6.1 One HMC425A step attenuator on each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: none stated
- Verification: Test
- Open items: none

2.6.2 Six GPIO output bits for each RF board

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: none stated
- Verification: Test
- Open items: none

2.6.3 High signal is 3.3 VDC; Low signal is 0 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: none stated
- Verification: Test
- Open items: none

2.6.4 Power up and reset condition: set all six pins high (0 dB attenuation state) for all RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: 2.6.2, 2.6.3, 2.6.7
- Verification: Test
- Open items: none

2.6.5 Upon Ethernet command, set pins as needed to achieve attenuation commanded. See section 1.3.1.6 for more information on Ethernet command.

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: 1.3.1.6, 2.6.2, 2.6.7
- Verification: Test
- Open items: What behavior is required for an "attenuation commanded" outside the 0 to 20 dB range or 1 dB increments in 1.3.1.6.2?

2.6.6 Unique GPIO bit settings for each unique RF board

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: 2.6.1, 2.6.2
- Verification: Test
- Open items: none

2.6.7 Truth Table for attenuator bit settings (from HMC425A datasheet)

| **Attenuation** | **Bit 1** | **Bit 2** | **Bit 3** | **Bit 4** | **Bit 5** | **Bit 6** |
|-----------------|-----------|-----------|-----------|-----------|-----------|-----------|
| 0 dB            | H         | H         | H         | H         | H         | H         |
| 0.5 dB          | H         | H         | H         | H         | H         | L         |
| 1 dB            | H         | H         | H         | H         | L         | H         |
| 2 dB            | H         | H         | H         | L         | H         | H         |
| 4 dB            | H         | H         | L         | H         | H         | H         |
| 8 dB            | H         | L         | H         | H         | H         | H         |
| 16 dB           | L         | H         | H         | H         | H         | H         |
| 31.5 dB         | L         | L         | L         | L         | L         | L         |

> Combination of above states provides attenuation equal to sum of bits selected.

- Type: Firmware – driver/hardware interface
- Work package: WP-05 IF attenuator control (HMC425A)
- Depends on: none stated
- Verification: Test
- Open items: none

2.6.8 Attenuator state changes shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.6.2
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.7 HMC472A 100 MHz Step Attenuator Control

- Type: Heading

2.7.1 One HMC472A step attenuator

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.7.2 Six GPIO output bits

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.7.3 High signal is 3.3 VDC (2.0 to 5.0 VDC); Low signal is 0 VDC (0 to 0.8 VDC)

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.7.4 Power up and reset condition: set bit 1 low, set bit 2 low, all other pins high (24 dB attenuation state)

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.7.2, 2.7.3, 2.7.6
- Verification: Test
- Open items: none

2.7.5 100 MHz Attenuator setting algorithm

- Type: Heading

2.7.5.1 Apply 100 MHz attenuator setting algorithm 5 +/- 1 seconds after power up and/or reset. Do not apply the algorithm again until the next power cycle and/or reset.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.7.4
- Verification: Test
- Open items: none

2.7.5.2 Read the ADC Input for 100 MHz Reference Power Detector and calculate the input power level as shown in section 2.9.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.6
- Verification: Test
- Open items: none

2.7.5.3 Adjust the attenuator bits such that the power level at the detector input is -1 dBm +/- 1 dB.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.7.6, 2.9.6
- Verification: Test
- Open items: none

2.7.5.4 Wait 1000 ms +/- 100 ms, remeasure ADC input to verify power level is in required range. If not re-adjust attenuator.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.7.5.3, 2.9.6
- Verification: Test
- Open items: What retry limit and failure response apply to "If not re-adjust attenuator" if the target range cannot be reached?

2.7.5.5 After setting is complete this attenuator will not be adjusted again until the next power on and/or reset.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.7.5.3, 2.7.5.4
- Verification: Test
- Open items: Does "will not be adjusted again until the next power on and/or reset" permit the factory-test setting required by 2.9.5.1.1?

2.7.6 Truth Table for attenuator bit settings (from HMC472A datasheet)

| **Attenuation** | **Bit 1** | **Bit 2** | **Bit 3** | **Bit 4** | **Bit 5** | **Bit 6** |
|-----------------|-----------|-----------|-----------|-----------|-----------|-----------|
| 0 dB            | H         | H         | H         | H         | H         | H         |
| 0.5 dB          | H         | H         | H         | H         | H         | L         |
| 1 dB            | H         | H         | H         | H         | L         | H         |
| 2 dB            | H         | H         | H         | L         | H         | H         |
| 4 dB            | H         | H         | L         | H         | H         | H         |
| 8 dB            | H         | L         | H         | H         | H         | H         |
| 16 dB           | L         | H         | H         | H         | H         | H         |
| 31.5 dB         | L         | L         | L         | L         | L         | L         |

> Combination of above states provides attenuation equal to sum of bits selected.

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.7.7 Attenuator state changes shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.7.2
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.8 ADC Inputs for IF Power Detector(s)

- Type: Heading

2.8.1 One ADL5906 power detector on each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: Does "ADL5906" identify the same RF-board device called "ADL5906A" in 2.10.4?

2.8.2 Detected power for each channel to be reported as described in 1.3.2

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 1.3.2, 2.8.5
- Verification: Test
- Open items: none

2.8.3 ADL5906 power detector analog voltage output

- Type: Heading

2.8.3.1 Range of output voltages: 0.3 to 3.8 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: none

2.8.3.2 ADC reference voltage: 4 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: none

2.8.4 Calibration during factory test

- Type: Heading

2.8.4.1 Capability to calibrate each detector with three IF output power levels at low, mid, and high end of IF frequency band, at cold, room, high temperature

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 2.8.1, 2.8.3.1, 2.8.3.2
- Verification: Test
- Open items: What calibration procedure, coefficient storage, and acceptance criteria implement "Capability to calibrate each detector"?

2.8.4.1.1 Calibration power levels: -30 dBm, -20 dBm, +13 dBm

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: Are "Calibration power levels" specified at the IF output or detector input, given the 20 dB distinction in 2.8.6?

2.8.4.1.2 Calibration IF frequencies: 3.5 GHz, 4.5 GHz, 5.5 GHz

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: none

2.8.4.1.2.1 Modulated IF signal

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: What modulation and signal parameters define "Modulated IF signal"?

2.8.4.1.3 Calibration temperatures: -40 C, +23 C, +55 C

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Test
- Open items: none

2.8.4.1.4 For reference only: typical slope is 42 to 52 mV/dB

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Inspection
- Open items: none

2.8.5 Use average intercept and slope values across frequency and interpolated values across temperature for measurement calculations during normal operation

- Type: Firmware – control algorithm
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: 2.8.4.1
- Verification: Test
- Open items: What averaging, interpolation, and out-of-calibration-temperature behavior is required by "average intercept and slope values across frequency and interpolated values across temperature"?

2.8.6 For reference only: 20 dB coupler is used; actual IF output power level is 20 dB higher than detected power level

- Type: Firmware – driver/hardware interface
- Work package: WP-07 IF power detection and calibration (ADL5906)
- Depends on: none stated
- Verification: Inspection
- Open items: none

2.8.7 IF power detector readings shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.8.5
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.9 ADC Input for 100 MHz Reference Power Detector

- Type: Heading

2.9.1 One ADL5904 power detector on the processor board

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.9.2 ADL5904 power detector analog voltage output

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: Is the repeated "ADL5904 power detector analog voltage output" in 2.9.3 only a grouping duplication or a separate requirement?

2.9.3 ADL5904 power detector analog voltage output

- Type: Heading

2.9.3.1 Expected range of output voltages: 0.45 to 1.45 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.9.3.2 ADC reference voltage: 1.8 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.9.4 ADC readings shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.9.1
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.9.5 Calibration during factory test

- Type: Heading

2.9.5.1 Capability to calibrate detector with two IF output power levels

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.1, 2.9.3.2
- Verification: Test
- Open items: Does "two IF output power levels" mean the 100 MHz reference calibration input levels specified in 2.9.5.1.2?

2.9.5.1.1 Set 100 MHz Reference attenuator to 12 dB, see section 2.7.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.7
- Verification: Test
- Open items: How does factory-test control of "Set 100 MHz Reference attenuator to 12 dB" interact with the no-further-adjustment rule in 2.7.5.5?

2.9.5.1.2 Calibration input power levels: -18 dBm, +10 dBm

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.9.5.1.3 Calculate the voltage at the ADC using the ADC bits and the reference voltage

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.3.2
- Verification: Test
- Open items: What ADC resolution is used to calculate voltage from "the ADC bits"?

2.9.5.1.4 The low calibration input power level is equivalent to -22 dBm at the detector input. Record the calculated voltage at this input.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.5.1.3
- Verification: Test
- Open items: none

2.9.5.1.5 The high calibration input power level is equivalent to +6 dBm at the detector input. Record the calculated voltage at this input.

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.5.1.3
- Verification: Test
- Open items: none

2.9.5.1.6 Calibration frequency: 100 MHz

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.9.5.1.7 Calibration temperature: 23 C

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Test
- Open items: none

2.9.5.2 For reference only: typical slope is 35 mV/dB

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: none stated
- Verification: Inspection
- Open items: none

2.9.6 Detected Power Calculation:

- Type: Heading

2.9.6.1 Convert ADC value to measured voltage based on reference voltage

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.3.2
- Verification: Test
- Open items: none

2.9.6.2 Convert measured voltage to power level based on calibration power levels and voltages and interpolating

- Type: Firmware – control algorithm
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.6.1, 2.9.5.1.4, 2.9.5.1.5
- Verification: Test
- Open items: What behavior is required outside the "calibration power levels and voltages" when "interpolating"?

2.9.7 For reference only: measured power level will be used to set reference attenuator control as shown in section 2.7

- Type: Firmware – driver/hardware interface
- Work package: WP-06 Reference attenuator and detector (HMC472A, ADL5904)
- Depends on: 2.9.6, 2.7
- Verification: Inspection
- Open items: none

2.9.8 100 MHz reference power detector readings shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.9.6
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.10 ADC Inputs for Temperature Monitoring

- Type: Heading

2.10.1 One MCP9700A temperature sensor on each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.2 One MCP9700A temperature sensor on Processor board

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.3 MCP9700A analog voltage output

- Type: Heading

2.10.3.1 Temperatures to be reported as described in 1.3.1.4

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: 1.3.1.4, 2.10.1, 2.10.2, 2.10.3.2, 2.10.3.3, 2.10.3.4
- Verification: Test
- Open items: none

2.10.3.2 ADC reference voltage 2.5 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.3.3 Output voltage at 0 °C = 400 mV

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.3.4 10.0 mV/°C

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.3.5 Measures -40 to 125 °C

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: How can a sensor that "Measures -40 to 125 °C" trigger the 2.12.2.1 alarm when "any temperature sensor exceeds 125 C"?

2.10.4 One ADL5906A temp sensor on each RF board, up to 4 RF boards

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: Does "ADL5906A" identify the same RF-board device called "ADL5906" in 2.8.1?

2.10.5 ADL5906A temperature sensor analog voltage output

- Type: Heading

2.10.5.1 ADC reference voltage 2.5 VDC

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.5.2 Output voltage at 25 °C = 1400 mV

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.5.3 4.8 mV/°C

- Type: Firmware – driver/hardware interface
- Work package: WP-08 Temperature monitoring
- Depends on: none stated
- Verification: Test
- Open items: none

2.10.6 Temperature measurements shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.10.3, 2.10.5
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.11 Warnings

- Type: Heading

2.11.1 Temperature Warning

- Type: Heading

2.11.1.1 Temperature Warning is triggered when any temperature sensor exceeds 105 C. See section 2.10 for information on temperature sensors.

- Type: Firmware – application logic
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.10
- Verification: Test
- Open items: What clear and reset behavior is required after "Temperature Warning is triggered", including interaction with 1.3.1.9?

2.11.2 Warnings shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.11.1.1
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.12 Alarms

- Type: Heading

2.12.1 Summary Alarm

- Type: Heading

2.12.1.1 Summary Alarm state is triggered from normal operating state when any status input pin goes to fault state. See sections 2.2 and 2.3 for information on status inputs.

- Type: Firmware – application logic
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.2, 2.3
- Verification: Test
- Open items: What clear and reset behavior is required after "Summary Alarm state is triggered", including interaction with 1.3.1.9?
- CD note: Open item: Confirm this supplied source annotation: In 2.12.1.1, "sections 2.1 and 2.3" was corrected to "sections 2.2 and 2.3".

2.12.2 Over Temperature Alarm

- Type: Heading

2.12.2.1 Over Temperature Alarm State is triggered when any temperature sensor exceeds 125 C. See section 2.10 for information on temperature sensors.

- Type: Firmware – application logic
- Work package: WP-09 Status inputs, warnings, alarms, LED
- Depends on: 2.10
- Verification: Test
- Open items: How can "any temperature sensor exceeds 125 C" be verified for the 2.10.3.5 sensor that "Measures -40 to 125 °C", and what clear and reset behavior is required under 1.3.1.9?

2.12.3 Alarm states and causes of the alarm state shall be reported to the UART output. See section 2.13 for more information on the UART requirements.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13, 2.12.1.1, 2.12.2.1
- Verification: Test
- Open items: none
- CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

### 2.13 UART output for board level test

- Type: Heading

2.13.1 The processor shall print status and engineering information to a UART output to be used during factory test

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.13.4
- Verification: Test
- Open items: What output format and cadence are required for "status and engineering information"?

2.13.2 The UART output shall include all information as described in requirements above.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: 2.2.4, 2.3.3, 2.4.4, 2.5.1.7, 2.5.2.7, 2.6.8, 2.7.7, 2.8.7, 2.9.4, 2.9.8, 2.10.6, 2.11.2, 2.12.3
- Verification: Test
- Open items: none

2.13.3 The software developer may include additional information in the UART output as needed.

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: none stated
- Verification: Inspection
- Open items: What acceptance scope applies to optional "additional information" included "as needed"?

2.13.4 UART Configuration

- Type: Heading

2.13.4.1 Baud rate: 115200

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: none stated
- Verification: Test
- Open items: none

2.13.4.2 Data bits: 8

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: none stated
- Verification: Test
- Open items: none

2.13.4.3 Parity: none

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: none stated
- Verification: Test
- Open items: none

2.13.4.4 Stop Bits: 1

- Type: Firmware – driver/hardware interface
- Work package: WP-16 UART test output
- Depends on: none stated
- Verification: Test
- Open items: none

## Test

TEST-1 The vendor shall develop automated test software to provide coverage of all requirements shown in this document.

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Test
- Open items: What exceptions or non-automated methods are acceptable when "automated test software" cannot provide coverage of a requirement?

TEST-2 The vendor shall complete testing to verify performance of all requirements in this document and compile a test report showing verification.

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Inspection
- Open items: none

TEST-3 Final test shall be completed on the target application board(s) provided by Amplitech.

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Test
- Open items: none

TEST-4 Vendor shall notify AmpliTech at least 5 business days prior to final test.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: When must "final test" be scheduled to provide "at least 5 business days" notice?

TEST-5 AmpliTech may choose to be present for final test at vendor site.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: Will AmpliTech exercise "may choose to be present", and what attendance arrangements are required?

## Contract

CON-1 The contract for this software development effort is preferred to be a firm fixed price contract.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: Which contract form is selected when "preferred to be a firm fixed price contract" is read with CON-2?

CON-2 Time and material contracts are also acceptable.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: Will "Time and material contracts are also acceptable" be selected instead of the firm-fixed-price preference in CON-1?

CON-3 Vendor will assign to AmpliTech, Inc. all right, title, and interest to software upon final payment, with exception of pre-existing material defined by the vendor in the proposal and agreed to by AmpliTech.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: Which "pre-existing material" will be defined in the proposal and agreed to by AmpliTech?

CON-4 Vendor shall supply an inventory list of all pre-existing or third-party code used in the project including exact version numbers and license types.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: none

CON-5 Open Source Restrictions: vendor shall not introduce restrictive open-source licenses (like GPL or AGPL) without prior written consent.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: Which licenses are "restrictive open-source licenses" beyond the examples "GPL or AGPL", and what written-consent process applies?

CON-6 Vendor shall provide 30 day post handover warranty in which any bugs identified by AmpliTech shall be repaired at zero cost.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: What event starts "30 day post handover warranty" and what response or repair timing applies to "any bugs identified"?

## Deliverables

CON-7 AmpliTech has 5 business days to review and accept deliverables.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: What acceptance criteria and outcome apply if the "5 business days to review and accept deliverables" expire without a decision?

SDRL-01 SDRL 01: Preliminary Software Functional Description Document, details what a software system or subsystem must do, how it must behave, and how users or other systems will interact with it, focusing on features, inputs, and outputs rather than underlying code

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SDRL-02 SDRL 02: Preliminary Software Interface Control Document, including command structures for communicating with and controlling the LNB

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SDRL-03 SDRL 03: Final Software Functional Description Document

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SDRL-04 SDRL 04: Final Software Interface Control Document

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SDRL-05 SDRL 05: Functional software to be used by AmpliTech for preliminary test

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Test
- Open items: What functionality and acceptance criteria define "Functional software" for "preliminary test"?

SDRL-06 SDRL 06: Software verification test report

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SDRL-07 SDRL 07: Final software source code – transfer ownership of private Git repository containing full commit history, branches, and tags

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: How does "transfer ownership of private Git repository" align with the assignment timing "upon final payment" in CON-3?

SDRL-08 SDRL 08: Final compiled software ready for installation on processor

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Test
- Open items: What artifact format and installation procedure define "Final compiled software ready for installation on processor"?

SDRL-09 SDRL 09: Build and Run Instructions, including description and version of all tools used to develop and build the software product, environment setup scripts, dependency manifests, deployment instructions such that AmpliTech can build, install, and run the software

- Type: Documentation deliverable
- Work package: WP-18 Documentation (SDRLs)
- Depends on: none stated
- Verification: Inspection
- Open items: none

SDRL-10 SDRL 10: Final automated test software source code, including description and version of all tools used to develop and build the test software, environment setup scripts, dependency manifests, deployment instructions such that AmpliTech can build, install, and run the test software

- Type: Test software
- Work package: WP-17 Automated test software
- Depends on: none stated
- Verification: Test
- Open items: none

## Milestones, Acceptance Criteria, Payment Structure for Firm Fixed Price Contract

MS-1 Completion of Preliminary Design Review

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: What review criteria define "Completion of Preliminary Design Review"?

MS-1.a Delivery of SDRL 01

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-01
- Verification: Inspection
- Open items: none

MS-1.b Delivery of SDRL 02

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-02
- Verification: Inspection
- Open items: none

MS-1.c First invoice of 10% of project cost after AmpliTech acceptance of deliverables

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-1.a, MS-1.b, CON-7
- Verification: Inspection
- Open items: none

MS-2 Progress Demonstration 1: using manufacturer development boards with at least 50% of requirements implemented

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Demonstration
- Open items: How is "at least 50% of requirements implemented" counted and accepted?

MS-2.a Delivery of SDRL 03

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-03
- Verification: Inspection
- Open items: none

MS-2.b Delivery of SDRL 04

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-04
- Verification: Inspection
- Open items: none

MS-2.c Next invoice of 30% of project cost after AmpliTech acceptance of deliverables

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-2.a, MS-2.b, CON-7
- Verification: Inspection
- Open items: none

MS-3 Progress Demonstration 2: using manufacturer development boards with at least 90% of requirements implemented

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Demonstration
- Open items: How is "at least 90% of requirements implemented" counted and accepted?

MS-3.a Next invoice of 30% of project cost after AmpliTech acceptance of deliverables

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-3, CON-7
- Verification: Inspection
- Open items: Which "deliverables" require acceptance for this invoice when MS-3 lists no SDRL delivery?

MS-4 Progress demonstration 3: using target application boards provided by AmpliTech, 100% of requirements implemented

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Demonstration
- Open items: How is "100% of requirements implemented" demonstrated and accepted on target boards?

MS-4.a Delivery of SDRL 05

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-05
- Verification: Inspection
- Open items: none

MS-4.b Next invoice of 15% of project cost after AmpliTech acceptance of deliverables

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-4.a, CON-7
- Verification: Inspection
- Open items: none

MS-5 Project completion

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Inspection
- Open items: What acceptance criteria define "Project completion" beyond the listed deliveries?

MS-5.a Delivery of SDRL 06

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-06
- Verification: Inspection
- Open items: none

MS-5.b Delivery of SDRL 07

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-07
- Verification: Inspection
- Open items: none

MS-5.c Delivery of SDRL 08

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-08
- Verification: Inspection
- Open items: none

MS-5.d Delivery of SDRL 09

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-09
- Verification: Inspection
- Open items: none

MS-5.e Delivery of SDRL 10

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SDRL-10
- Verification: Inspection
- Open items: none

MS-5.f Final invoice of 15% of project cost after AmpliTech acceptance of deliverables.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-5.a, MS-5.b, MS-5.c, MS-5.d, MS-5.e, CON-7
- Verification: Inspection
- Open items: How does "Final invoice" after acceptance of SDRL 07 align with the CON-3 assignment "upon final payment"?

## Tentative Schedule

SCH-1 The tentative schedule below assumes an effective date of contract of Nov 9, 2026. This date will likely change depending on AmpliTech receipt of contract from end customer. The target finish date for each milestone below will shift corresponding to the actual EDC.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: When will the "effective date of contract" be confirmed and the tentative milestone dates rebaselined?

SCH-2 AmpliTech will work to accelerate the schedule for the target application board to be provided to the vendor. Updates on this will be provided during the course of the project.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: When will the promised "Updates" to accelerate target-board availability be provided?

SCH-3 Total duration of the contract is planned for 15 business weeks.

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: none

SCH-4 | **Software Development Schedule**                               | **75 days** | **Mon 11/9/26** | **Thu 3/4/27** |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: What working-day calendar reconciles "75 days" and the listed dates with "15 business weeks" in SCH-3?

SCH-5 | Effective Date of Contract (Tentative)                          | 0 days      | Mon 11/9/26     | Mon 11/9/26    |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: SCH-1
- Verification: Analysis
- Open items: none

SCH-6 | Complete Preliminary Design Review, SDRL 01, SDRL 02            | 10 days     | Mon 11/9/26     | Fri 11/20/26   |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-1
- Verification: Analysis
- Open items: none

SCH-7 | Complete Progress Demonstration 1, SDRL 03, SDRL 04             | 25 days     | Mon 11/23/26    | Thu 1/7/27     |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-2
- Verification: Analysis
- Open items: none

SCH-8 | Complete Progress Demonstration 2                               | 20 days     | Fri 1/8/27      | Thu 2/4/27     |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-3
- Verification: Analysis
- Open items: none

SCH-9 | Target Application boards provided to vendor from AmpliTech     | 60 days     | Mon 11/9/26     | Thu 2/11/27    |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: none stated
- Verification: Analysis
- Open items: How does target-board availability after "60 days" ending "Thu 2/11/27" align with "tentatively 10 weeks" in SCOPE-11?

SCH-10 | Complete Progress demonstration 3, SDRL 05                      | 5 days      | Fri 2/12/27     | Thu 2/18/27    |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-4, SCH-9
- Verification: Analysis
- Open items: none

SCH-11 | Project Completion, SDRL 06, SDRL 07, SDRL 08, SDRL 09, SDRL 10 | 10 days     | Fri 2/19/27     | Thu 3/4/27     |

- Type: Process/contract
- Work package: WP-20 Project/contract
- Depends on: MS-5
- Verification: Analysis
- Open items: none

## Index by work package

WP-01 Ethernet interface, PHY, and network config: OV-2, REF-1, REF-2, REF-13, 1.1, 1.3.1.5, 2.1.1

WP-02 M&C protocol: REST option: REF-3, 1.2.2.1, 1.2.2.2, 1.2.2.3, 1.2.2.4, 1.2.2.5, 1.2.2.6

WP-03 M&C protocol: SNMPv3 option: REF-5, REF-6, REF-7, REF-8, REF-11, 1.2.3, 1.2.3.1, 1.2.3.2, 1.2.3.3, 1.2.3.4, 1.2.3.5, 1.2.3.6

WP-04 M&C functions and parametric reporting: SCOPE-2, SCOPE-3, 1.2.1, 1.3.1.1, 1.3.1.1.1, 1.3.1.2, 1.3.1.2.1, 1.3.1.3, 1.3.1.4, 1.3.1.4.1, 1.3.1.6, 1.3.1.6.1, 1.3.1.6.2, 1.3.1.6.3, 1.3.1.7, 1.3.1.8, 1.3.1.9, 1.3.1.10, 1.3.1.10.1, 1.3.1.11, 1.3.1.11.1, 1.3.1.12, 1.3.1.13, 1.3.1.13.1, 1.3.1.14, 1.3.1.14.1, 1.3.1.15, 1.3.1.15.1, 1.3.1.16, 1.3.1.16.1, 1.3.2

WP-05 IF attenuator control (HMC425A): REF-17, 2.6.1, 2.6.2, 2.6.3, 2.6.4, 2.6.5, 2.6.6, 2.6.7

WP-06 Reference attenuator and detector (HMC472A, ADL5904): REF-19, REF-20, 2.7.1, 2.7.2, 2.7.3, 2.7.4, 2.7.5.1, 2.7.5.2, 2.7.5.3, 2.7.5.4, 2.7.5.5, 2.7.6, 2.9.1, 2.9.2, 2.9.3.1, 2.9.3.2, 2.9.5.1, 2.9.5.1.1, 2.9.5.1.2, 2.9.5.1.3, 2.9.5.1.4, 2.9.5.1.5, 2.9.5.1.6, 2.9.5.1.7, 2.9.5.2, 2.9.6.1, 2.9.6.2, 2.9.7

WP-07 IF power detection and calibration (ADL5906): OV-5, REF-18, 1.3.2.1, 1.3.2.2, 1.3.2.3, 1.3.2.4, 2.8.1, 2.8.2, 2.8.3.1, 2.8.3.2, 2.8.4.1, 2.8.4.1.1, 2.8.4.1.2, 2.8.4.1.2.1, 2.8.4.1.3, 2.8.4.1.4, 2.8.5, 2.8.6

WP-08 Temperature monitoring: REF-21, 2.10.1, 2.10.2, 2.10.3.1, 2.10.3.2, 2.10.3.3, 2.10.3.4, 2.10.3.5, 2.10.4, 2.10.5.1, 2.10.5.2, 2.10.5.3

WP-09 Status inputs, warnings, alarms, LED: OV-4, 2.2.1, 2.2.2, 2.2.3, 2.3.1, 2.3.1.1, 2.3.1.2, 2.3.2, 2.3.2.1, 2.3.2.2, 2.4.1.1, 2.4.1.2, 2.4.1.3, 2.4.2.1, 2.4.2.2, 2.4.2.3, 2.4.3.1, 2.4.3.2, 2.4.3.3, 2.11.1.1, 2.12.1.1, 2.12.2.1

WP-10 SPI devices (ADF4368, ADAR2001): OV-3, REF-15, REF-16, 2.5.1.1, 2.5.1.2, 2.5.1.3, 2.5.1.4, 2.5.1.5, 2.5.1.6, 2.5.2.1, 2.5.2.2, 2.5.2.3, 2.5.2.4, 2.5.2.5, 2.5.2.6

WP-11 Event recorder: REF-9, 1.4.1, 1.4.1.1, 1.4.1.2, 1.4.1.3, 1.4.1.4, 1.4.1.5, 1.4.1.6, 1.4.1.7, 1.4.1.8

WP-12 Time keeping (NTP): REF-4, REF-10, 1.5.1.1, 1.5.1.2, 1.5.1.3, 1.5.1.4, 1.5.1.5, 1.5.1.6

WP-13 Firmware update and rollback: 1.6.1, 1.6.2, 1.6.3, 1.6.4, 1.6.5, 1.6.6

WP-14 Factory reset: 1.3.3, 1.3.3.1, 1.3.3.2, 1.3.3.3, 1.3.3.4

WP-15 Set-at-test variables: 1.7.1, 1.7.1.1, 1.7.1.2, 1.7.1.3, 1.7.2, 1.7.3

WP-16 UART test output: OV-6, 2.2.4, 2.3.3, 2.4.4, 2.5.1.7, 2.5.2.7, 2.6.8, 2.7.7, 2.8.7, 2.9.4, 2.9.8, 2.10.6, 2.11.2, 2.12.3, 2.13.1, 2.13.2, 2.13.3, 2.13.4.1, 2.13.4.2, 2.13.4.3, 2.13.4.4

WP-17 Automated test software: SCOPE-7, SCOPE-8, SCOPE-12, TEST-1, TEST-2, TEST-3, SDRL-10

WP-18 Documentation (SDRLs): SCOPE-5, SCOPE-6, SDRL-01, SDRL-02, SDRL-03, SDRL-04, SDRL-06, SDRL-09

WP-19 Hardware support: HW-1, HW-2, HW-3, HW-4, SCOPE-9, SCOPE-10, REF-12, REF-14

WP-20 Project/contract: OV-1, HW-5, HW-6, HW-7, SCOPE-1, SCOPE-11, TEST-4, TEST-5, CON-1, CON-2, CON-3, CON-4, CON-5, CON-6, CON-7, SDRL-05, SDRL-07, SDRL-08, MS-1, MS-1.a, MS-1.b, MS-1.c, MS-2, MS-2.a, MS-2.b, MS-2.c, MS-3, MS-3.a, MS-4, MS-4.a, MS-4.b, MS-5, MS-5.a, MS-5.b, MS-5.c, MS-5.d, MS-5.e, MS-5.f, SCH-1, SCH-2, SCH-3, SCH-4, SCH-5, SCH-6, SCH-7, SCH-8, SCH-9, SCH-10, SCH-11

## Open items index

HW-1 Which processor is selected, given "candidate processor" and "Alternate processors can be considered"?

HW-2 Which PHY is selected, given "candidate Ethernet PHY" and "100 Base-T is also being considered"?

HW-3 Is the "candidate POE controller" the final selection required by 2.3.2?

HW-4 Who approves "Alternate processors" and when must selection be finalized?

HW-5 Does "Requirement 1.1 specifies 1000 Base-T" remain the baseline while HW-6 considers 100 Base-T?

HW-6 Is "100 Base-T is also being considered" an alternative to the "Single 1000 Base-T" requirement in 1.1?

HW-7 When is the requested "high level description of cost and schedule differences" due?

SCOPE-2 Which protocol is to be defined under "Definition of protocol", given the alternatives in 1.2.1?

SCOPE-10 Which "manufacturer evaluation boards" are needed and who supplies them?

SCOPE-11 How does "tentatively 10 weeks" for target-board availability align with the "60 days" ending "Thu 2/11/27" in SCH-9?

REF-3 CD note: Open item: Confirm this supplied source annotation: The References entry "Open API Specification v3.1.1" is not in the Word file. Requirement 1.2.2.1 is the only place the Word file names it.

1.1 Is "Single 1000 Base-T" the selected baseline, given "100 Base-T is also being considered" in HW-6?

1.2.1 Which of "either a RESTful or Simple Network Management Protocol (SNMP)" is selected for implementation?

1.2.2.1 What project-specific API definition is required by "comply with Open API Specification v3.1.1"?

1.2.2.1 CD note: Open item: Confirm this supplied source annotation: The References entry "Open API Specification v3.1.1" is not in the Word file. Requirement 1.2.2.1 is the only place the Word file names it.

1.2.2.4 Which "authentication scheme approved by end customer" is required?

1.2.2.6 Which roles and permissions satisfy "End customer must approved the Seller role scheme"?

1.2.3.6 What permissions and membership rules are required for the "minimum of 15 groups"?

1.3.1.4 Which temperatures are "critical internal temperatures", given the sensor sets in 2.10?

1.3.1.5 What address configuration, persistence, and activation behavior is required by "Set IP address: Included"?

1.3.1.7 Does "Read gain (attenuator)" return the commanded setting or an independently measured value?

1.3.1.8 What reset scope and completion behavior is required by "LNB Reboot: Included"?

1.3.1.9 What does "Alarm & warning reset" clear when a triggering condition remains, and how does reset interact with the states in 2.11 and 2.12?

1.3.1.12 What defines "unmuted operating hours" and what accumulation, resolution, and persistence behavior is required?

1.3.1.16.1 What firmware-version format is required by "To be set in source code"?

1.3.2.2 Under what conditions and acceptance method is "IF detector uncertainty (upper): ≤ 2.0 dB" verified?

1.3.2.4 Does "Measurement rate: ≥ 4 measurements/second" apply collectively or separately to each reported value?

1.3.3 Which values and states must the "Factory Reset" restore, retain, or clear?

1.3.3.1 What is the "aliased static IP address" and how is it configured?

1.3.3.2 Which TLS version is required by "shall utilize TLS" for factory reset?

1.3.3.4 Which users and privileges satisfy the "end customer approval" of the "access privilege schema"?

1.4.1 Which fields, retention period, capacity, and persistence behavior define the "state" and "semi-permanently logs" operation?

1.4.1.1 Which actions count as "all Operator initiated changes" and how do they overlap 1.4.1.2?

1.4.1.2 Which actions count as "all M&C initiated changes" and how do they overlap 1.4.1.1?

1.4.1.3 What record is required "after all firmware updates" when an update fails and rolls back under 1.6.5?

1.4.1.4 Which "faults" beyond 2.11 and 2.12 must be detected and recorded?

1.4.1.5 Is "should follow RFC 5424" mandatory for acceptance or an objective?

1.4.1.7 What "HMI" and download interface are required?

1.4.1.8 Which edition and timestamp representation of "ISO 8601-1" are required, including behavior before network time is obtained?

1.5.1.1 How is the time server "specified by the operator or external M&C master" configured, and what is required when it is unavailable?

1.5.1.3 Is "NTP version 4" an acceptance requirement or an optional objective relative to the version 3 threshold in 1.5.1.4?

1.5.1.5 Under what synchronization and operating conditions must "Time error: ≤ 1 second" hold?

1.5.1.6 What measurement and operating conditions define "Time precision: ≤ 1 millisecond"?

1.6.1 What update transport, image format, and authorization are required by "remotely updateable via the Ethernet interface"?

1.6.2 What population-management interface and update result reporting are required for an "unattended remote update"?

1.6.4 Which integrity and authentication method is selected by "such as SHA-2 or SHA-3 HASH functions or end customer approved digital signature methods"?

1.6.5 What conditions define "update failure" and a "functioning firmware and software version"?

1.6.6 How many "prior versions" must be retained and what rollback compatibility rules apply?

1.7.1 What write authorization, persistence, and later-edit policy applies to variables "set at test"?

1.7.2 Does "leading zero padded" mean padding every variable to 32 characters, and what character encoding is required?

2.1.1 Which processor and PHY are selected to "meet all requirements", given the candidate devices and speed alternatives in HW-1, HW-2, and HW-6?

2.2.3 What pull-up strength is required by "Weak pull up"?

2.2.4 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.3.1 Component facts needed: exact device identity and status interface for "LT80603".

2.3.1.1 Is "pullup to 3.3 V" provided by the board or a processor configuration?

2.3.2.1 Is "pullup to 3.3 V" provided by the board or a processor configuration?

2.3.3 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.4.4 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.5.1.3 Component facts needed: register definitions and read/write behavior for "all registers defined in the ADF4368 data sheet".

2.5.1.6 Appendix A is "TBD" despite "Register values" being stated as defined there, and values for RF boards 3 and 4 are not supplied.

2.5.1.7 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.5.2.3 Component facts needed: register definitions and read/write behavior for "all registers defined in the ADAR2001 data sheet".

2.5.2.6 Appendix B is "TBD" despite "Register values" being stated as defined there, and values for RF boards 3 and 4 are not supplied.

2.5.2.7 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.6.5 What behavior is required for an "attenuation commanded" outside the 0 to 20 dB range or 1 dB increments in 1.3.1.6.2?

2.6.8 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.7.5.4 What retry limit and failure response apply to "If not re-adjust attenuator" if the target range cannot be reached?

2.7.5.5 Does "will not be adjusted again until the next power on and/or reset" permit the factory-test setting required by 2.9.5.1.1?

2.7.7 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.8.1 Does "ADL5906" identify the same RF-board device called "ADL5906A" in 2.10.4?

2.8.4.1 What calibration procedure, coefficient storage, and acceptance criteria implement "Capability to calibrate each detector"?

2.8.4.1.1 Are "Calibration power levels" specified at the IF output or detector input, given the 20 dB distinction in 2.8.6?

2.8.4.1.2.1 What modulation and signal parameters define "Modulated IF signal"?

2.8.5 What averaging, interpolation, and out-of-calibration-temperature behavior is required by "average intercept and slope values across frequency and interpolated values across temperature"?

2.8.7 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.9.2 Is the repeated "ADL5904 power detector analog voltage output" in 2.9.3 only a grouping duplication or a separate requirement?

2.9.4 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.9.5.1 Does "two IF output power levels" mean the 100 MHz reference calibration input levels specified in 2.9.5.1.2?

2.9.5.1.1 How does factory-test control of "Set 100 MHz Reference attenuator to 12 dB" interact with the no-further-adjustment rule in 2.7.5.5?

2.9.5.1.3 What ADC resolution is used to calculate voltage from "the ADC bits"?

2.9.6.2 What behavior is required outside the "calibration power levels and voltages" when "interpolating"?

2.9.8 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.10.3.5 How can a sensor that "Measures -40 to 125 °C" trigger the 2.12.2.1 alarm when "any temperature sensor exceeds 125 C"?

2.10.4 Does "ADL5906A" identify the same RF-board device called "ADL5906" in 2.8.1?

2.10.6 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.11.1.1 What clear and reset behavior is required after "Temperature Warning is triggered", including interaction with 1.3.1.9?

2.11.2 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.12.1.1 What clear and reset behavior is required after "Summary Alarm state is triggered", including interaction with 1.3.1.9?

2.12.1.1 CD note: Open item: Confirm this supplied source annotation: In 2.12.1.1, "sections 2.1 and 2.3" was corrected to "sections 2.2 and 2.3".

2.12.2.1 How can "any temperature sensor exceeds 125 C" be verified for the 2.10.3.5 sensor that "Measures -40 to 125 °C", and what clear and reset behavior is required under 1.3.1.9?

2.12.3 CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

2.13.1 What output format and cadence are required for "status and engineering information"?

2.13.3 What acceptance scope applies to optional "additional information" included "as needed"?

TEST-1 What exceptions or non-automated methods are acceptable when "automated test software" cannot provide coverage of a requirement?

TEST-4 When must "final test" be scheduled to provide "at least 5 business days" notice?

TEST-5 Will AmpliTech exercise "may choose to be present", and what attendance arrangements are required?

CON-1 Which contract form is selected when "preferred to be a firm fixed price contract" is read with CON-2?

CON-2 Will "Time and material contracts are also acceptable" be selected instead of the firm-fixed-price preference in CON-1?

CON-3 Which "pre-existing material" will be defined in the proposal and agreed to by AmpliTech?

CON-5 Which licenses are "restrictive open-source licenses" beyond the examples "GPL or AGPL", and what written-consent process applies?

CON-6 What event starts "30 day post handover warranty" and what response or repair timing applies to "any bugs identified"?

CON-7 What acceptance criteria and outcome apply if the "5 business days to review and accept deliverables" expire without a decision?

SDRL-05 What functionality and acceptance criteria define "Functional software" for "preliminary test"?

SDRL-07 How does "transfer ownership of private Git repository" align with the assignment timing "upon final payment" in CON-3?

SDRL-08 What artifact format and installation procedure define "Final compiled software ready for installation on processor"?

MS-1 What review criteria define "Completion of Preliminary Design Review"?

MS-2 How is "at least 50% of requirements implemented" counted and accepted?

MS-3 How is "at least 90% of requirements implemented" counted and accepted?

MS-3.a Which "deliverables" require acceptance for this invoice when MS-3 lists no SDRL delivery?

MS-4 How is "100% of requirements implemented" demonstrated and accepted on target boards?

MS-5 What acceptance criteria define "Project completion" beyond the listed deliveries?

MS-5.f How does "Final invoice" after acceptance of SDRL 07 align with the CON-3 assignment "upon final payment"?

SCH-1 When will the "effective date of contract" be confirmed and the tentative milestone dates rebaselined?

SCH-2 When will the promised "Updates" to accelerate target-board availability be provided?

SCH-4 What working-day calendar reconciles "75 days" and the listed dates with "15 business weeks" in SCH-3?

SCH-9 How does target-board availability after "60 days" ending "Thu 2/11/27" align with "tentatively 10 weeks" in SCOPE-11?

CD note: Open item: Confirm this supplied source annotation: Every "See section 2.12.3 for more information on the UART requirements" was corrected to "2.13". The original text cited 2.12.3.

CD note: Open item: Confirm this supplied source annotation: In 2.12.1.1, "sections 2.1 and 2.3" was corrected to "sections 2.2 and 2.3".

CD note: Open item: Confirm this supplied source annotation: The References entry "Open API Specification v3.1.1" is not in the Word file. Requirement 1.2.2.1 is the only place the Word file names it.

CD note: Open item: Confirm this supplied source annotation: The Word file's footer reads "AmpliTech Proprietary | RFP: AGMDC0011 | Rev 01" even though the filename says Rev02.

## Self-check results

**Count — PASS:** The source contains 231 outline-numbered items; the PTS contains the same 231 items: 21 numbered Markdown headings and 210 numbered entry lines (including grouping entries).

**Outline coverage and order — PASS:** Every SOW outline number appears once in the specification body, in source order; none is missing, duplicated, or invented. Index references are excluded from this occurrence count. Milestone sub-items use the supplied Word-file letters.

**Statement fidelity — PASS:** All numbered Statements and all CD-identified Statements match source text character for character after removing source list markers and joining wrapped lines. Both truth tables match the source exactly after removing indentation; their accompanying text is retained.

**Markdown format — PASS:** No requirement line begins with a Markdown list marker or four-space indentation. Only metadata fields use list markers; top-level and second-level grouping items use Markdown headings.

**Notes and component facts — PASS:** Every CD note is phrased as an open item, including the supplied source-provenance annotations; no external component, standard, library, or license facts have been added. Unfetched component details are marked as component facts needed under Open items. No external sources were used.
