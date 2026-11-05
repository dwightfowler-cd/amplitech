# Primary Technical Source (PTS) — Run 02

**Derived from:** `union-sow_rev02.md` (RFP AGMDC0011, Union Embedded Software Development — LNB Command & Control)
**Purpose:** Exhaustive, non-compressive extraction of every technical fact, requirement, constraint, and implied need in the source SOW, expanded with primary reference links, datasheet-derived specifications, and Connected Development (CD) analyst commentary. This is raw input material for a future Product Requirements Document (PRD) — not a summary.
**Author:** Bob (GitHub Copilot CLI), first pass
**Status:** DRAFT — Run 02, pass 1 corrections (Claude, 2026-11-03), applied to Bob's run 01.
**Run 02 pass 1 scope:** (1) every claim run 01 based on "general industry knowledge" or other unsourced recall was checked against a primary or manufacturer source and corrected, sourced, or withdrawn; (2) ADAR2001 description fixed; (3) MCP9700A offset/slope fixed; (4) ADL5904 calibration flag (gap #7) withdrawn; (5) RF-board population model in §1 rewritten (run 01's claim of pull-up detection was wrong); (6) diagram-availability notes cleaned up. Changed text is marked ✅ CORRECTED or 📎 SOURCED. **Not yet addressed in this pass:** section-number scheme, party structure (Union/AmpliTech), and the other items in the review plan.

> **Legend for inline commentary**
> - `> 🧩 CD-NOTE:` — an inference, derived requirement, or "they'll also need X" observation not explicitly stated in the SOW but implied by it.
> - `> ⚠️ CD-FLAG:` — an ambiguity, contradiction, missing spec, or risk item that should be raised back to Union/AmpliTech.
> - `> 🔗 CD-REF:` — a primary source link added by CD (datasheet, standard, RFC, spec) not already hyperlinked in the SOW, or a corrected/verified version of one that was.

---

## 0. Document Context & Parties

- **Customer chain:** End Customer → AmpliTech (prime, hardware OEM) → Union Embedded Software Development (SOW author, Union is itself presumably a subcontractor/vendor relationship manager) → Connected Development (proposed software vendor, "the Seller"/"the vendor" in the SOW text).
- **RFP number:** AGMDC0011.
- **Document title:** "Statement of Work," authored under "Union Embedded Software Development."
- **Product being specified:** Embedded command-and-control (C2) software for a **Low Noise Block (LNB) Downconverter**, used in **satellite communications ground stations**.
- **Deliverable type:** Software architecture, design, implementation, documentation, and test for the LNB's embedded processor — NOT the hardware itself (hardware is AmpliTech's responsibility; CD supports schematic/pinout review only).

> 🧩 CD-NOTE: Because this is explicitly a *ground station* LNB (receive-only downconverter block) rather than a BUC/transceiver, the PRD should state up front that there is **no RF transmit path**, no uplink modulation, and no high-power amplifier control — scope is limited to LO synthesis, gain/attenuation control, power detection, and health monitoring/reporting of the downconversion chain itself.

> 🧩 CD-NOTE: Multiple named entities exist (Union, AmpliTech, end customer). The PRD should clarify a RACI: who approves RBAC schemes, authentication schemes, and alarm/warning thresholds ("end customer must approve") vs. who accepts deliverables (AmpliTech, 5 business days) vs. who writes requirements (Union). This is a 3-tier approval chain that affects schedule risk.

---

## 1. System Architecture Overview

- Two block diagrams are included in the SOW: Figure 1, "LNB Block Diagram" (`media/image1.png`), and Figure 2, "Processor Board" (`media/image2.png`). Both have been extracted from `Union-software-SOW_Rev02.docx` and rendered. The Word file also embeds the editable Visio sources (`word/embeddings/Microsoft_Visio_Drawing.vsdx` and `Microsoft_Visio_Drawing1.vsdx`); their text matches the rendered figures.
  - **Figure 1 (Q-band LNB):** RF input 37.5–41.9 GHz via WR22 waveguide → UltraLNA module (LNA, LNA, attenuator, image-reject filter, splitter) → two channel enclosures, each an "RF and Synthesizer Board" (Channel 1 LO 34.1 GHz, Channel 2 LO 36.3 GHz) → two IF outputs, 3.4–5.6 GHz, N-type. A "Power, Control, Reference Board" connects to Ethernet/control/status/DC power (RJ45), the 100 MHz reference input (SMA), the LED, and DC power for the UltraLNA module.
  - **Figure 2 (Power, Control, Reference Frequency Board):** processor with Ethernet MAC → Ethernet PHY → PoE → RJ45 side; voltage regulators; per-channel connections labeled "DC Power, SPI Bus, Atten Ctrl" for Ch 1 and Ch 2; LED control; 100 MHz reference path: input (SMP) → variable attenuator → LNA → filter → power detector → splitter → "100 MHz to Synth1" and "100 MHz to Synth2".
  > 🧩 CD-NOTE: Both figures show exactly **two** RF channels, while the SOW text repeatedly says "up to 4 RF boards." Whether four-channel operation must be implemented and demonstrated, or only architected, is open (questions-4-union.md Q03).
  > 🧩 CD-NOTE: The frequency plan in Figure 1 is internally consistent: 37.5–39.7 GHz minus 34.1 GHz and 39.7–41.9 GHz minus 36.3 GHz both give 3.4–5.6 GHz IF. Each LO divided by 4 (ADAR2001 is a 4× multiplier, §2.5) gives 8.525 GHz and 9.075 GHz, inside both the ADF4368 output range and the ADAR2001 input range (§2.5).
- **Top-level interface summary (explicitly stated):**
  - End-customer command & control interface: **Ethernet** (single physical interface, see §2).
  - Internal component interface: **SPI** (synthesizer and multiplier register access).
  - **GPIO inputs**: status signals from RF boards and power regulators.
  - **GPIO outputs**: LED control, step-attenuator control bits.
  - **Analog inputs** (via ADC): IF power detectors, 100 MHz reference power detector, temperature sensors.
  - **UART output**: board-level test/engineering console, one-way (output only), 115200/8/N/1.

