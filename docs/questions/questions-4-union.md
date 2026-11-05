# Questions for Union: Product Definition and Engineering Clarifications

**To:** Union product owner / Marketing and executive leadership  
**From:** Connected Development  
**Project:** LNB command-and-control software, RFP AGMDC0011  
**Source:** `union-sow_rev02.md`, reviewed alongside `pts-run-01.md`  
**Diagram source:** `Union-software-SOW_Rev02.docx`; extracted originals and rendered PNGs in `media/`  
**Status:** Initial discussion draft; no answers or proposed scope changes are assumed approved.

## Purpose and requested response

Before we turn the Statement of Work into a Product Requirements Document, we would like to align on the product commitments that will determine customer acceptance, development cost, launch timing, and long-term supportability. The questions below distinguish business decisions from the engineering detail needed to implement those decisions. They are intended to resolve uncertainty, not expand the contracted scope by default.

Each question opens with a product-owner decision and its business significance. The supporting bullets identify the SOW basis and the precise information your engineering team can supply. Product leadership can delegate technical answers while retaining approval of the resulting customer commitment.

Please respond by question ID with the decision or supporting artifact, the accountable owner, and an expected answer date. Where end-customer approval is required, please identify the approver and approval date. An answer of "not required for this release" is useful and should be recorded explicitly.

**Timing labels:** 
- **Early decision** means the answer materially affects scope, architecture, pricing, or hardware readiness. 
- **Definition needed** means the requirement needs a testable interpretation. 
- **Optional scope decision** identifies functionality not explicitly required by the SOW; it is not presumed included.

SOW references below use the original requirement numbers, not the section numbers in the first PTS. The PTS is an analytical aid, not an authoritative specification: its inferences and external component descriptions require confirmation before becoming requirements.

**Reviewed diagrams:** [Figure 1: Q-band LNB](media/image1.png) and [Figure 2: Power, control, and reference-frequency board](media/image2.png). Original vector images are preserved as [image1.emf](media/image1.emf) and [image2.emf](media/image2.emf). The drawings establish topology but do not replace detailed schematics or component values.

## A. Product baseline, ownership, and hardware readiness

### Q01. Which exact Infineon processor are we committing to?

**Product-owner question:** Can Union confirm the intended processor ordering code so procurement, hardware design, and software development all target the same device? Resolving this now avoids an unnecessary platform change later.