> ✅ CORRECTED (run 02) — RF-board population: The SOW states "up to 4 RF boards" at every RF subsystem but never states how the firmware learns how many boards are installed. **The status pins cannot answer this.** SOW 2.2.3 deliberately pulls each status input high so that "status is high when RF boards are not connected" — an absent board and a healthy board read identically. (Run 01 claimed presence was detectable from the pull-ups; that was wrong and contradicted gap #15.)
> 🧩 CD-NOTE — proposed detection model (for confirmation with AmpliTech, Q03):
>   1. **Configured population is authoritative.** The installed board count/slots is a per-unit configuration value, tied to the LNB model number set at test (SOW 1.7), not inferred at run time.
>   2. **SPI identity probe corroborates it at boot.** For each configured slot, read the ADF4368 identity registers and do a scratchpad write/read-back over SPI. ADI's no-OS driver header defines an expected chip-type value `ADF4368_CHIP_TYPE` = 0x06, product-ID registers at 0x0004/0x0005, and a scratchpad at register 0x000A. SOW 2.5.1.2 already requires every register to be read back, so the SPI read path (SDO) must exist. Source: https://analogdevicesinc.github.io/no-OS/doxygen/adf4368_8h_source.html (exact datasheet register addresses to be confirmed against ADF4368 Rev. 0, "Register Maps" section).
>   3. **Mismatch is a fault, not a reconfiguration.** A configured slot that fails the probe, or a probe that answers in an unconfigured slot, raises a defined fault condition. Which alarm/LED state that produces is a customer decision (Q16/Q17).
>   A dedicated presence pin or ID EEPROM per board would be cleaner, but nothing in the SOW or Figure 2 shows one; that is a hardware question for the pinout review.
> 🧩 CD-NOTE: A clean internal software architecture implied by the SOW's structure: (1) Ethernet/M&C protocol stack (REST or SNMPv3) → (2) a device/business logic layer (alarms, warnings, event recorder, calibration, state machine) → (3) hardware abstraction layer (SPI driver, GPIO driver, ADC driver, UART logger) → (4) per-RF-board instance objects (1..N, N = configured population) each owning its own ADF4368, ADAR2001, HMC425A, ADL5906, MCP9700A. This maps directly to a PRD "Software Architecture" section.


---

## 2. Hardware & Target Environment

### 2.1 Processor — Infineon XMC7200-E272K8384AA (candidate)

- SOW states: *"The candidate processor is Infineon XMC7200-E272K8384AA... Alternate processors can be considered if all requirements defined in this document are met."*
- Primary datasheet/product link (from SOW): https://www.infineon.com/part/XMC7200D-E272K8384AA
  > 🔗 CD-REF: Verified live — note the **exact part family string on Infineon's site is "XMC7200D-E272K8384AA"** (with a "D"), while the SOW's prose calls it "XMC7200-E272K8384AA" (no "D"). This should be reconciled/confirmed with Union — it may simply be shorthand, but part-number precision matters for BOM/procurement and for pulling the correct register map / reference manual.
  > ⚠️ CD-FLAG: Confirm exact orderable part number (XMC7200D-E272K8384AA) before procurement/BSP selection.
- **Key specs pulled directly from Infineon's product page** (these were NOT in the SOW text and are added here as primary technical facts the PRD will need):
  - CPU: **Dual Arm Cortex-M7** (primary cores) + **Secondary core: Arm Cortex-M0+**
  - Max frequency: **350 MHz**
  - Flash / Program memory: **8384 KByte**
  - SRAM (incl. cache): **1024 KByte**
  - A/D input lines: **99 channels**
  - Timed I/O (GPIO/timer) channels: **102**
  - Serial I/O interfaces: **14** total, types: **UART, I²C, I²S, SPI**
  - Ethernet: product page text truncated ("10/103"); **not relied on**. The KIT_XMC72_EVK guide (project file, Rev. *D, 2025-05-16) states the XMC7200D supports Gigabit Ethernet and that the kit wires the MCU's **ETH1** port to its PHY in **RGMII** mode, set by default for 10/100 Mbps, with a board rework required for 1000 Mbps. The number of MAC instances ("2×") is from the product page and has not been re-verified.
  - CAN nodes: **10**
  - DMA: **3 DMA controllers, 216 channels total**
  - Crypto peripheral: **Yes** (hardware crypto accelerator — directly relevant to SOW's firmware-signing/authentication and TLS 1.3 requirements, see §3 and §7.6)
  - FPU: **Yes** (hardware floating point — relevant for power/temperature calibration math, dB/log conversions)
  - On-chip voltage regulator: **Yes**
  - Real-Time Clock: **Yes** (directly relevant to NTP/time-keeping requirement, §7.5)
  - Watchdog timer + oscillator watchdog: **Yes**
  - Package: **PG-LFBGA-272** (272-ball BGA)
  - Operating temperature: **-40°C to +125°C** (industrial grade — matches the LNB's outdoor/ground-station deployment environment and the -40°C calibration point used for RF detectors, §9.8)
  - I/O voltage range: **2.7 V to 5.5 V**
  - Six power modes (relevant to any low-power/standby requirements, though none explicitly stated in SOW)

  > 🧩 CD-NOTE: Per the Infineon product page the device lists **2× Ethernet** (count not re-verified, see above). The SOW requires one external port (Req 1.1), so any second MAC is unused unless AmpliTech's board connects it. Figure 2 shows a single MAC→PHY path.
  > 🧩 CD-NOTE: With **99 ADC channels** and **102 timed I/O (GPIO) channels** available on the host MCU, the processor has enormous I/O headroom relative to the 4-RF-board maximum design point. The PRD's pin budget should be computed explicitly (see §9 "Pin/Channel Budget Analysis" below) to confirm this processor choice is not a bottleneck even at the 4-board maximum.
  > 🧩 CD-NOTE: Built-in hardware crypto + Cortex-M7 with FPU strongly supports running a TLS 1.3 stack (§3.2.1.3) and SHA-2/SHA-3 firmware signature verification (§7.6.4) without a companion secure element, but the PRD should explicitly decide whether a hardware security module / secure boot root-of-trust is required for firmware authentication, since the SOW only specifies the hashing/signing *algorithm family*, not a secure-boot architecture.
  > ⚠️ CD-FLAG: "DSP Functionality: Yes" is listed on the product page but the SOW makes no DSP-related requirement. Likely irrelevant to this project (no signal processing of RF/IF waveforms is requested — only detector *voltage* reads) but worth a one-line confirmation in the PRD that no DSP firmware is in scope.

### 2.2 Ethernet PHY — Texas Instruments DP83869HMRGZR (candidate)

- SOW link: https://www.ti.com/product/DP83869HM/part-details/DP83869HMRGZR
- Verified via TI: **"DP83869HM High Immunity 10/100/1000 Ethernet Physical Layer Transceiver With Copper and Fiber Interface"** — datasheet currently at Rev. E.
  > 🔗 CD-REF: Direct datasheet PDF: https://www.ti.com/lit/gpn/DP83869HM (and HTML viewer: https://www.ti.com/document-viewer/DP83869HM/datasheet)
  > 🧩 CD-NOTE: Part name confirms **both copper (RJ45/magnetics) and fiber (SGMII/SFP-style) interface support** at the PHY level. The SOW only discusses a copper 1000 Base-T / 100 Base-T interface — the fiber capability of this specific PHY appears unused in this design. PRD should confirm copper-only is in scope and that no SFP/fiber path exists on the target board.
  > ✅ CORRECTED (run 02): Run 01 asserted that "High Immunity" *typically denotes* surge/EMI/ESD robustness. That was an unsourced inference and is withdrawn. What the TI product title supports is only that the part is marketed as high-immunity with copper and fiber interfaces; any robustness rating must come from the DP83869HM datasheet. The software observation stands on its own: the SOW has no requirement to report Ethernet link-up/link-down or PHY faults (gap #17).
  > ⚠️ CD-FLAG: The SOW does not specify a software requirement to detect/report **Ethernet link-down/link-up events** or **PHY fault conditions** to the event log or UART, despite dedicating this much hardware attention to PHY robustness. This looks like a gap — recommend adding to PRD as an inferred requirement.

### 2.3 PoE Controller — Analog Devices MAX5969A (candidate)

- SOW link: https://www.analog.com/en/products/max5969a.html
  > ✅ CORRECTED (run 02): Run 01 described this part "from general industry knowledge" (PoE+ class 4, ~25.5 W). That description is withdrawn. From the MAX5969A/MAX5969B datasheet (Maxim 19-5008, https://www.analog.com/media/en/technical-documentation/data-sheets/MAX5969A-MAX5969B.pdf):
  >   - IEEE 802.3af/at powered-device (PD) interface controller with integrated isolation MOSFET; supports 2-event classification and PoE classes 0 to 5.
  >   - Inrush current limit 180 mA max; normal-operation current limit 720–880 mA; 100 V input absolute maximum; −40 to +85 °C.
  >   - Outputs: open-drain **power-good (PG)**, and **2EC**, which asserts low when powered by a Type 2 (802.3at) PSE (also used for wall-adapter detection).
  >   - **UVLO differs by variant:** MAX5969A has a *legacy* 36 V UVLO; MAX5969B has the *IEEE 802.3af/at-compliant* 40 V UVLO.
  > ⚠️ CD-FLAG (new, hardware — not a software item): UVLO (undervoltage lockout) is the input-voltage threshold below which the PD controller keeps its isolation switch off, so the board is not powered until the PoE voltage is high enough. The SOW's References cite **IEEE 802.3at-2009**, and the SOW names the **MAX5969A**. The MAX5969A/B data sheet's feature list says: *"Legacy UVLO at 36V (MAX5969A)"* and *"IEEE 802.3af/at-Compliant, 40V UVLO (MAX5969B)."* In other words, the manufacturer labels the **B** variant's UVLO, not the A's, as 802.3af/at-compliant. Question for AmpliTech: is the MAX5969A (legacy 36 V UVLO) intentional, given the 802.3at reference?
  > ⚠️ CD-FLAG (new): SOW 2.3.2 lists "one status signal from the POE controller" without naming the pin. The datasheet offers two candidates (PG and 2EC). Confirm which, and how it reaches the MCU's GPIO domain.
  > 🧩 CD-NOTE: The SOW already defines one software-visible status signal from the "POE controller, MAX5969A" (§9.1 below, open-drain, pull-up to 3.3V, active-low fault) — so basic PoE fault monitoring IS in scope. The flag above is only about whether *additional* PoE telemetry (negotiated class, power budget) should also be exposed via M&C — currently not requested, but a reasonable PRD "nice to have."

### 2.4 Power Regulators — Analog Devices LT80603 (referenced, no datasheet link in SOW)

- SOW references "LT80603, up to 12" power regulator status signals (§9.1).
  > ✅ CORRECTED (run 02): Run 01 described the LT80603 as "a µModule step-down regulator family… e.g., LT8050/LT8060-series." That was wrong. ADI describes the LT80603 as a compact, ultralow-EMI, high-efficiency synchronous **monolithic** step-down switching regulator (Silent Switcher), documented in the **LT80602/LT80603 data sheet (Rev. 1)**. Sources: https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/lt80603evkit.html ; https://www.analog.com/LT80602/datasheet
  > ⚠️ CD-FLAG: The SOW's References section omits this part; add the link. Confirm the exact variant (the EV kit also covers LT80603A/LT80603HV/LT80603AHV) and which pin provides the "status" signal and its polarity, since SOW 2.3.1.1–2 states open-drain, high = normal.
  > 🧩 CD-NOTE: 12 instances of a single regulator status line is a lot of discrete fault inputs (3 per RF board × 4 boards, or a mix of board + shared rails) — PRD should ask AmpliTech/Union for the power-rail map (which 12 rails, powering what) so the software's power-sequencing/fault-correlation logic (e.g., "which fault caused the LED to go red, and should it suppress nuisance faults during power-up sequencing") can be designed correctly. The SOW's "no fault" state requirement for LED (§9.4) depends on *all* of these 12 + 1 PoE + up to 4 RF-board status pins being simultaneously healthy — this is effectively a 17-input AND-gate implemented in firmware, and the state machine should debounce/sequence these to avoid false "fault" indications during normal power-up transients.

### 2.5 RF-Chain Components (per RF board, up to 4 boards)

| Component | Function | SOW Link | Verified Notes |
|---|---|---|---|
| **ADF4368** | Microwave synthesizer (LO generation) | https://www.analog.com/en/products/adf4368.html | ✅ CORRECTED (run 02): Run 01's "12.15 GHz fundamental" was wrong. Per the ADF4368 data sheet (Rev. 0): integer-N and fractional-N PLL with integrated VCO; output **800 MHz to 12.8 GHz**; VCO 6.4–12.8 GHz; output dividers 1/2/4/8; SPI register map; reference peak detector flags REF_OK with ≥200 mVpp at 100 MHz single-ended. ADI publishes a no-OS driver (`drivers/frequency/adf4368`). |
| **ADAR2001** | 4× frequency multiplier / LO distributor | https://www.analog.com/en/products/adar2001.html | ✅ CORRECTED (run 02): Run 01 called this a true-time-delay beamformer "per general industry knowledge." **That was wrong; the SOW's word "multiplier" is correct.** ADI data sheet title: *10 GHz to 40 GHz, 1:4 Channel, 4× Frequency Multiplier/Filter.* Input 2.5–10 GHz CW, output 10–40 GHz; 4× multiplication with programmable harmonic filter, 1:4 split, four independently enabled differential PAs; 3- or 4-wire SPI; two on-chip state machines (multiplier/filter, 16 states; transmitter, 70 states); on-chip temperature sensor and ADC; specified −40 to +85 °C. In this design it multiplies the ADF4368 output to the LO (8.525 GHz × 4 = 34.1 GHz; 9.075 GHz × 4 = 36.3 GHz). |
| **HMC425A** | IF step attenuator (per RF board) | https://www.analog.com/en/products/hmc425a.html | 📎 Sourced (run 02): 6-bit GaAs MMIC digital positive-control attenuator, **2.2–8.0 GHz**, bit values 0.5/1/2/4/8/16 dB (31.5 dB total), ±0.5 dB typical step error; HMC425LP3 data sheet gives attenuation accuracy of ±(0.5 + 5% of setting) dB max. Truth table in SOW 2.6.7 matches. |
| **HMC472A** | 100 MHz reference step attenuator (single, shared) | https://www.analog.com/en/products/hmc472a.html | 📎 Sourced (run 02): 6-bit GaAs MMIC digital positive-control attenuator, **DC–3.8 GHz**, 0.5 dB LSB to 31.5 dB, ±0.25 dB typical step error, TTL/CMOS control, single +5 V. Used here at 100 MHz with an automatic setting algorithm (§5.7) rather than Ethernet control. |
| **ADL5906** | IF RMS power detector + temperature output (per RF board) | https://www.analog.com/en/products/adl5906.html | 📎 Sourced (run 02): *10 MHz to 10 GHz, 67 dB TruPwr Detector* (true-RMS), response stated independent of waveform type; data-sheet log slope **55 mV/dB** typical (the SOW's 42–52 mV/dB "reference only" figure presumably reflects board-level scaling; to confirm). Pin 8 **VTEMP**: temperature sensor output 1.4 V at 25 °C, 4.8 mV/°C. Ordering grades: ADL5906**A**CPZN, −40 to +105 °C; ADL5906**S**CPZN, −55 to +125 °C. |
| **ADL5904** | 100 MHz reference power detector (single, processor board) | https://www.analog.com/en/products/adl5904.html | 📎 Sourced (run 02): *DC to 6 GHz, 45 dB TruPwr Detector with Envelope Threshold Detection*; RMS input range −30 to +15 dBm; output slope 36.5 mV/dB typical; 3.3 V supply; −40 to +105 °C. Also contains an envelope-threshold comparator: when the internal envelope detector exceeds a user-set voltage on VIN−, the event is latched in an on-chip SR flip-flop (outputs Q/QB) and **held until a reset pulse is applied to the RST pin** (no power cycle needed). The SOW uses only the RMS output (VRMS, via ADC, SOW 2.9). **No firmware action is implied** unless the board wires Q/QB or RST to the MCU; that is a pin-out review check only. |
| **MCP9700A** | Analog temperature sensor (processor board + each RF board) | https://www.microchip.com/en-us/product/mcp9700a | ✅ CORRECTED (run 02): Per Microchip DS20001942: **V₀°C = 500 mV typical, Tc = 10.0 mV/°C typical** (MCP9700/9700A). Accuracy −2.0 to +4.0 °C over −40 to +125 °C (MCP9700A); output swing 100 mV to 1.75 V over the operating range. The SOW's 400 mV is the **MCP9701/9701A** offset, whose slope is 19.5 mV/°C — see flag below. |

> ✅ CORRECTED (run 02): "ADL5906A" in SOW 2.10.4 is the **same physical ADL5906**, read at its VTEMP pin (pin 8): the data sheet specifies 1.4 V at 25 °C and 4.8 mV/°C, exactly the SOW's figures. Two separate chips are not implied. The trailing "A" most plausibly refers to the A-grade ordering code (ADL5906ACPZN, rated −40 to +105 °C).
> ⚠️ CD-FLAG (new): If the A grade is fitted, its rated maximum (+105 °C) equals the SOW's Temperature Warning threshold (2.11.1.1) and sits 20 °C below the Over-Temperature Alarm threshold (2.12.2.1). Confirm the fitted grade and whether the detector's own reading is meant to be trusted above its rating. Remaining open item: the ADC channel mapping for each ADL5906's VRMS and VTEMP outputs (Q27).

> ✅ CORRECTED (run 02) — MCP9700A transfer function: SOW 2.10.3 states 400 mV at 0 °C and 10.0 mV/°C. Microchip's data sheet gives **500 mV at 0 °C and 10.0 mV/°C** for the MCP9700/9700A, and **400 mV at 0 °C and 19.5 mV/°C** for the MCP9701/9701A. The SOW therefore pairs the MCP9701's offset with the MCP9700's slope. Correct conversion for the MCP9700A: **T(°C) = (V_out − 0.500 V) / 0.0100 V/°C.** Using the SOW's 400 mV would read **10 °C high** at every temperature, tripping the 105 °C warning at a true 95 °C and the 125 °C alarm at a true 115 °C. Sources: https://www.microchip.com/en-us/product/mcp9700a ; DS20001942.
> ⚠️ CD-FLAG: Ask AmpliTech to confirm the fitted part (MCP9700A vs MCP9701A) and correct SOW 2.10.3 (questions-4-union.md Q19).

> 🧩 CD-NOTE: All RF-chain ICs (ADF4368, ADAR2001, HMC425A/472A, ADL5906, ADL5904) are from a single vendor family (Analog Devices, including legacy Hittite "HMC" parts now under ADI) — the PRD should note this as a potential single-supplier/BOM risk worth flagging to AmpliTech (component-availability/lead-time risk), even though it's a hardware procurement concern rather than a software one; CD's software (especially SPI driver abstraction) should nonetheless be written so register-map tables are data-driven/config-driven rather than hardcoded, easing any future second-source substitution.

---

## 3. Standards, Protocols, and References (all URLs verified or supplied)

| Reference | Direct link | Role in this project |
|---|---|---|
| IEEE 802.3-2018 | https://standards.ieee.org/ieee/802.3/7071/ | Base Ethernet standard — physical/MAC layer for the 1000 Base-T (or 100 Base-T) interface. |
| IEEE 802.3at-2009 | https://standards.ieee.org/ieee/802.3at/4293/ | PoE+ (Power over Ethernet, Type 2, up to ~25.5W at the PD) — governs the MAX5969A PD interface behavior. |
| RFC 1305 (NTPv3) | https://datatracker.ietf.org/doc/html/rfc1305 | **Threshold** (minimum acceptable) time-sync protocol version. |
| RFC 5905 (NTPv4) | https://datatracker.ietf.org/doc/html/rfc5905 | **Objective** (preferred) time-sync protocol version. |
| RFC 3413 (SNMP Applications) | https://datatracker.ietf.org/doc/html/rfc3413 | Defines SNMP engine application types (command generator/responder, notification originator, proxy) — foundational if SNMPv3 is chosen. |
| RFC 3414 (SNMPv3 USM) | https://datatracker.ietf.org/doc/html/rfc3414 | User-based Security Model — the base USM framework the SOW requires ("SNMP USM: Implemented/Included"). |
| RFC 3415 (SNMPv3 VACM) | https://datatracker.ietf.org/doc/html/rfc3415 | View-based Access Control Model — required, with a **minimum of 15 groups**. |
| RFC 3826 (AES in USM) | https://datatracker.ietf.org/doc/html/rfc3826 | Defines AES-CFB as a USM privacy protocol — matches SOW's "AES-128 Cipher Feedback Block (CFB)" requirement. |
| RFC 7630 (HMAC-SHA-2 in USM) | https://datatracker.ietf.org/doc/html/rfc7630 | Defines the SHA-2 family auth protocols for USM. **Important nuance (see flag below).** |
| RFC 5424 (Syslog) | https://datatracker.ietf.org/doc/html/rfc5424 | Format target for the Event Recorder log. |
| ISO 8601-1 | https://www.iso.org/standard/70907.html | Timestamp format standard for all event-recorder entries, to millisecond precision. |
| OpenAPI Specification v3.1.1 | https://spec.openapis.org/oas/v3.1.1.html | Required protocol-definition standard IF the RESTful M&C option is chosen. |
| TLS 1.3 | https://datatracker.ietf.org/doc/html/rfc8446 | 🔗 CD-REF (not in SOW's reference list but implied): the formal spec for "Transport Security Layer version 1.3" required for HTTPS REST bearer. |
| HTTPS | https://datatracker.ietf.org/doc/html/rfc9110 (HTTP semantics) | 🔗 CD-REF: current HTTP semantics RFC (obsoletes RFC 7230–7235), relevant baseline if REST/HTTPS option is chosen. |

> 📎 SOURCED (run 02): RFC 7630 defines four HMAC-SHA-2 USM authentication protocols: `usmHMAC128SHA224AuthProtocol`, `usmHMAC192SHA256AuthProtocol`, `usmHMAC256SHA384AuthProtocol`, and `usmHMAC384SHA512AuthProtocol`. Conforming implementations MUST support usmHMAC192SHA256 and SHOULD support usmHMAC384SHA512; the other two are OPTIONAL. The only SHA-512-based protocol is **usmHMAC384SHA512AuthProtocol** (SHA-512, MAC truncated to 48 octets, 64-octet key). **The PRD must pin down the exact protocol name/OID with the customer**, since manager interoperability depends on it.
> ⚠️ CD-FLAG (new): **RFC 7630 is obsolete** — it was obsoleted by **RFC 7860** because the MIB MODULE-IDENTITY value was incorrectly specified. The SOW cites RFC 7630; the PRD should reference RFC 7860. Source: https://www.rfc-editor.org/rfc/rfc7630 ; https://datatracker.ietf.org/doc/draft-ietf-opsawg-hmac-sha-2-usm-snmp/

> 🧩 CD-NOTE: Because RFC 3413/3414/3415/3826/7630 collectively require a **full SNMPv3 agent stack** (USM + VACM + AES-CFB + HMAC-SHA2), and the RESTful alternative requires **OpenAPI 3.1.1 + HTTPS/TLS1.3 + a customer-approved auth scheme + RBAC**, this is effectively **two complete, mutually exclusive M&C subsystems** that satisfy the same functional requirement set. The PRD should treat "REST vs. SNMPv3" as a **Day-1 architectural decision gate**, not a feature to build in parallel — each path has a very different protocol stack, security model, and third-party library dependency (an embedded SNMPv3 agent with USM/VACM and SHA-512 authentication — see the §7 license note for what lwIP and CycloneTCP provide — vs. an embedded HTTPS server + TLS 1.3 library + a REST routing layer). ✅ CORRECTED (run 02): run 01's example of "Net-SNMP's agentx" as an embedded agent was unsourced and is withdrawn. **Recommend this be an explicit open question posed back to Union/AmpliTech/end-customer early, since it gates nearly the entire software architecture.**

> 🧩 CD-NOTE: Given the Infineon XMC7200 has a hardware crypto accelerator (§2.1), both TLS 1.3 and AES-CFB/HMAC-SHA2 crypto operations can likely be hardware-accelerated — worth a specific feasibility/benchmarking spike early in the project since SNMP's "≥4 measurements/second" M&C responsiveness requirement (§5.2 below) could be affected by crypto overhead per request if TLS/USM crypto is done in software.

---

## 4. Ethernet / Network Interface Requirements (SOW §1.x)

- **Req 1.1 — Physical layer** (SOW text, verbatim): *"Physical electrical interface: Single 1000 Base-T Ethernet interface. For clarity, all management and operator functions shall use a single common Ethernet interface."*
- **Hardware and Target Environment** (SOW text, verbatim): *"Requirement 1.1 specifies 1000 Base-T. 100 Base-T is also being considered to simplify both hardware and software requirements. Vendors are requested to provide a high level description of cost and schedule differences for 1000 Base-T vs 100 Base-T."*
  > 🧩 CD-NOTE: The proposal must include this 1000 vs 100 Base-T cost/schedule comparison. ✅ CORRECTED (run 02): run 01's statement that the delta is "almost purely software/driver scope" contradicted the SOW, which says 100 Base-T would simplify **both hardware and software**, and has been removed.
- **Single common interface for all functions**: management and operator traffic share **one** Ethernet interface — no separate management VLAN/interface implied. Stated explicitly: *"For clarity, all management and operator functions shall use a single common Ethernet interface."*
  > 🧩 CD-NOTE: This single-interface constraint interacts with the Factory Reset requirement (§6.3 below), which requires "an aliased static IP address" reachable over the *same* physical port — i.e., the processor must support **multiple IP addresses bound to one physical interface** (IP aliasing), with the factory-reset listener firmly isolated (only a reset command — and nothing else — reachable via that alias). This is a meaningful network-stack requirement (needs a TCP/IP stack supporting multiple bound addresses per interface, e.g., lwIP multi-address support) that should be called out explicitly as its own requirement line in the PRD, since it's easy to under-scope "IP aliasing" as a trivial feature.

### 4.1 Monitor & Control (M&C) Protocol — Option A: RESTful

- Protocol: **OpenAPI v3.1.1** compliant.
- Bearer: **HTTPS**.
- Transport security: **TLS 1.3**.
- Authentication: scheme must be **approved by end customer** (not specified by Union — open item).
- Authorization: **Role-Based Access Control (RBAC)**; roles may be predefined or user-defined with granular user management; **Seller's (CD's) role scheme must be approved by end customer**.
  > 🧩 CD-NOTE: "Granular user management" implies persistent user-account storage (usernames, hashed credentials or cert bindings, role assignments) living on the embedded device itself — this has real implications for: (a) non-volatile storage sizing/wear-leveling for a user database, (b) secure credential storage (hashing algorithm, salt, possibly leveraging the CPU's crypto block), and (c) an account-management API surface (create/delete/modify user, assign role) that isn't explicitly itemized as an M&C function in §5.1 but is clearly required to make RBAC meaningful. **This should become explicit PRD requirements**, e.g. "User Account Management API" and "Credential Storage Security."
  > ⚠️ CD-FLAG: No password-complexity, account-lockout, audit-log-of-logins, or session-timeout requirements are stated anywhere in the SOW despite requiring authentication + RBAC + a security-conscious customer. Recommend flagging to Union as a likely gap that the end customer will want addressed (many ground-station/government-adjacent customers mandate things like failed-login lockout and session expiry as part of ICD security review).

### 4.2 Monitor & Control Protocol — Option B: SNMPv3 (only SNMPv3, not v1/v2c)

- Bearer: **UDP over Ethernet**.
- USM: **Required**, AES-128-CFB privacy, **SHA-512-family** auth (see RFC 7630 flag above).
- VACM: **Required**, minimum **15 groups**.
  > 🧩 CD-NOTE: 15 VACM groups is a specific, non-trivial number — worth asking Union/end-customer what access-control granularity drives this (e.g., per-operator-role × per-subsystem combinations?). This number should map onto a concrete access-control matrix in the PRD (which OIDs/subtrees each of the 15 groups can read/write) rather than being treated as an arbitrary config limit.
  > 🧩 CD-NOTE: A full SNMPv3 implementation requires a **custom or licensed MIB** describing every readable/writable OID (alarms, warnings, gain settings, IP config, factory reset, firmware version, etc. — essentially everything in §5). **Authoring the enterprise MIB is itself a first-class deliverable implied by, but not explicitly listed among, the SDRLs** (§10) — the closest explicit SDRL is "Software Interface Control Document" (SDRL 02/04), which for the SNMP path should *be* (or include) the MIB definition. Recommend the PRD explicitly state "MIB file(s), if SNMP option selected, are part of the ICD deliverable."

### 4.3 M&C Reporting & Operations (functional command/telemetry list) — SOW §5 (orig. 1.3.1)

Each bullet below is a **discrete command or telemetry point** the firmware must expose over whichever M&C protocol is chosen:

1. **Summary alarm** (read) — see Alarms §9.12.
2. **Over-temperature alarm** (read) — see Alarms §9.12.
3. **Communication fault** — explicitly **"No requirement"** (i.e., Union considered and explicitly excluded this; not an oversight).
   > 🧩 CD-NOTE: Worth noting in the PRD *why* this was excluded if known (perhaps the physical-layer link-state detection is considered out of scope per §2.2's flag above) — or simply carry it forward as "intentionally descoped by customer" so it isn't accidentally "fixed" later as a perceived gap.
4. **Critical internal temperatures** (read, multiple sensors) — monitored and reported; see §9.10.
5. **Set IP address** (write) — note interaction with the factory-reset aliased-IP requirement (§6.3) and whether this is DHCP, static, or both (SOW doesn't say — **gap**, see §12).
   > ⚠️ CD-FLAG: No requirement is stated for DHCP client support, IPv6, VLAN tagging, or static-vs-dynamic addressing policy. Given this is ground-station infrastructure, static IP is likely the norm, but the PRD should explicitly ask/confirm: DHCP support required? IPv6 required (likely no, but should be confirmed)? Default/fallback IP behavior if unconfigured?
6. **Set gain (attenuator)** (write) — one HMC425A per RF board (up to 4), **range 0–20 dB in 1 dB increments**.
   > ⚠️ CD-FLAG (important, cross-reference §9.6): the HMC425A hardware truth table (reproduced below) natively supports **0 to 31.5 dB in 0.5 dB steps**, but the *M&C-exposed* range/resolution is **0–20 dB in 1 dB steps**. This is a deliberate restriction of the hardware's capability at the API/command layer — confirm this is intentional (e.g., matching an RF link-budget design margin) rather than an oversight, and make sure the PRD states explicitly that the command-layer range (0–20 dB, 1 dB steps) is a *software-enforced subset* of the full hardware range (0–31.5 dB, 0.5 dB steps), with out-of-range commands rejected/clamped per a to-be-defined error-handling policy (also a gap — see §12).
7. **Read gain (attenuator)** (read) — readback of current attenuator setting per RF board.
8. **LNB Reboot** (write/command) — remote reboot capability.
   > 🧩 CD-NOTE: Interacts with the Event Recorder requirement to log state "prior to and following" the change (§7.1) — a reboot command must flush/persist the pending log entry *before* actually resetting, which has real implications for storage write ordering/timing (must complete the "prior to" log write before the reset executes, and the "after" log write must survive across the reset — i.e., requires non-volatile, power-loss-safe logging, not just a RAM buffer).
9. **Alarm & warning reset** (write/command) — clears latched alarms/warnings.
10. **Read all warnings** (read).
11. **Read all alarms** (read).
12. **Read accumulated operating hours** (read) — explicitly defined as **"unmuted operating hours"** — implies a **mute/unmute concept** exists elsewhere in the system that is not otherwise described in the SOW.
    > ⚠️ CD-FLAG: "Unmuted operating hours" implies the LNB has a mute/unmute state (likely an RF output mute, common in LNB designs to prevent harmful transmission during fault conditions or maintenance) that is **never otherwise defined** in this SOW — no mute command, mute status read, or mute GPIO/control path is specified anywhere else in the document. **This is likely the single most significant functional gap found in this pass.** Recommend this be raised directly with Union: is there a mute function, and if so, what triggers it (operator command? automatic on alarm? both?), what does it control (which attenuator/output stage?), and how is "unmuted" distinguished from "muted" for the operating-hours counter? This probably needs its own requirement subsection in the PRD.
13. **Read LNB model** (read) — value set at test (§8).
14. **Read LNB serial number** (read) — value set at test (§8).
15. **Read hardware LNB revision** (read) — value set at test (§8).
16. **Read LNB firmware version** (read) — **"set in source code"** (i.e., a build-time constant, not a field-settable value).
    > 🧩 CD-NOTE: Implies the build process must embed a version string (ideally derived from git tag/commit, e.g., via CI) into the firmware image — worth specifying a version-string format (semantic versioning recommended: MAJOR.MINOR.PATCH(+build/commit-hash)) in the PRD so the "Read LNB firmware version" command has a well-defined, parseable return format for fleet-wide update automation (§7.2).

### 4.4 M&C Parametric Values — SOW §5.2 (orig. 1.3.2)

1. **Output IF signal power level**: readable range **-33 dBm to +12 dBm minimum**.
2. **IF detector uncertainty (upper bound)**: **≤ 2.0 dB**.
3. **IF detector type**: **RMS** (true-RMS responding detector — consistent with ADL5906 being a translinear RMS log detector).
4. **Measurement rate**: **≥ 4 measurements/second** per channel.
   > 🧩 CD-NOTE: "≥4 measurements/second" sets a firm real-time sampling-loop budget: for up to 4 RF boards × (IF power + temperature) + 1 shared 100MHz reference + 1 processor-board temp = up to **10 analog channels**, each needing to be sampled, converted, and made available to the M&C layer at ≥4 Hz — i.e., a full ADC scan cycle must complete well under 250ms. This is a concrete real-time design constraint the PRD should state explicitly as a performance requirement, and it should drive the ADC driver design (e.g., round-robin DMA-driven scanning using the CPU's "3 DMA controllers/216 channels," §2.1) rather than naive polling.

### 4.5 Factory Reset — SOW §5.3 (orig. 1.3.3)

1. Reachable via **IP over Ethernet, aliased static IP address** (same physical port as main M&C — see §4 note above on IP aliasing).
2. Connection must use **TLS**.
3. **Only** a factory-reset command is reachable via this interface (hard isolation from all other M&C functions) — i.e., this must be a minimal, separate protocol handler, not just an additional route bolted onto the main REST/SNMP service.
4. **Access-controlled**: only specific privilege levels may execute it; **privilege schema requires end-customer approval**.
   > 🧩 CD-NOTE: "Factory reset" semantics are undefined — does it erase user accounts/roles, network config, calibration data, event log, or all of the above? Does it return the unit to the "set at test" values (§8) or wipe those too? **Gap — needs explicit definition** of exactly what state is reset vs. preserved (e.g., calibration constants and set-at-test serial/model/revision strings almost certainly must survive a factory reset, since those are tied to the physical unit, not its configuration).
   > ⚠️ CD-FLAG: Using TLS for a "reset-only" interface implies a minimal embedded TLS server stack must be stood up *twice* conceptually (once for the main M&C REST path if chosen, once for the factory-reset listener) or the same TLS stack must support two completely different authorization domains on two different bound addresses. This should be explicitly architected, not assumed to be "free" because TLS is already required elsewhere.

### 4.6 Event Recorder — SOW §7 (orig. 1.4)

- Must log a record **before and after** every: operator-initiated change, M&C-initiated change, firmware update, and detected warning/alarm/fault.
- Format: should follow **RFC 5424** (syslog).
- Must be **human-readable**.
- Must be **downloadable via the HMI**.
  > ⚠️ CD-FLAG: "HMI" (Human-Machine Interface) is used here but **no HMI is otherwise defined anywhere in the SOW** — the only described interfaces are Ethernet (REST/SNMP), SPI (internal), GPIO, analog, and UART (board-test only). There is no mention of a web UI, a front-panel display, or any operator-facing graphical/textual control surface. **This is the second major gap found in this pass.** Likely interpretations: (a) "HMI" loosely means "via the M&C/Ethernet interface" (i.e., download over REST/SNMP, perhaps as a bulk file-transfer or a large SNMP table walk), or (b) there is a separate, unstated HMI application (perhaps a customer-supplied or AmpliTech-supplied GUI) that consumes this LNB's M&C interface, and the embedded firmware's job is only to make the log available via the network API, not to render any UI itself. **This must be clarified with Union before PRD finalization** — if a literal front-panel/GUI HMI is expected, that is an entirely unscoped, potentially large body of work (web server + UI assets, or local display hardware) not budgeted anywhere else in this SOW (no display hardware is mentioned in the hardware section).
- Timestamps: **ISO 8601-1**, millisecond resolution — requires the RTC (confirmed present on XMC7200, §2.1) to be NTP-disciplined (§4.7) to be meaningful/accurate.

### 4.7 Time Keeping — SOW §8 (orig. 1.5)

- Time source: **network time server** specified by operator or external M&C master (i.e., NTP server address is itself a configurable M&C parameter — another command not explicitly itemized in §5.1's list, but logically required: "Set NTP server address").
- Protocol: **NTP**, objective **v4** (RFC 5905), threshold (minimum) **v3** (RFC 1305).
- **Time error ≤ 1 second**, **time precision ≤ 1 millisecond**.
  > 🧩 CD-NOTE: "Error ≤1s" and "precision ≤1ms" are two different metrics (accuracy vs. resolution) — the PRD should distinguish them. Resolution of ≤1 ms is a representation/timer question. Absolute accuracy within 1 s must hold between NTP synchronizations, including during network outages, so the allowed drift depends on the time-base oscillator's specified tolerance and the longest NTP outage the customer requires the unit to ride through. ✅ CORRECTED (run 02): Run 01 quoted "typically ±20–100 ppm" crystal tolerance; that range was unsourced and is withdrawn. The tolerance must come from the target board's BOM. For illustration only: at an assumed 50 ppm drift, 1 s of error accumulates in 1 / (50 × 10⁻⁶) = 20,000 s ≈ 5.6 h. (The EVK provides a 32.768 kHz watch crystal on P21[0]/P21[1] for the RTC, per the KIT_XMC72_EVK guide; the target board's time base is unknown.) The PRD should ask AmpliTech what NTP-outage duration must be tolerated, since the answer may require a higher-stability oscillator — a hardware consideration.
  > 🧩 CD-NOTE: An NTP *client* (not server) role is described — the LNB synchronizes *from* a server, it does not need to serve NTP itself. Worth stating explicitly in the PRD to avoid scope creep.

### 4.8 Firmware Update — SOW §9 (orig. 1.6)

1. All firmware/software **remotely updateable via Ethernet**.
2. **Fleet-wide automated/unattended update** capability across the "entire Item population" — implies a scripting/API surface for bulk/unattended orchestration (likely driven externally, but the LNB's update API must support non-interactive/unattended invocation — e.g., no interactive confirmation prompts, idempotent/resumable transfer).
3. **No special/proprietary tools** required — i.e., the update mechanism must work with standard tools (implies a well-documented, possibly scriptable HTTP/SNMP-based upload mechanism rather than a vendor-proprietary flashing utility requiring a Windows GUI, for example).
4. **Cryptographic integrity**: SHA-2 or SHA-3 hash, or an **end-customer-approved digital signature** method.
   > 🧩 CD-NOTE: "Hash" alone (SHA-2/3) only proves *integrity* (no corruption), not *authenticity* (who signed it) — a hash-only scheme cannot prevent a malicious actor from uploading a hostile-but-well-formed image with a matching hash they computed themselves. **True authentication requires a digital signature** (e.g., RSA or ECDSA over a SHA-2/3 digest) or an HMAC with a securely provisioned shared secret. The PRD should treat "or end customer approved digital signature" as the operative security requirement and recommend proposing an asymmetric-signature scheme (e.g., ECDSA P-256 + SHA-256, leveraging the CPU's crypto accelerator) as the default proposal to the end customer, rather than hash-only, to meet a reasonable security bar for ground-station infrastructure.
5. **Automatic rollback** on update failure to "most recent functioning" version.
   > 🧩 CD-NOTE: Requires an **A/B (dual-bank) firmware partitioning scheme** on flash (8384 KB total flash per §2.1 — the PRD should size the A/B partition split, plus space for calibration data, user/role database, and event log, against this 8MB budget) with a boot-time validity check (e.g., CRC/hash check + a "boot successful" watchdog-style confirmation from the running application before committing to the new bank) to detect "failure" and roll back. This is a non-trivial bootloader architecture requirement that should be explicitly designed and documented (likely as part of SDRL 01/03 functional description), not treated as a simple firmware feature.
6. **Manual rollback**: end user can remotely roll back to **prior versions** (plural — implies more than one historical version may need to be retained, which has flash-budget implications beyond simple A/B if more than 2 versions must be kept, or implies "prior" just means "the previous A/B partner," which should be clarified).
   > ⚠️ CD-FLAG: Clarify with Union whether "roll back to prior versions" (plural) means only the immediate previous version (consistent with simple A/B) or a deeper history (requiring more flash budget and a version-management scheme) — affects both flash partitioning and PRD storage requirements.

### 4.9 Set-at-Test Variables — SOW §10 (orig. 1.7)

- Three fields, set at test by the operator using the Ethernet interface (SOW 1.7.1): **LNB Model Number, LNB Serial Number, Hardware LNB Revision**.
- Each field: **max 32 alphanumeric characters**, **leading-zero padded**, **dashes (-) allowed**.
  > ⚠️ CD-FLAG: "Leading zero padded" is an unusual constraint for an *alphanumeric* field (zero-padding normally applies to purely numeric fields/counters). Clarify with Union: does this mean (a) if the value is numeric, pad with leading zeros to a fixed width (and if so, what width — is it always 32, or some other field-specific width?), or (b) all fields must literally always be exactly 32 characters long with zero-padding on the left regardless of content type (which is unusual for e.g. a serial number containing letters and dashes)? This directly affects the data-validation/storage format the PRD must specify.
  > 🧩 CD-NOTE: These three values must survive firmware updates, reboots, and (per the Factory Reset gap in §4.5) very likely must survive factory reset too, since they're tied to the physical hardware unit, not to configuration state — the PRD should explicitly classify these (and calibration constants, §9.8–9.9) as "hardware-identity data," stored in a protected/separate non-volatile region from "user-configurable data" (network settings, accounts, roles) that *would* be cleared by factory reset.
  > 🧩 CD-NOTE: Because these are "set at test" and presumably **write-once** (or write-rarely, access-controlled) fields, the PRD should specify whether the write operation is idempotent/overwritable at all post-test, or truly one-time-programmable (OTP) at the software level — this affects whether a software bug could accidentally allow re-writing a serial number in the field, which would be a quality-escape risk for any serialized/traceable hardware product.

---

## 5. Internal LNB Requirements — SOW Section 2 (hardware-facing control loops)

### 5.1 Ethernet PHY Interface (§2.1 / orig. 2.1)
- Processor interfaces to the Ethernet PHY IC to satisfy all stated Ethernet/M&C requirements (§4 above). No additional electrical detail given (e.g., RMII/RGMII/SGMII interconnect between XMC7200 and DP83869 is not specified in the SOW text).
  > ⚠️ CD-FLAG: The MAC-to-PHY interconnect for the target board is not specified — needed for the SOW-mandated schematic pin-out review. ✅ CORRECTED (run 02): Run 01 called RGMII "the most likely choice" without a source. What is sourced: the KIT_XMC72_EVK wires ETH1 to its PHY in RGMII mode (EVK guide §4.2.13). Whether the target board uses RGMII with the DP83869HM must be confirmed from AmpliTech's schematic (Q04).

### 5.2 Status GPIO Inputs from RF Board(s) (§2.2)
- One status signal **per RF board**, up to 4.
- **Active-high-good** logic: high = no fault, low = fault.
- **Weak pull-up** on each pin so an unconnected/absent RF board reads as "status good" (no fault) by default.
  > ⚠️ CD-FLAG: This default-pull-up-to-good behavior means the firmware **cannot distinguish "RF board not installed" from "RF board installed and healthy"** using the status pin alone. See the corrected population model in §1 (configured population authoritative, SPI identity probe at boot as corroboration, mismatch = fault). Confirm with AmpliTech (Q03).
- Status changes must be logged to UART (§5.6 cross-ref).

### 5.3 Status GPIO Inputs from On-Board Power Regulators (§2.3)
- One status signal per **LT80603**, up to **12**; open-drain, pulled up to 3.3V, active-low fault (same polarity convention as above but note: "high for normal operation, low for fault" here, vs. RF-board status pins described slightly differently above — functionally consistent, both active-low-fault).
- One status signal from the **MAX5969A** PoE controller, same open-drain/3.3V/active-low-fault convention.
- All changes logged to UART.
  > 🧩 CD-NOTE: Total discrete fault-status GPIO inputs = up to 4 (RF boards) + 12 (LT80603 regulators) + 1 (PoE) = **17 individual fault inputs**, all feeding the 3-color LED state machine (§5.4) and the Summary Alarm (§9.12.1). The PRD should enumerate all 17 (or however many are confirmed) as named, individually loggable/reportable signals — not just an aggregate "fault/no fault" bit — since the Event Recorder requirement (§4.6) implies the *cause* of each fault transition should be individually identifiable in the log, and "Read all alarms" (§4.3.11) implies per-source alarm detail should be retrievable, not just a single bit.

### 5.4 LED Control (§2.4) — 3-state visual indicator state machine
| State | Color | Entry condition | GPIO pattern |
|---|---|---|---|
| Initializing | Yellow | Power-up or reset; held until **all** input status pins read "no fault" | Yellow=HIGH(3.3V, ≥6mA source), Green=LOW, Red=LOW |
| Normal Operating | Green | From Initializing (or from Fault) once **all** status pins are healthy | Green=HIGH(3.3V, ≥6mA source), Yellow=LOW, Red=LOW |
| Fault | Red | From Normal Operating, the instant **any** status pin goes to fault | Red=HIGH(3.3V, ≥6mA source), Green=LOW, Yellow=LOW |

- LED state transitions must be logged to UART.
  > 🧩 CD-NOTE: This is a classic 3-state FSM gated by the logical AND of all 17 status inputs (§5.3 note) — recommend the PRD formally specify this as a state diagram with explicit transition table (including whether there's any debounce/hold-off timing to avoid LED "flicker" during power-up sequencing transients across 17 independently-timed power rails — the SOW doesn't specify debounce timing, a likely gap/clarification item). Also note: does a **new, additional** fault (e.g., 2nd regulator fails while 1st fault already present) while already in Fault state need to be logged as a distinct event even though the LED stays Red? (Yes, implied by "log...following all detected internal warnings, alarms, and faults," §4.6 — each individual status transition should be logged regardless of aggregate LED state.)
  > 🧩 CD-NOTE: "Must source up to 6 mA" for each LED GPIO is a concrete electrical/driver-strength spec worth carrying into the pinout-review support task (confirm XMC7200 GPIO drive-strength configuration meets 6mA source current on the selected pins — likely trivial for this MCU class but should be explicitly confirmed, not assumed).

### 5.5 SPI Bus I/O — ADF4368 & ADAR2001 Register Access (§2.5)
- **ADF4368** (1 per RF board, up to 4):
  - Registers written **and read back** on power-up and after reset.
  - Firmware must support writing/reading **all** registers defined in the ADF4368 datasheet (i.e., a complete register-map driver, not just a few key registers).
  - **Unique register values per RF board** (board 1 and board 2 values defined in **Appendix A — currently "TBD"**).
  - Register values are **fixed in firmware**, **not controllable via Ethernet** (i.e., LO frequency/synthesizer configuration is not a runtime M&C-exposed parameter — a fixed-frequency design per board).
  - Register read/write activity must be printed to UART (register addresses + values) — i.e., UART output must include a **human-readable register dump**, not just a status code.
- **ADAR2001** (1 per RF board, up to 4): identical requirements pattern (read/write on power-up+reset, full register-map support, per-board-unique fixed values from **Appendix B — currently "TBD"**, not Ethernet-controllable, UART-logged).
  > ⚠️ CD-FLAG: **Both Appendix A and Appendix B are literally "TBD"** in the current SOW revision — these are not optional nice-to-haves, they are the actual numeric register values the firmware must hardcode (or load from a config table) for boards 1 and 2 at minimum (boards 3/4 values aren't mentioned at all — another gap, since "up to 4" boards is the stated max but only 2 boards' worth of register values are even planned to be specified). **This is a hard blocker for functional implementation and final test** — flag as a critical open item requiring Union/AmpliTech to supply before Progress Demonstration milestones (the schedule, §13 below, requires 50%/90%/100% of requirements "implemented," and register-value-dependent synthesizer/multiplier bring-up cannot be completed without this data).
  > 🧩 CD-NOTE: Since register values are "defined in firmware" (not runtime-configurable) but are per-board-unique, the PRD should specify a **compile-time or load-time configuration table** approach (e.g., a per-board-index array of register-value sets, selected via a board-identity mechanism — which again ties back to the open question in §5.2 about how board identity/count is determined) rather than conditional code branches, to keep the design maintainable as more board register sets (3, 4) are added later.

### 5.6 HMC425A IF Step Attenuator Control (§2.6)
- 1 per RF board (up to 4), **6 GPIO output bits** each (so up to 24 GPIO pins just for this function across 4 boards).
- Logic: 3.3V=High, 0V=Low.
- **Power-up/reset default: all bits HIGH → 0 dB attenuation** (i.e., default to *minimum* attenuation, not maximum — worth confirming this is intentional from an RF-safety perspective, since 0dB default means full signal level passes through immediately at power-up before any M&C command sets a deliberate value).
- Set via Ethernet command (§4.3.6 cross-ref), independently **per board**.
- Truth table (from HMC425A datasheet, reproduced faithfully from SOW):

  | Attenuation | Bit1 | Bit2 | Bit3 | Bit4 | Bit5 | Bit6 |
  |---|---|---|---|---|---|---|
  | 0 dB | H | H | H | H | H | H |
  | 0.5 dB | H | H | H | H | H | L |
  | 1 dB | H | H | H | H | L | H |
  | 2 dB | H | H | H | L | H | H |
  | 4 dB | H | H | L | H | H | H |
  | 8 dB | H | L | H | H | H | H |
  | 16 dB | L | H | H | H | H | H |
  | 31.5 dB | L | L | L | L | L | L |
  (Combinable additively — binary-weighted attenuator: bits represent 0.5/1/2/4/8/16 dB, summed when multiple bits are Low.)
- All attenuator state changes logged to UART.
  > 🧩 CD-NOTE: Since the hardware truth table is strictly binary-weighted/additive (standard digital step attenuator design), the firmware's "set attenuation" function is a simple binary-to-bit-pattern mapping (attenuation_half_db_steps = round(dB * 2); invert bit pattern since Low=more attenuation) — but confirm the **0–20dB/1dB M&C-exposed resolution** (§4.3.6 flag) means the driver must round/reject odd 0.5dB requests, and must reject/clamp requests >20dB even though the hardware could go to 31.5dB — this rounding/clamping policy needs explicit, testable PRD requirements (what happens on an out-of-range or non-1dB-increment command — error response? silent rounding? reject with specific error code?).

### 5.7 HMC472A 100 MHz Reference Step Attenuator Control (§2.7)
- Single unit (shared, not per-RF-board), 6 GPIO bits, same electrical logic levels (3.3V High [2.0-5.0V tolerance], 0V Low [0-0.8V tolerance] — note: this is the only place in the SOW where explicit voltage *tolerance bands* are given for logic levels, worth propagating that same tolerance assumption to the HMC425A and LED GPIOs in the PRD even though not explicitly repeated there).
- **Power-up/reset default: Bit1=Low, Bit2=Low, all others High → 24 dB attenuation** (16+8=24dB; notably a much higher default attenuation than the HMC425A's 0dB default — consistent with this being a reference/LO path where excess power at power-up could be more problematic, reinforcing that the differing defaults are deliberate, not inconsistent).
- **Automatic self-calibrating algorithm** (not Ethernet-commanded, unlike the per-board IF attenuators):
  1. Apply the algorithm **5 ± 1 seconds** after power-up/reset; run **only once** per power cycle/reset (not repeated).
  2. Read the 100 MHz reference power detector ADC (§5.9), calculate input power (per §5.9's calculation method).
  3. Adjust attenuator bits until detector input power = **-1 dBm ± 1 dB**.
  4. Wait **1000 ms ± 100 ms**, re-measure, re-adjust if out of range.
  5. Once converged, **do not adjust again** until next power-on/reset.
- Truth table identical in structure to HMC425A (same binary-weighted 0.5–31.5dB range).
- All state changes logged to UART.
  > 🧩 CD-NOTE: This is a **closed-loop control algorithm** (read-adjust-reread-readjust, bounded iteration) — the PRD should specify this as a distinct functional/algorithmic requirement, including: what happens if convergence to -1dBm±1dB is never achieved after the single re-check (step 4)? Is there a maximum retry count, and what's the fallback/alarm behavior if the reference path is unable to converge (e.g., a disconnected/failed 100MHz reference input)? This failure mode is not addressed in the SOW and should be a flagged open item — it's directly relevant to system reliability since the 100MHz reference likely feeds the ADF4368 synthesizers (common LO reference distribution architecture), so a mis-calibrated or failed reference path could silently degrade every RF board's LO performance.

### 5.8 ADC Inputs for IF Power Detector(s) — ADL5906 (§2.8)
- 1 per RF board (up to 4). Output voltage range **0.3–3.8 VDC** against a **4V ADC reference**.
- **Factory calibration**: 3 power levels × 3 frequencies × 3 temperatures = **27 calibration data points per detector** (not quite — re-reading: 3 power levels at "low/mid/high end of IF band" combined with 3 frequencies, so power levels and frequencies are paired, giving up to 3×3=9 combinations, times 3 temperatures = **27 points**, or possibly power-levels-at-each-frequency = 3 points representing the practical calibration sweep; the exact Cartesian structure should be confirmed):
  - Power levels: **-30 dBm, -20 dBm, +13 dBm**.
  - Frequencies: **3.5 GHz, 4.5 GHz, 5.5 GHz** (modulated IF signal, not CW). ✅ CORRECTED (run 02): Run 01 asserted that RMS-detector crest-factor tables are "typically referenced to specific modulation types" (unsourced, withdrawn). The ADL5906 data sheet claims response independent of waveform types (GSM-EDGE, CDMA, W-CDMA, TD-SCDMA, WiMAX, LTE). **Gap remains:** the calibration waveform/modulation is unspecified and should be defined for repeatability.
  - Temperatures: **-40°C, +23°C, +55°C**.
  - Reference-only typical slope: **42–52 mV/dB**.
- Operational measurement: **average intercept/slope across frequency**, **interpolated across temperature**.
- Reference-only: a **20 dB coupler** is used in the signal path, so actual IF output power is 20 dB higher than the detected power (SOW 2.8.6). ✅ CORRECTED (run 02): run 01 concluded that firmware "must add this 20dB offset back in before reporting"; that is not established, because calibration is defined against IF *output* power (see flag below; Q25).
  > ⚠️ CD-FLAG: The 20 dB coupler offset is called "reference only" — background context, not a firmware constant. ✅ CORRECTED (run 02): Run 01's "±0.5–1 dB variation" figure was unsourced and is withdrawn. Run 01 also said firmware "must add this 20 dB offset back in"; that is not established. SOW 2.8.4.1 defines calibration in terms of **IF output power levels**, so an end-to-end calibration may already absorb the coupling factor into the fitted intercept. Whether a separate correction is applied is an open question (Q25); the coupler's actual coupling value and frequency dependence must come from AmpliTech's design.
  > 🧩 CD-NOTE: The calibration/measurement scheme described (average slope/intercept across frequency, interpolated across temperature) is a **2-D (frequency × temperature) piecewise-linear calibration model** — the PRD should specify the exact interpolation method (linear is implied, but should state: linear interpolation between the 3 calibrated temperature points, and what extrapolation policy applies outside -40°C to +55°C, since operational temperature range could exceed this per the MCU's -40 to +125°C rating) and the exact data structure for storing the 27 (or fewer) calibration constants per detector per board (non-volatile storage requirement, tied into the factory-test calibration procedure that must be a documented step in SDRL 09/10 build-and-test instructions).
- IF detector readings logged to UART.

### 5.9 ADC Input for 100 MHz Reference Power Detector — ADL5904 (§2.9)
- Single unit on processor board (not per-RF-board).
- Output: **0.45–1.45 VDC** against **1.8V ADC reference**.
- **Factory calibration** (2-point, single frequency/temperature):
  - Set the 100MHz reference attenuator (HMC472A) to **12 dB** fixed during calibration.
  - Input power levels: **-18 dBm, +10 dBm** → equivalent to **-22 dBm and +6 dBm at the detector input** (SOW 2.9.5.1.4–5).
  > ✅ CORRECTED (run 02) — gap #7 withdrawn: Run 01 flagged this as an arithmetic error by assuming the 12 dB attenuator was the only element between the calibration input and the detector. Figure 2 shows the path is **input → variable attenuator → LNA → filter → power detector**. Both calibration points shift by the same amount (−18 → −22 dBm and +10 → +6 dBm are each −4 dB net), which is what a fixed path gain/loss produces. With 12 dB of attenuation, the LNA-plus-filter section would need a net gain of **+8 dB** (−12 + 8 = −4 dB). This is consistent with the diagram, not a typo. Open item: AmpliTech to confirm the LNA/filter net gain that establishes the mapping (Q26).
  > 📎 SOURCED (run 02): Both detector-input calibration points, and the −1 dBm ± 1 dB target of the setting algorithm (2.7.5.3), fall inside the ADL5904's −30 to +15 dBm RMS input range.
  - Calibration frequency: **100 MHz**; temperature: **23°C** (single temperature only — no multi-temperature correction for this detector, unlike the ADL5906, which the PRD should note as an accepted asymmetry, not an oversight, given reference-only language).
  - Reference-only typical slope: **35 mV/dB**.
- **Detected power calculation** (2-step, explicitly defined method):
  1. ADC counts → voltage (via known ADC reference voltage, 1.8V).
  2. Voltage → power level via the 2-point calibration (interpolated, presumably linear between the two points, extrapolated outside).
- This measured power feeds back into the HMC472A auto-cal loop (§5.7).
- Readings logged to UART.

### 5.10 ADC Inputs for Temperature Monitoring (§2.10)
- **MCP9700A**: 1 per RF board (up to 4) + 1 on processor board (so up to 5 total instances).
  - ADC ref: **2.5V**. ✅ CORRECTED (run 02): SOW states **400 mV @ 0 °C**, **10.0 mV/°C**; the MCP9700A data sheet gives **500 mV @ 0 °C**, **10.0 mV/°C** (see §2.5 flag). Rated **-40 to +125 °C**; output swing 100 mV–1.75 V, within the 2.5 V ADC reference.
- **ADL5906 (labeled "ADL5906A" in this subsection)** temperature output: 1 per RF board (up to 4).
  - ADC ref: **2.5V**. Output: **1400mV @ 25°C**, **4.8 mV/°C** slope.
  - (⚠️ see combined-component flag in §2.5 — this is very likely the same physical ADL5906 IC's secondary temperature-sense pin, not a separate part.)
- All temperature readings logged to UART.
  > 🧩 CD-NOTE: Total temperature sensor instances = up to 4 (RF-board MCP9700A) + 1 (processor-board MCP9700A) + up to 4 (RF-board ADL5906 VTEMP) = **up to 9 temperature channels**, all of which must be continuously monitored against the two system-wide thresholds (105°C warning, 125°C alarm — §5.11/5.12) at a rate sufficient to satisfy the "critical internal temperatures" M&C reporting requirement and the ≥4 Hz measurement-rate spec (§4.4.4) if that rate requirement is read as applying system-wide rather than just to IF power.

### 5.11 Warnings (§2.11)
- **Temperature Warning**: triggered when **any** of the (up to 9) temperature sensors **exceeds 105°C**.
- Warnings logged to UART.
  > 🧩 CD-NOTE: Only one warning type is explicitly defined (temperature). The PRD/M&C "Read all warnings" command (§4.3.10) must therefore be architected as an **extensible warning-registry framework** (even though only 1 warning type exists today) rather than a single hardcoded bit, since the explicit command name "read all warnings" (plural) and the general Event-Recorder requirement to log "all detected internal warnings" implies the system is meant to scale to more warning types in the future — worth designing the warning/alarm subsystem as a generic, table-driven framework (ID, description, trigger condition, active/cleared state, timestamp-of-last-transition) from day one.

### 5.12 Alarms (§2.12)
- **Summary Alarm**: triggered when **any** of the (up to 17) status-input pins goes to fault (cross-ref §5.2/§5.3).
- **Over-Temperature Alarm**: triggered when **any** temperature sensor **exceeds 125°C** (20°C above the Warning threshold — a sensible hysteresis-free two-tier design, though note: no explicit *hysteresis/clearing* threshold is given for either the 105°C warning or 125°C alarm — e.g., does the warning clear immediately at <105°C, or is there a lower clear-threshold to prevent rapid on/off chattering near the boundary? **Gap** worth flagging.).
- Alarm states **and causes** must be logged to UART (i.e., not just "alarm active" but *which* input/sensor caused it — reinforces the per-source-detail note in §5.3).

### 5.13 UART Output for Board-Level Test (§2.13)
- Purpose: **factory test only** — prints status/engineering info.
- Must include **all** information from the preceding requirements (i.e., every logged event listed throughout §5.2–5.12 — register dumps, GPIO status changes, LED state changes, attenuator changes, detector readings, temperature readings, warnings, alarms).
- Additional developer-chosen content is explicitly **permitted** ("may include additional information in the UART output as needed").
- **UART config**: **115200 baud, 8 data bits, no parity, 1 stop bit (8N1)**, output only (no mention of UART command input — this is confirmed to be a **one-way diagnostic/log output**, not an interactive console, consistent with "UART output" phrasing throughout).
  > 🧩 CD-NOTE: Given the breadth of content required on this single UART (full register dumps for 2 SPI devices × up to 4 boards, 17 status-pin transitions, LED state, 2 attenuator types × multiple boards' worth of bit-pattern changes, up to 10 analog-channel readings, warnings, and alarms), the PRD should specify a **structured, consistent log-line format** (e.g., timestamped, tagged by subsystem, similar to or even directly reusing the RFC 5424 format already mandated for the Event Recorder, §4.6) so the same logging subsystem/formatter can serve both the UART diagnostic stream and the persisted Event Recorder, reducing duplicate logging-code paths. This is a strong architectural recommendation, not a stated requirement.

---

## 6. Test Requirements (SOW "Test" section)

- Vendor (CD) must develop **automated test software** covering **all** requirements in the document.
- Vendor must **complete testing** and produce a **test report** showing verification of all requirements.
- **Final test** must be performed on the **actual target application board(s)** (not just eval boards) provided by AmpliTech.
- Vendor must give **AmpliTech ≥5 business days' notice** before final test; AmpliTech may attend/observe at the vendor's site.
  > 🧩 CD-NOTE: "Automated test software covering all requirements" implies a formal **requirements traceability matrix (RTM)** should be built and maintained throughout the project — mapping every numbered SOW requirement (and, ideally, every PTS/PRD-derived requirement from this document) to one or more automated test cases, feeding directly into SDRL 06 (Software verification test report). Recommend the PRD formally require an RTM as a living artifact from the start of the project, not only at the end.
  > 🧩 CD-NOTE: Because final test requires physical target boards that aren't available until "tentatively 10 weeks after EDC" (per the Hardware section) — yet Progress Demonstration 1 (50% of requirements) is scheduled for completion by business-day ~25 (roughly 5 weeks) and Progress Demonstration 2 (90%) by ~day 45 (roughly 9 weeks) — **both of the first two progress demonstrations necessarily occur before target hardware exists**, meaning CD's test software must be designed from day one to run equally well against **manufacturer evaluation boards** and the eventual target boards (i.e., hardware-abstracted test harness, not hardcoded to one board revision's quirks). This is an implicit but important test-architecture requirement.

---

## 7. Contract, IP, and Licensing Requirements

- Preferred contract type: **Firm Fixed Price**; **Time & Materials** also acceptable.
- **Full IP assignment** to AmpliTech upon final payment, **except** pre-existing material the vendor explicitly identifies in the proposal and AmpliTech agrees to.
- Vendor must supply an **inventory list of all pre-existing/third-party code**, including **exact version numbers and license types** (i.e., a formal Software Bill of Materials / SBOM is required).
- **Open-source restriction**: no "restrictive" licenses (explicitly calls out **GPL, AGPL**) without prior written consent.
  > 🧩 CD-NOTE: This restriction has direct technical consequences for the SNMPv3-vs-REST decision (§3). Every candidate library (TLS stack, TCP/IP stack, SNMP agent, JSON/OpenAPI tooling, RTOS) must be vetted against the GPL/AGPL restriction before selection, with a documented license-compliance checklist.
  > ✅ CORRECTED (run 02) — license facts, now sourced (run 01's statements were unsourced and partly wrong):
  >   - **Mbed TLS:** dual-licensed **Apache-2.0 OR GPL-2.0-or-later**; users choose which license to take the code under, so it can be used under Apache-2.0. (Mbed TLS ChangeLog/CONTRIBUTING, 2.28.6 and later.)
  >   - **wolfSSL:** copyleft (GPL) or a paid commercial license. Sources disagree on the GPL version: wolfssl.com/license says GPLv2; the repository LICENSING file says GPLv3. wolfssl.com lists commercial licenses at USD 7,000 per end product/SKU. Either GPL version is restricted under this SOW.
  >   - **Net-SNMP:** run 01 called it "dual-licensed"; it is not. Its license is a composite of BSD-style and MIT-style permissive notices from multiple copyright holders (SPDX `Net-SNMP`). Its suitability for an MCU target was not assessed.
  >   - **lwIP SNMP agent:** its SNMPv3 header defines only MD5 and SHA(-1) authentication and DES/AES privacy, which does not meet the SOW's SHA-512 requirement without extension. **Oryx CycloneTCP's** SNMP agent supports SHA-224 through SHA-512 and AES-CFB, but is licensed **GPL-2.0-or-later** (commercial terms not researched).
- **30-day post-handover warranty**: bugs found by AmpliTech in that window fixed at **zero cost**.

---

## 8. Deliverables (SDRL List) — SOW "Deliverables" section

AmpliTech has **5 business days** to review/accept each deliverable.

| SDRL | Deliverable |
|---|---|
| 01 | Preliminary Software Functional Description Document |
| 02 | Preliminary Software Interface Control Document (ICD) — command structures for the Ethernet LNB interface |
| 03 | Final Software Functional Description Document |
| 04 | Final Software Interface Control Document |
| 05 | Functional software for AmpliTech's preliminary test (on eval boards, tied to 100% demo on target boards per milestones — see note) |
| 06 | Software verification test report |
| 07 | Final software source code — **full private Git repository transfer**, including **complete commit history, branches, and tags** |
| 08 | Final compiled software, ready for installation on the processor |
| 09 | Build and Run Instructions — tools/versions used, environment setup scripts, dependency manifests, deployment instructions |
| 10 | Final automated test software source code — same build/tooling documentation requirements as SDRL 09, applied to the test suite |

> 🧩 CD-NOTE: SDRL 02/04 (ICD) is explicitly scoped as "command structures for communicating with and controlling the LNB" — for the SNMP path this strongly implies the **MIB file(s)** are a required component of this deliverable (cross-ref §4.2 note); for the REST path this implies the **OpenAPI 3.1.1 YAML/JSON spec file(s)** themselves are the deliverable artifact, not just prose documentation describing them. Recommend the PRD state explicitly: "ICD = [MIB files | OpenAPI specification files] + supporting prose," so the deliverable format is unambiguous and machine-readable/importable by the end customer's M&C tooling.
> 🧩 CD-NOTE: SDRL 07's requirement for "full commit history, branches, and tags" to transfer ownership implies CD must manage the project in a **single, clean, well-organized Git repository from day one** (with a deliberate branching/tagging strategy, e.g., tags at each milestone/SDRL delivery) since the entire repo — warts and all — becomes a customer-visible/owned deliverable at project end, not just a final source drop. This has practical implications for commit hygiene, secrets-scanning (must never commit credentials/keys), and possibly squashing/cleaning any exploratory branches before final handover.
> 🧩 CD-NOTE: SDRL 09/10's "description and version of all tools used to develop and build" implies a **reproducible build environment** specification. ✅ CORRECTED (run 02): the toolchain is no longer a guess — the KIT_XMC72_EVK guide states ModusToolbox™ is used to develop and debug XMC7200D projects, and the ModusToolbox run-time reference guide (002-30738 Rev. *W) lists XMC7200-specific Ethernet libraries (`ethernet-connection-manager`, `ethernet-core-freertos-lwip-mbedtls`, `ethernet-phy-driver`). The PRD should still require pinned tool/library versions, build scripts, and a scripted environment setup so AmpliTech can rebuild the same binary without CD.

---

## 9. Milestones, Acceptance Criteria, Payment Structure (Firm Fixed Price)

| # | Milestone | Deliverables | Payment |
|---|---|---|---|
| 1 | Preliminary Design Review complete | SDRL 01, 02 | 10% |
| 2 | Progress Demo 1 — eval boards, ≥50% requirements implemented | SDRL 03, 04 | 30% |
| 3 | Progress Demo 2 — eval boards, ≥90% requirements implemented | (none listed) | 30% |
| 4 | Progress Demo 3 — target boards, 100% requirements implemented | SDRL 05 | 15% |
| 5 | Project Completion | SDRL 06, 07, 08, 09, 10 | 15% (final) |

> 🧩 CD-NOTE: Payment is **milestone/percentage-of-requirements-implemented** based, which makes the Requirements Traceability Matrix (§6 note) doubly important — "50%/90%/100% of requirements implemented" must be objectively, jointly measurable (against a mutually agreed requirements count/list) to avoid payment disputes. **Recommend the PRD itself become the literal numbered checklist used to measure "% implemented" against**, agreed with Union/AmpliTech before PDR, rather than an informal/subjective measure. This elevates the PRD from a planning document to a **contractual acceptance artifact**.

---

## 10. Schedule

- Tentative EDC: **Nov 9, 2026**. Total contract duration: **15 business weeks (75 business days)**.
- Target application boards promised "tentatively 10 weeks after EDC" in the Hardware section, but the detailed schedule table shows **60 business days** (~12 weeks) for "Target Application boards provided to vendor," finishing **Thu 2/11/27** — a minor inconsistency between the prose ("10 weeks") and the schedule table ("~12 weeks/60 days") worth reconciling.
  > ⚠️ CD-FLAG: Confirm with Union/AmpliTech whether target hardware arrives at week 10 or week 12 — a 2-week slip this late in the schedule compresses Progress Demo 3 and final test/handover significantly, since Demo 3 (100% on target boards) is scheduled only ~1 week after target boards even arrive (Thu 2/11/27 boards arrival → Thu 2/18/27 Demo 3 complete) and Project Completion only 2 weeks after that (Thu 3/4/27). This is an aggressive hardware-bring-up-to-final-demo timeline (~1 week) that assumes the target boards work essentially identically to eval boards — a risk worth flagging explicitly in the PRD's risk register.
- Full schedule table (durations/dates as stated) reproduced:
  | Task | Duration | Start | Finish |
  |---|---|---|---|
  | Software Development Schedule (overall) | 75 days | Mon 11/9/26 | Thu 3/4/27 |
  | EDC (tentative) | 0 days | Mon 11/9/26 | Mon 11/9/26 |
  | PDR, SDRL 01/02 | 10 days | Mon 11/9/26 | Fri 11/20/26 |
  | Progress Demo 1, SDRL 03/04 | 25 days | Mon 11/23/26 | Thu 1/7/27 |
  | Progress Demo 2 | 20 days | Fri 1/8/27 | Thu 2/4/27 |
  | Target boards delivered | 60 days | Mon 11/9/26 | Thu 2/11/27 |
  | Progress Demo 3, SDRL 05 | 5 days | Fri 2/12/27 | Thu 2/18/27 |
  | Project Completion, SDRL 06–10 | 10 days | Fri 2/19/27 | Thu 3/4/27 |

---

## 11. Appendices (as currently provided — both empty/TBD)

- **Appendix A — ADF4368 Register Values**: **TBD**.
- **Appendix B — ADAR2001 Register Values**: **TBD**.
  > ⚠️ CD-FLAG (repeated for visibility): These are hard blockers for completing §5.5's synthesizer/multiplier bring-up work and should be tracked as the #1 "needed from customer" action item in the PRD's open-items list.

---

## 12. Consolidated Gap / Open-Question List (for direct follow-up with Union)

This is a compiled index of every `⚠️ CD-FLAG` raised above, for quick reference:

1. ~~Block diagrams are unreadable `.emf` files~~ — **closed (run 02):** extracted to `media/image1.png` / `media/image2.png`; summarized in §1.
2. **Mute/unmute function** referenced only via "unmuted operating hours" — never otherwise defined (§4.3.12). **Highest-priority functional gap.**
3. **"HMI"** referenced for Event Recorder download — no HMI (web UI, front panel, etc.) is otherwise specified anywhere (§4.6). **Second-highest-priority gap.**
4. **ADF4368 and ADAR2001 register values (Appendices A & B) are TBD** — blocks SPI bring-up and board-3/4 register sets aren't addressed even once supplied (§5.5, §11).
5. **"ADL5906" vs "ADL5906A"** — confirm single physical part with dual-purpose (power + temp) outputs, not two chips (§2.5, §5.10).
6. **MCP9700A transfer function in SOW 2.10.3 is wrong** — SOW pairs 400 mV (MCP9701/9701A offset) with 10.0 mV/°C (MCP9700/9700A slope). MCP9700A data sheet: 500 mV, 10.0 mV/°C. Using 400 mV reads 10 °C high (§2.5, §5.10; Q19).
7. ~~ADL5904 2-point calibration arithmetic doesn't reconcile~~ — **withdrawn (run 02):** both points shift by −4 dB, consistent with 12 dB attenuation plus ≈ +8 dB net LNA/filter gain shown in Figure 2. Remaining item: confirm LNA/filter net gain (§5.9; Q26).
8. ~~"ADAR2001" called a "multiplier"~~ — **withdrawn (run 02):** the ADAR2001 *is* a 4× frequency multiplier (ADI data sheet). New, smaller item: the SOW does not say whether the ADAR2001's on-chip temperature sensor or state machines are used (§2.5; Q28).
9. **LT80603 datasheet link missing** from References — part exists (ADI LT80602/LT80603 data sheet, Rev. 1); confirm variant and status pin (§2.4).
10. **MAX5969A** — now sourced (run 02). New items: (a) MAX5969A has legacy 36 V UVLO, MAX5969B has the 802.3af/at-compliant 40 V UVLO — confirm variant; (b) confirm which output (PG or 2EC) is the SOW's "status signal" (§2.3).
11. **No DHCP/IPv6/static-IP policy stated** for "Set IP address" (§4.3.5).
12. **Out-of-range / non-standard-increment attenuator command behavior undefined** (reject? clamp? error code?) (§4.3.6, §5.6).
13. **Factory Reset scope undefined** — what state is wiped vs. preserved (§4.5).
14. **"Leading zero padded" alphanumeric set-at-test fields** — ambiguous requirement, needs field-width/format clarification (§4.9).
15. **RF-board presence/count detection mechanism undefined** — status-pin pull-ups make "absent" indistinguishable from "healthy." Run 02 proposes: configured population authoritative, SPI identity probe at boot, mismatch = fault (§1, §5.2; Q03).
16. **No hysteresis/clear-threshold defined** for 105°C warning / 125°C alarm (§5.12).
17. **No PHY link-state/Ethernet-fault reporting requirement**, despite PHY being explicitly "high immunity" (§2.2).
18. **No account security policy** (lockout, password complexity, session timeout) for RBAC/authentication (§4.1).
19. **Exact SNMPv3 auth protocol** ("SHA-512" → `usmHMAC384SHA512AuthProtocol`, the only SHA-512-based protocol in RFC 7630) needs confirming; and RFC 7630 is obsoleted by RFC 7860 (§3).
20. **REST vs. SNMPv3 is an unresolved, architecture-gating fork** — recommend forcing this decision early (§3).
21. **"Roll back to prior versions" (plural)** — depth of version history to retain is unspecified (§4.8).
22. **IF-detector calibration modulation/waveform type unspecified** (§5.8).
23. **20dB coupler factor labeled "reference only"** — needs authoritative value from AmpliTech hardware design, with tolerance (§5.8).
24. **Board-3/4 figures referenced as the stated max ("up to 4") but never fully provisioned with data** (register values, etc.) anywhere in the document (§5.5, §11).
25. **Target hardware delivery timeline inconsistency**: prose says "~10 weeks after EDC," schedule table shows 60 business days (~12 weeks) (§10).
26. **RGMII/RMII/SGMII MAC-PHY interconnect** between XMC7200 and DP83869 not specified — needed for pinout review (§5.1).
27. **Dual-MAC headroom on XMC7200** (2 Ethernet MACs vs. 1 required port) — confirm second MAC is genuinely unused (§2.1).
28. **Oscillator stability/TCXO question** for the ≤1-second time-error requirement during NTP-outage periods (§4.7).

---

## 13. Suggested Next Steps (process note, not a technical requirement)

1. ~~Convert and insert the two `.emf` block diagrams~~ — done (run 02); see §1.
2. ~~Re-attempt direct datasheet fetches~~ — done for MAX5969A, ADF4368, ADAR2001, HMC425A, HMC472A, ADL5906, ADL5904, MCP9700A, LT80603 (run 02). Still to do: full register maps for ADF4368/ADAR2001 (needed with Appendices A/B) and the DP83869HM data sheet details for the pin-out review.
3. Send the consolidated Gap/Open-Question list (§12) to Union/AmpliTech as a formal RFI (Request for Information) ahead of PDR, since several items (Appendices A/B, the mute function, the HMI reference) are true blockers to producing a complete PRD.
4. Use this PTS as direct input to draft the PRD's requirement-numbering scheme, ideally preserving traceability back to the SOW's own section numbers (1.1, 1.3.1.6, 2.8, etc.) for the milestone-acceptance RTM described in §9.

---

*End of pts-run-02.md — pass 1 corrections. Further passes pending.*