- **SOW basis:** Hardware and Target Environment; References. The prose names `XMC7200-E272K8384AA`, but its Infineon URL names `XMC7200D-E272K8384AA`, with an additional **D**.
- **Engineering details requested:** Confirm the full orderable part number, package, silicon revision if constrained, and governing datasheet/reference-manual revisions. Is the missing D simply a documentation omission? Is this still a candidate, or is it the selected production MCU?
- **Supporting reference:** [Infineon XMC7200D-E272K8384AA](https://www.infineon.com/part/XMC7200D-E272K8384AA). If alternatives remain acceptable, identify the approval authority and decision deadline rather than treating an alternative as already authorized.
- **Timing:** Early decision.

### Q02. Who has authority to approve product requirements and close open decisions?

**Product-owner question:** Who can make binding decisions for Union, AmpliTech, and the end customer, and how quickly can those decisions be obtained? A clear approval path protects the delivery schedule.

- **SOW basis:** Scope of Work; requirements 1.2.2.4-6 and 1.3.3.4; Deliverables; Contract. The SOW names AmpliTech as the deliverable reviewer and end customer as the approver of several security schemes.
- **Engineering details requested:** Identify the product owner, hardware lead, software-interface approver, security approver, and acceptance authority. Specify which decisions need end-customer sign-off and how approved clarifications become part of the requirements baseline.
- **Boundary to confirm:** Do not assume a contractual hierarchy from the document title alone. Please confirm Union's role, Connected Development's contracting counterpart, and the escalation/change-control process.
- **Timing:** Early decision.

### Q03. What product configurations must the first release actually support?

**Product-owner question:** Is the initial deliverable one specific LNB configuration, or a product family with multiple RF-board populations? This determines both development scope and the validation matrix.

- **SOW basis:** Requirements 1.3.1.6 and 2.2, 2.5, 2.6, 2.8, 2.10 repeatedly state "up to 4 RF boards"; appendices are described for boards 1 and 2.
- **Diagram evidence:** Both extracted figures show two channels, with 100 MHz reference distribution to Synth1 and Synth2. Is this the first-release configuration with four-channel capability reserved for later, or must four-channel operation be implemented and demonstrated now?
- **Engineering details requested:** List supported models, installed board counts, slot assignments, board types/revisions, and whether mixed board types are permitted. Must one binary support all approved configurations, or may images be model-specific?
- **Configuration boundary:** State how the installed configuration is established and whether replacement or hot-swapping is supported. A high status input cannot identify an installed board because requirement 2.2 intentionally pulls an unconnected input high.
- **Timing:** Early decision.

### Q04. When can we baseline the hardware information needed for software?

**Product-owner question:** Can we agree on the hardware information package and its delivery date? This is a prerequisite for credible pinout review, integration estimates, and bring-up planning.

- **SOW basis:** Project Overview figures; Hardware and Target Environment; Scope of Work; requirements 2.1-2.10.
- **Engineering details requested:** Confirm the revision/authority of the extracted LNB/processor block diagrams and supply current schematics, BOM, connector definitions, MCU pin-multiplexing assignments, clocks, reset/power sequencing, GPIO signal names, SPI topology/chip selects, and ADC channel mappings. The diagrams have now been extracted from the Word document; no replacement images are needed merely to make them readable.
- **Specific confirmations:** Identify the MAC-to-PHY interface and timing configuration for the DP83869HM, PHY management/reset wiring, and exact part/datasheet intended by `LT80603`. Map each of the up to 12 regulator-status inputs and the MAX5969A status input to its rail/function and actual signal polarity.
- **Timing:** Early decision.

### Q05. What measurable criteria will choose 1000BASE-T versus 100BASE-T?

**Product-owner question:** Is gigabit Ethernet a customer commitment, or may we recommend 100 Mbps if it meets the operational need at lower overall cost or risk?

- **SOW basis:** Hardware and Target Environment requests a cost/schedule comparison; requirement 1.1 specifies a single 1000BASE-T interface.
- **Engineering details requested:** Identify required link speeds, auto-negotiation/fallback behavior, network compatibility, expected firmware-image/log sizes, and acceptable update/download times. State whether maintaining the candidate PHY is a constraint or part of the trade study.
- **Decision requested:** Agree on the trade-study acceptance criteria, reviewer, and selection deadline. Hardware layout, magnetics, interface timing, driver support, and validation may all affect the comparison; a software-only cost difference should not be assumed.
- **Timing:** Early decision.

### Q06. Which customer control ecosystem must the product integrate with?

**Product-owner question:** Should the first release provide REST or SNMPv3, and which customer platform must it demonstrably work with? This is a major scope and architecture decision.

- **SOW basis:** Requirement 1.2.1 permits either RESTful M&C or SNMP; 1.2.2 and 1.2.3 define their respective minimums.
- **Engineering details requested:** Confirm whether exactly one protocol is sufficient or both are commercially required. Identify existing management software, integration specifications, compatibility constraints, sample client workflows, and access to a representative test system.
- **Acceptance artifacts:** For REST, confirm delivery of a machine-readable [OpenAPI 3.1.1](https://spec.openapis.org/oas/v3.1.1.html) definition. For SNMP, confirm MIB-file delivery, ownership of the enterprise OID namespace, and any mandatory existing MIB compatibility.
- **Timing:** Early decision.

### Q07. What does the customer mean by "HMI," and who supplies it?

**Product-owner question:** Is Connected Development delivering embedded firmware only, or also a browser interface or operator application? Clarifying this prevents a substantial user-interface scope mismatch.

- **SOW basis:** Requirement 1.4.1.7 requires operator download of the event log "via the HMI"; the remaining scope does not explicitly define a UI deliverable.
- **Engineering details requested:** Identify whether the HMI is an existing customer management system, an embedded web UI, a desktop application, or another interface. Specify the required download workflow, file format, permissions, and supported client environment.
- **Boundary to confirm:** If a new UI is expected, identify its screens/functions and acceptance criteria. If not, confirm that a documented API/download capability consumed by customer-supplied software satisfies this requirement.
- **Timing:** Early decision.

### Q08. What is the operating environment and required service availability?

**Product-owner question:** What conditions and downtime expectations must this product support in the field? These define the reliability promise, rather than leaving it to individual component ratings.

- **SOW basis:** Project Overview identifies satellite ground stations; requirements 2.8 and 2.10 specify calibration temperatures and sensor ranges but do not define a complete product operating envelope.
- **Engineering details requested:** Confirm ambient/internal operating temperature limits, expected service life, continuous-operation expectations, network isolation/connectivity, PoE deployment constraints, and any mandated environmental or regulatory qualification.
- **Boundary to confirm:** Are RF signal processing, transmit-chain control, additional remote services, or hardware power-switching controls outside this software scope? Identify any additional customer specifications that take precedence over or supplement the SOW.
- **Timing:** Early decision.

## B. Customer operations, access, and recovery

### Q09. What network provisioning experience should an installer have?

**Product-owner question:** How should a new unit be discovered, addressed, and brought into service without risking loss of access?

- **SOW basis:** Requirements 1.1, 1.3.1.5, and 1.3.3 specify one physical interface, IP-address configuration, and a static factory-reset alias.
- **Engineering details requested:** Define default addressing, IPv4/IPv6 scope, static addressing/DHCP requirements, subnet mask, gateway, DNS if needed, and when changes take effect. Specify how the operator confirms a new address and recovers from invalid or unreachable settings.
- **Deployment detail:** How are multiple factory-default units commissioned on the same network without address conflicts? Are VLAN tagging or network-access controls customer requirements, or explicitly out of scope?
- **Timing:** Definition needed.

### Q10. What identities and permissions should control the product?

**Product-owner question:** Which people and automation systems may monitor, configure, service, reset, or update an LNB? We need the access policy the customer will approve, not simply a technology choice.

- **SOW basis:** Requirements 1.2.2.4-6 require customer-approved REST authentication and RBAC; 1.2.3 requires SNMP USM/VACM and at least 15 groups; 1.3.3.4 separately restricts factory reset.
- **Engineering details requested:** Provide the role-to-operation matrix, local versus external identity model, maximum users/service identities, permission granularity, and credential provisioning/revocation process. For REST, are predefined roles sufficient, or are customer-defined roles required?
- **SNMP-specific detail:** Does "minimum 15 groups" mean configurable group capacity or 15 supplied groups? Identify required security levels, users/views, and confirm the RFC 7630 SHA-512-based authentication protocol, for example `usmHMAC384SHA512AuthProtocol`, for manager interoperability.
- **Timing:** Early decision.

### Q11. Who owns certificates, credentials, and their lifecycle?

**Product-owner question:** How will the customer securely commission and maintain devices over their service life, including on isolated networks?

- **SOW basis:** Requirements 1.2.2.2-4, 1.2.3, and 1.3.3.2 require authenticated/encrypted access but do not define deployment of keys, certificates, or credentials.
- **Engineering details requested:** Specify certificate authority/trust model, device identity, initial credential delivery, server-only versus mutual TLS if applicable, renewal/replacement, and recovery from expired certificates or lost credentials. Confirm TLS-version/cipher policy for the reset interface, which specifies TLS but not a version.
- **Customer policy requested:** Identify applicable password, failed-login, session-expiration, audit, and key-storage policies. These are policy questions, not assertions that a particular identity architecture or additional certification is already required.
- **Timing:** Early decision.

### Q12. What exactly should a factory reset erase or preserve?

**Product-owner question:** What does "return to factory condition" mean for this product, and how do we ensure it does not destroy unit identity or factory calibration?

- **SOW basis:** Requirements 1.3.3, 1.7, 2.8, and 2.9.
- **Engineering details requested:** Supply a reset/preserve matrix for network settings, user identities, certificates/keys, roles, attenuation settings, board configuration, event logs, operating-hours counters, calibration records, model/serial/hardware revision, and firmware images.
- **Access and recovery:** Define the alias IP address/subnet, availability conditions, reset request/confirmation protocol, authorization method, and post-reset access path. Is the alias continuously active, or enabled only under specific service conditions? Confirm that all non-reset operations are inaccessible through that alias.
- **Timing:** Early decision.

### Q13. What does "unmuted operating hours" mean for this LNB?

**Product-owner question:** Which real operating condition should the lifetime-hours counter represent? The answer affects what customers can infer about utilization and equipment life.

- **SOW basis:** Requirement 1.3.1.12 explicitly defines accumulated operating hours as "unmuted operating hours," but no mute command or control signal is otherwise defined.
- **Engineering details requested:** Does a mute state exist? If so, define its hardware mechanism, unit-wide versus per-board scope, triggers, commands, telemetry, startup state, and interaction with alarms. If no mute function is intended, please replace the wording with the correct counting condition.
- **Counter behavior:** Define reporting resolution, persistence interval, allowed loss on abrupt power interruption, reset/service policy, and behavior during initialization, faults, firmware updates, and partial-board operation. Do not equate 20 dB attenuation with mute without an explicit definition.
- **Timing:** Early decision.

### Q14. What should "set gain" and "read gain" mean to an operator?

**Product-owner question:** Should the interface expose attenuation directly, or translate it into LNB gain? A consistent customer-facing definition avoids incorrect link-budget settings.

- **SOW basis:** Requirements 1.3.1.6-7 and 2.6. The command range is 0-20 dB in 1 dB increments; the supplied hardware table supports 0-31.5 dB in 0.5 dB increments.
- **Engineering details requested:** Confirm units, sign convention, per-board addressing, and whether readback means last commanded setting, GPIO state, or measured gain. Is the restricted command range intentional?
- **Behavior requested:** Define invalid-value responses, treatment of unpopulated boards, persistence across restart, and whether the explicit 0 dB power-up/reset default always overrides the last setting. Are coordinated multi-board updates required?
- **Timing:** Definition needed.

### Q15. What constitutes a successful command and acceptable responsiveness?

**Product-owner question:** How quickly should the unit respond to users and automation, and how should they know a requested action actually completed?

- **SOW basis:** Requirement 1.3 lists M&C operations, but does not define response timing, concurrent clients, or transaction semantics.
- **Engineering details requested:** Define maximum command/telemetry response time, simultaneous users/managers, polling load, and priority of competing writes. Distinguish acknowledgement from completion for reboot, reset, calibration, and update.
- **Interface contract:** Specify error reporting for invalid values, insufficient privilege, absent hardware, busy device, stale data, and hardware failure. Identify retry/idempotency requirements for automation and whether updates to related settings must be atomic.
- **Timing:** Definition needed.

## C. Startup, faults, and service continuity

### Q16. When is the unit ready for customer service?

**Product-owner question:** What should the customer see during startup, and when may the product claim to be operational?

- **SOW basis:** Requirement 2.4 changes Yellow to Green when all status inputs are healthy; 2.5 requires SPI initialization; 2.7 starts reference adjustment 5 +/- 1 seconds after power-up/reset.
- **Engineering details requested:** Is Green determined solely by the status pins as written, or must SPI verification and reference adjustment also complete? Specify readiness telemetry, Ethernet availability, maximum boot time, and permitted commands before initialization completes.
- **Failure case:** If a status pin is faulty from power-up, should the LED stay Yellow indefinitely under the stated rule, or eventually show Red? Identify startup grace periods, timeout behavior, and whether startup faults produce the Summary Alarm before Normal state has ever been reached.
- **Timing:** Early decision.

### Q17. What should the unit do when a fault occurs?

**Product-owner question:** Are alarms informational, or should the LNB take protective action to preserve equipment or service?

- **SOW basis:** Requirements 2.2-2.4 and 2.12 define status inputs, LED behavior, and alarms, but do not specify shutdown, muting, reboot, or degraded operation.
- **Engineering details requested:** Provide a response matrix for RF-board faults, regulator faults, PoE status, overtemperature, SPI failure, and reference-adjustment failure. State whether healthy boards continue operating when another board fails, and whether recovery is automatic or requires an operator.
- **Scope boundary:** Confirm whether protective control hardware exists. Should Summary Alarm include only status-pin faults, or also temperature/software-detected failures? Requirement 2.12.1 references 2.1 and 2.3; should its RF-board reference be 2.2?
- **Timing:** Early decision.

### Q18. How should alarms clear, latch, and acknowledge?

**Product-owner question:** Should the customer see only current faults, or also faults that occurred and then recovered? This changes the operational meaning of the reset command.

- **SOW basis:** Requirements 1.3.1.9-11, 2.11, and 2.12 require reset/read operations and thresholds above 105 C and 125 C, but do not specify latching or clearing rules.
- **Engineering details requested:** Define active versus historical/latched state, acknowledgement versus reset, all-source versus per-source reset, and behavior when reset is requested while the underlying cause remains active.
- **Threshold handling:** Specify hysteresis, assertion/clear delays, GPIO debounce, repeated-event suppression, and per-source cause reporting. Confirm that thresholds are fixed or identify who may configure them. A 20 C separation between warning and alarm is not itself clearing hysteresis.
- **Timing:** Definition needed.

### Q19. How should temperature limits work at the edge of sensor capability?

**Product-owner question:** Can we agree on a temperature-monitoring specification that reliably detects the customer's intended warning and protection conditions?

- **SOW basis:** Requirement 2.10 gives an MCP9700A measurement range of -40 to +125 C; 2.12.2 triggers an alarm when any temperature sensor exceeds 125 C.
- **Engineering details requested:** Explain how an above-125 C alarm is to be guaranteed for a sensor specified only through 125 C. Confirm threshold tolerance, sensor accuracy, range validity, and handling of saturated, disconnected, or implausible readings.
- **Conversion constants:** Confirm the MCP9700A transfer function: the SOW states 400 mV at 0 C and 10 mV/C. Provide the approved datasheet revision and any board-level conditioning/calibration that establishes the effective offset; a 100 mV offset difference changes the computed temperature by 10 C.
- **Timing:** Definition needed before measurement/alarm acceptance criteria are frozen.

### Q20. What is the approved behavior when reference adjustment cannot succeed?

**Product-owner question:** Should an LNB with an invalid 100 MHz reference remain available in a degraded state, or be declared unavailable?

- **SOW basis:** Requirement 2.7 sets the HMC472A to 24 dB at startup, starts adjustment at 5 +/- 1 seconds, targets -1 dBm +/- 1 dB, and calls for remeasurement after 1000 +/- 100 ms.
- **Engineering details requested:** Define maximum iterations/elapsed time, attenuation limits/step strategy, reference-input operating range, and the response to missing, unstable, or out-of-range reference power. Does the 1000 ms wait apply after every corrective step?
- **Lifecycle:** Confirm that the algorithm may iterate during one startup adjustment session but must not continuously retune after completion. What happens if the reference becomes invalid later? How is factory calibration allowed to set 12 dB without interference from startup adjustment?
- **Timing:** Definition needed.

### Q21. What reliability behavior is required beyond reporting faults?

**Product-owner question:** What autonomous recovery should the customer expect when software stops responding or power is interrupted?

- **SOW basis:** Requirements 1.3.1.8 and 1.6.5 define remote reboot and update-failure rollback; a general watchdog/recovery policy is not specified.
- **Engineering details requested:** Define allowed outage duration, recovery after brownout/watchdog reset, crash-loop behavior, persistence guarantees, and required reset-cause reporting. Identify any reboot limits or conditions requiring service intervention.
- **Boundary to confirm:** Requirement 1.3.1.3 explicitly says communication fault has "No requirement." Should that exclusion remain unchanged? Optional local link/NTP diagnostics must not be interpreted as a newly required communication-fault alarm without approval.
- **Timing:** Optional scope decision for new recovery/diagnostic commitments; definition needed for existing reboot behavior.

## D. Measurements and factory calibration

### Q22. What exactly must the power measurement performance guarantee cover?

**Product-owner question:** Under which operating conditions will we promise the specified power-reading accuracy to the end customer?

- **SOW basis:** Requirement 1.3.2 specifies at least -33 to +12 dBm, RMS detection, uncertainty no greater than 2.0 dB, and at least four measurements per second.
- **Engineering details requested:** Define the RF measurement reference plane, applicable frequency/temperature/waveform ranges, error metric and confidence basis, and whether the 2.0 dB limit includes coupler tolerance, analog conditioning, ADC error, and calibration-instrument uncertainty.
- **Range coverage:** Calibration in 2.8 uses -30, -20, and +13 dBm. How is accuracy at -33 dBm verified, below the lowest calibration point? Specify behavior and telemetry validity when the signal is below/above the supported measurement range.
- **Timing:** Early decision for the product performance commitment.

### Q23. What measurement freshness and channel coverage must we demonstrate?

**Product-owner question:** Does "four measurements per second" mean each customer's power reading is refreshed at that rate, or something broader?

- **SOW basis:** Requirement 1.3.2.4 appears in the IF-power subsection; requirements 2.8-2.10 define the other analog channels without explicit rates.
- **Engineering details requested:** Confirm whether the rate applies per installed IF channel and whether it also applies to reference power and temperature. Define averaging/filtering, maximum sample age, timestamps if required, and sustained performance during log download and other network activity.
- **Channel inventory:** At four RF boards, the text implies four IF-power outputs, one reference-power output, five MCP9700A outputs, and four ADL5906 temperature outputs: **14 analog measurements**, subject to hardware confirmation. Identify alarm-detection latency separately from display/polling refresh rate.
- **Timing:** Definition needed.

### Q24. What factory IF-calibration procedure will be authoritative?

**Product-owner question:** Can we agree on a repeatable factory process that meets accuracy targets without leaving production time and test cost undefined?

- **SOW basis:** Requirement 2.8 lists -30/-20/+13 dBm; 3.5/4.5/5.5 GHz; -40/+23/+55 C; a modulated IF signal; averaged frequency coefficients and temperature interpolation.
- **Engineering details requested:** Confirm the full test matrix: all three powers at all three frequencies and temperatures would produce 27 points per detector, or 108 for four boards before repeats. Specify waveform/modulation, bandwidth/crest factor, settling times, number of samples, and instrument tolerances.
- **Calculation and workflow:** Specify fitting of three power points into slope/intercept, frequency averaging, temperature interpolation and out-of-calibration-range policy. Which temperature measurement selects the correction? Define invalid-calibration rejection, record format, persistence, revisioning, recalibration permissions, and the factory Ethernet commands needed.
- **Timing:** Definition needed before test architecture and factory workflow are baselined.

### Q25. How is the coupler correction represented in calibration?

**Product-owner question:** How do we ensure the customer receives actual IF output power, rather than detector-input power or an accidentally double-corrected value?

- **SOW basis:** Requirement 2.8.6 says, for reference only, that a 20 dB coupler makes actual IF output power 20 dB higher than detected power.
- **Engineering details requested:** Provide a reference-plane diagram and identify where calibration power levels are applied/measured. Does calibration already relate detector voltage to actual output power, thereby absorbing the coupler factor into the fitted intercept?
- **Decision requested:** Confirm whether a separate coupling correction is needed, its approved value/tolerance/frequency dependence, and whether it is measured per unit. Do not add a separate 20 dB correction if the end-to-end calibration already incorporates it.
- **Timing:** Definition needed.

### Q26. What is the complete 100 MHz reference calibration signal path?

**Product-owner question:** Can the hardware team confirm the reference-power calibration model so the startup adjustment targets the correct physical power level?

- **SOW basis:** Requirement 2.9.5 sets the attenuator to 12 dB and maps external calibration inputs of -18/+10 dBm to -22/+6 dBm at the detector.
- **Diagram evidence and engineering details requested:** Figure 2 shows the input passing through a variable attenuator, LNA, and filter before the power detector, followed by distribution to the synthesizers. Identify the external injection point, all intervening gain/loss, and the detector reference plane. The stated endpoint pairs imply a net loss of 4 dB. With 12 dB attenuation, an additional net gain of 8 dB elsewhere in the path would reconcile them; the illustrated LNA makes this plausible, but its gain is not specified.
- **Clarification requested:** Confirm the LNA/filter net gain/loss and whether these values establish the intended calibration mapping. Supply the approved conversion example, ADC-to-voltage convention, fitting/interpolation policy, accuracy target, and whether the single 23 C calibration is sufficient over the product operating range.
- **Timing:** Definition needed; the numbers alone do not prove a SOW arithmetic error.

### Q27. How will the specified ADC voltage domains be implemented?

**Product-owner question:** Can we confirm that the selected hardware supports the measurement requirements before committing to firmware and accuracy acceptance?

- **SOW basis:** Requirements 2.8-2.10 specify 4 V, 1.8 V, and 2.5 V ADC references, with IF detector outputs up to 3.8 V.
- **Engineering details requested:** Identify internal/external ADCs, simultaneous versus switched references, permitted input voltages, dividers/buffers, reference tolerances, resolution, source impedance, settling requirements, and calibration of board-level scaling.
- **Component identity:** Confirm whether `ADL5906A` in 2.10 means the temperature output of the `ADL5906` in 2.8, or another device. Provide actual output-pin/channel mappings and the approved 1400 mV at 25 C, 4.8 mV/C conversion basis.
- **Timing:** Early decision for hardware feasibility and pinout review.

### Q28. When will the RF register configurations be available and approved?

**Product-owner question:** Can we assign an owner and delivery date to the missing RF settings? Software can build the drivers before then, but successful RF bring-up and acceptance depend on these inputs.

- **SOW basis:** Requirement 2.5 specifies fixed firmware register values; Appendices A and B remain TBD and refer only to RF boards 1 and 2 despite support for up to four.
- **Engineering details requested:** Supply approved ADF4368 and ADAR2001 register maps/values for every required board variant, programming order, delays, reference/LO assumptions, and chip-revision dependencies. Identify read-only, reserved, write-only, self-clearing, and side-effect registers.
- **Verification boundary:** Define what "write and read all registers" means for registers that cannot safely support both operations; specify valid readback masks, mismatch handling, and lock/readiness criteria. Confirm that no Ethernet register-tuning interface is required, including in factory mode.
- **Timing:** Early decision on ownership/dates; definition needed before RF integration.

### Q29. What manufacturing identity format and service policy should we implement?

**Product-owner question:** How should model, serial number, and hardware revision be provisioned and corrected throughout the unit's life?

- **SOW basis:** Requirement 1.7 allows alphanumeric values up to 32 characters, leading-zero padding, and dashes; 1.3.1.13-16 requires identity and firmware-version readout.
- **Engineering details requested:** Provide exact field formats and examples, padding width, allowed character case, empty/invalid-value handling, and whether 32 characters excludes storage terminators. Define permissions and whether factory values may be rewritten during rework or service; the SOW does not state write-once behavior.
- **Traceability:** Specify identity persistence across reset/update/board replacement, firmware-version syntax, and whether model/hardware compatibility must be enforced during updates. Who owns serial-number allocation and duplicate prevention?
- **Timing:** Definition needed.

## E. Event history, diagnostics, and time

### Q30. What information must a state record preserve?

**Product-owner question:** What should a service engineer or customer be able to reconstruct after an incident?

- **SOW basis:** Requirement 1.4 requires before/after state records for operator changes, M&C changes, firmware updates, and warnings/alarms/faults.
- **Engineering details requested:** Define the state snapshot fields, triggering event categories, source-board identifiers, cause, old/new values, actor identity, and operation outcome. Clarify whether operator and M&C changes are distinct sources or overlapping descriptions.
- **Before/after interpretation:** For spontaneous faults, should "before" mean the most recent sampled state in a rolling buffer rather than a record taken before an unpredictable event? Specify treatment of abrupt power loss, unsuccessful commands, reboot boundaries, and firmware changes.
- **Timing:** Definition needed.

### Q31. How much event history must the product retain?

**Product-owner question:** What service-history retention do we promise, and what should happen when storage fills?

- **SOW basis:** Requirement 1.4 describes continual logging and semi-permanent persistence but provides no retention/capacity figure.
- **Engineering details requested:** Specify minimum record count or time-based retention with an assumed event rate, persistence across outages/reboots, overwrite policy, storage-full behavior, and wear/endurance expectations over the service life.
- **Export and format:** Is RFC 5424 conformance mandatory or preferred, as "should" suggests? Define required timestamp/structured-data fields, export size/format, permissions, consistency during download, and whether any remote syslog streaming is required or out of scope.
- **Timing:** Early decision for storage sizing; optional scope decision for remote streaming.

### Q32. What time accuracy is required when the network clock is unavailable?

**Product-owner question:** How should event history remain trustworthy during first startup, loss of time service, and long network outages?

- **SOW basis:** Requirement 1.5 specifies operator-selected NTP, v4 objective/v3 threshold, time error no greater than 1 second, and precision no greater than 1 millisecond; 1.4.1.8 requires millisecond timestamps.
- **Engineering details requested:** Define when accuracy must be achieved, allowed synchronization time, NTP-outage holdover duration, power-off retention, server configuration/failover, and any time-server authentication requirement. Confirm whether NTPv3 alone satisfies acceptance or NTPv4 is required for delivery.
- **Timestamp semantics:** Define UTC/offset representation, unsynchronized-time indication, clock-step handling, and event ordering when time moves backward. Clock precision/resolution and absolute accuracy should have separate test criteria.
- **Timing:** Definition needed; holdover requirements may affect oscillator/backup hardware.

### Q33. What is the UART's acceptance role and output contract?

**Product-owner question:** Is UART output an internal engineering aid, a production-test interface, or an interface the customer expects to rely on after delivery?

- **SOW basis:** Requirement 2.13 specifies 115200 baud, 8 data bits, no parity, one stop bit; preceding sections require register dumps, state changes, analog readings, warnings, and alarms.
- **Engineering details requested:** Define periodic-reading rates, raw versus engineering-unit values, line format, timestamps, channel naming, voltage/interface levels, and whether flow control is available. At 8N1, 115200 baud provides approximately 11,520 data bytes/second before formatting/processing overhead.
- **Performance and scope:** Specify output behavior under bursts/overflow and whether UART remains active in production. Confirm whether output-only is sufficient; an interactive UART command interface is not specified. Numerous requirements point to 2.12.3 for UART information, although the configuration is in 2.13; confirm the intended reference.
- **Timing:** Definition needed.

## F. Software updates and field lifecycle

### Q34. Where does responsibility for fleet-wide updates begin and end?

**Product-owner question:** Are we supplying an automation-ready device interface, a sample update script, or a fleet-management system? These are materially different deliverables.

- **SOW basis:** Requirements 1.6.1-3 require Ethernet updates, unattended updates across the population, and no special/Seller-specific tools.
- **Engineering details requested:** Identify the customer's orchestration platform, expected fleet size/concurrency, standard tools/protocols allowed, required example automation, progress/status reporting, timeout/retry behavior, and acceptable interruption of M&C/RF service.
- **Scope of "all software":** Identify every updateable component, including application cores, bootloader/recovery code, and any other firmware-bearing devices. If SNMP is selected for M&C, define the separate image-transfer mechanism if one is needed.
- **Timing:** Early decision.

### Q35. What establishes that a firmware image is authorized?

**Product-owner question:** Who may authorize software for this product, and who will retain that authority after handover?

- **SOW basis:** Requirement 1.6.4 asks for authentication using SHA-2/SHA-3 hashes or customer-approved digital signatures.
- **Engineering details requested:** Approve the authenticity/integrity scheme, signing authority, verification-key provisioning, algorithm/key sizes, image manifest/compatibility checks, key rotation/revocation, and responsibility for signing release images after transfer.
- **Important distinction:** An unkeyed hash alone does not establish publisher identity. Confirm whether authenticated update delivery plus a hash is acceptable, or a signed image is required. Separately decide whether boot-time image authentication/secure boot is required; it is not automatically equivalent to update-time verification.
- **Timing:** Early decision.

### Q36. What failures must automatic rollback recover from?

**Product-owner question:** What recovery promise should we make when an unattended update fails, particularly at a remote or inaccessible site?

- **SOW basis:** Requirement 1.6.5 requires automatic rollback to the most recent functioning version.
- **Engineering details requested:** Define update-failure conditions: interrupted transfer, failed verification, power loss during programming, boot failure, crash loop, or functional failure after boot. Define the health-confirmation criteria, observation period, maximum recovery time, and retained diagnostic evidence.
- **Recovery coverage:** Must the process tolerate bootloader updates and migration of persistent configuration/calibration data? Identify expected behavior if no valid fallback remains. A/B images are one possible design, not an architecture mandated by the SOW; storage and bootloader strategy need feasibility confirmation.
- **Timing:** Early decision.

### Q37. How far back must a customer be able to roll software?

**Product-owner question:** Should a customer be able to restore only the immediately previous release, or select any approved historical version?

- **SOW basis:** Requirement 1.6.6 allows remote rollback to "prior versions" without specifying depth or storage location.
- **Engineering details requested:** Define eligible versions, local versus externally supplied image availability, configuration-schema compatibility, rollback of settings, and permissions. Must images be retained on the unit, or may the customer re-upload an older signed release?
- **Policy conflict to resolve:** Are versions with known critical defects prohibited from reinstallation, and how should that policy coexist with the requested rollback capability? Confirm whether any anti-rollback mechanism is required rather than assuming it.
- **Timing:** Definition needed before update/storage design is frozen.

### Q38. What must survive every lifecycle operation?

**Product-owner question:** Can we approve one data-preservation policy covering restart, reset, update, rollback, and service? This prevents avoidable field-data loss and inconsistent recovery behavior.

- **SOW basis:** Requirements 1.3.1.12-16, 1.3.3, 1.4, 1.6, 1.7, 2.8, and 2.9 collectively depend on persistent identity, history, settings, and calibration.
- **Engineering details requested:** Provide one matrix listing each data class, default, owner, write permissions, preservation rules, allowed loss after power failure, and migration behavior. Include operating hours, board configuration, users/keys, identity, calibration, logs, network settings, and version metadata.
- **Hardware input:** Confirm available internal/external nonvolatile storage and service-life/write-endurance constraints. Corrupted or missing calibration/configuration needs an explicitly approved error state, not an invented silent default.
- **Timing:** Definition needed; overlaps Q12 but covers the full lifecycle, not only factory reset.

## G. Verification, handover, and delivery commitments

### Q39. How will "50%, 90%, and 100% implemented" be measured?

**Product-owner question:** Can we agree on objective milestone evidence before development begins? This protects both the customer's acceptance expectations and the project's payment schedule.

- **SOW basis:** Milestones require at least 50% and 90% implementation on manufacturer development boards, followed by 100% on target hardware.
- **Engineering details requested:** Agree on the requirement inventory/traceability matrix, counting or weighting method, and the difference between implemented, demonstrated, and fully verified. Identify which requirements may use simulated inputs on evaluation boards and what evidence is required for each demonstration.
- **Baseline control:** Should approved answers in this document feed a signed PRD/acceptance baseline? Specify handling of new requirements, approved exclusions, and incomplete customer inputs when measuring completion.
- **Timing:** Early decision.

### Q40. What test environment and equipment will each party provide?

**Product-owner question:** Who is responsible for making comprehensive automated verification physically possible and repeatable?

- **SOW basis:** Test requires automated coverage of all requirements, a verification report, and final verification on AmpliTech target boards.
- **Engineering details requested:** Allocate supply/control of evaluation boards, target units, fixtures, PoE/network equipment, RF sources, calibrated power instruments, thermal chamber, debug probes, and reference clocks. Define access to instrument automation APIs and fixture designs.
- **Coverage and limits:** Agree on configuration/board-revision coverage, four-board testing, performance uncertainty, fault injection, power-interruption/update tests, and any manual setup or inspection exceptions to "automated" coverage. Identify witness tests and where each test will occur.
- **Timing:** Early decision for cost and schedule.

### Q41. What is the committed target-board delivery and contingency plan?

**Product-owner question:** Can we align hardware availability with the promised software delivery date and explicitly plan for integration risk?

- **SOW basis:** Scope of Work tentatively says target boards arrive 10 weeks after EDC. The schedule lists a 60-day task finishing February 11, 2027, Demo 3 on February 18, and project completion on March 4, with November 9, 2026 as tentative EDC.
- **Engineering details requested:** Confirm the working/holiday calendar, actual EDC, committed board arrival date, number/revision/completeness of units, prerequisite hardware validation, and support turnaround for board defects or respins. Confirm whether the 60 days are working days under that calendar rather than converting them to calendar weeks by assumption.
- **Contingency:** Agree how delivery changes if boards or register tables are late or defective. There are only five scheduled working days for Demo 3 after board arrival; how much integration contingency is available, and when must final-test notice be issued to meet the five-business-day requirement?
- **Timing:** Early decision.

### Q42. What documentation and reproducibility standard will satisfy handover?

**Product-owner question:** What must AmpliTech be able to build, install, test, and maintain independently at the end of the project?

- **SOW basis:** SDRLs 01-10 require functional/interface documents, compiled software, a full-history private Git repository, build/run instructions, setup scripts, dependency manifests, and test source/tool documentation.
- **Engineering details requested:** Confirm supported host OS, toolchain/SDK versions, licensed-tool requirements, debug/programming interfaces, deployment/recovery procedures, and whether a clean-machine rebuild demonstration is required. Is bit-for-bit reproducibility required, or a documented functional rebuild?
- **Artifact acceptance:** Define delivery formats for OpenAPI/MIB files as applicable, calibration/test procedures, release images/manifests, repository transfer and retained branches/tags, and the review criteria for each SDRL. Should "final" SDRL 03/04 at the 50% milestone permit controlled revisions after target-board integration?
- **Timing:** Definition needed before preliminary design review.

### Q43. What third-party software and licensing commitments are acceptable?

**Product-owner question:** May we use commercial or permissively licensed components where they reduce risk, and who will fund and maintain those dependencies?

- **SOW basis:** Contract requires an inventory of pre-existing/third-party code with exact versions/licenses, agreement on retained pre-existing IP, and written consent before introducing restrictive licenses such as GPL/AGPL.
- **Engineering details requested:** Confirm acceptable license categories, commercial stack/tool budgets, royalty/redistribution terms, source-access expectations, maintenance rights after transfer, and approval timing. Exact licenses must be checked for the selected component/version, not inferred from a library's name.
- **Inventory format:** Is a version/license inventory sufficient, or is a machine-readable SBOM format such as SPDX or CycloneDX required? Agree on pre-existing-code exclusions and the customer's usable build/distribution rights before selecting dependencies.
- **Timing:** Early decision.

### Q44. What does acceptance start, and what support follows it?

**Product-owner question:** Can we align delivery acceptance, defect remediation, and ongoing ownership so the product has a clear transition into service?

- **SOW basis:** Deliverables allow five business days for review/acceptance; Contract provides a 30-day post-handover warranty for no-cost repair of bugs identified by AmpliTech.
- **Engineering details requested:** Define the handover/warranty start event, defect versus change classification, severity levels, reporting channel, response/remediation expectations, and acceptance/retest process. How are unresolved defects, third-party defects, and hardware-caused failures handled?
- **Lifecycle boundary:** Is maintenance or vulnerability remediation beyond the warranty a separate engagement? Who owns future releases, signing operations, dependency updates, and factory-test maintenance after handover?
- **Timing:** Definition needed.

## H. Diagram-specific product commitments

### Q45. Which RF/IF bands and physical interfaces define the saleable product?

**Product-owner question:** Can we confirm the channel frequency plan and customer-facing connections that Marketing will promise? The diagrams add important product specifications not fully stated in the numbered software requirements.

- **Diagram evidence:** Figure 1 labels a Q-band LNB with a 37.5-41.9 GHz RF input through a WR22 interface, two RF/synthesizer enclosures with LOs of 34.1 GHz and 36.3 GHz, and two 3.4-5.6 GHz N-type IF outputs. It also labels an RJ45 Ethernet/control/DC-power connection and an SMA 100 MHz reference input. Figure 2 labels its reference input SMP, which may be an internal board connection rather than the external connector.
- **Engineering details requested:** Confirm the RF sub-band assigned to each channel, LO values, and required calibrated/guaranteed IF band. The calibration frequencies in requirement 2.8 are 3.5, 4.5, and 5.5 GHz; are accuracy and operation also required at the diagram's 3.4 and 5.6 GHz endpoints? Confirm external versus internal connector definitions so SMA/SMP references are not treated as a contradiction without the assembly context.
- **Control boundary:** Figure 1 also shows an attenuator in the shared UltraLNA module, upstream of the channel split. Is that a fixed hardware element outside software scope, or does it require an additional control/telemetry function beyond the per-board HMC425A attenuators? If drawings and prose differ, identify which approved source governs.
- **Timing:** Early decision for the product baseline; definition needed for RF acceptance and control scope.

## Response record

Use the question IDs above to preserve traceability as decisions move into the PRD. Open items should carry an owner and date, not an assumed engineering answer.

| Question ID | Decision / supporting artifact | Accountable owner | End-customer approval, if needed | Due date / status |
|---|---|---|---|---|
| Q01 | Pending confirmation of exact Infineon ordering code, including D | To be assigned | To be confirmed | Open |

This draft deliberately does not convert optional capabilities into requirements, reinterpret "communication fault: No requirement" as a defect, prescribe an A/B boot architecture, or treat unverified first-pass component descriptions as established facts. Approved responses, rather than the analytical notes themselves, should establish the next requirements baseline.
