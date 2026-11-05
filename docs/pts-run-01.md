# Primary Technical Source (PTS) — Run 01

**Derived from:** `union-sow_rev02.md` (RFP AGMDC0011, Union Embedded Software Development — LNB Command & Control)
**Purpose:** Exhaustive, non-compressive extraction of every technical fact, requirement, constraint, and implied need in the source SOW, expanded with primary reference links, datasheet-derived specifications, and Connected Development (CD) analyst commentary. This is raw input material for a future Product Requirements Document (PRD) — not a summary.
**Author:** Bob (GitHub Copilot CLI), first pass
**Status:** DRAFT — for human review and iteration (expect `pts-run-02.md` etc. after feedback)

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

- Two block diagrams are referenced but **not embedded as readable content** in the Markdown — both are `.emf` (Windows Enhanced Metafile) images (`media/image1.emf` "LNB Block Diagram", `media/image2.emf` "Processor Board").
  > ⚠️ CD-FLAG: `.emf` files are not natively viewable by most non-Windows tooling and were not extracted/converted in this pass. **Action item:** convert both to PNG/SVG and insert directly into the PTS/PRD — these diagrams likely contain critical topology information (RF board count/wiring, signal routing, power rails) not captured anywhere in the prose. This is a high-priority follow-up before the PRD is finalized.
- **Top-level interface summary (explicitly stated):**
  - End-customer manage & control interface: **Ethernet** (single physical interface, see §2).
  - Internal component interface: **SPI** (synthesizer and multiplier register access).
  - **GPIO inputs**: status signals from RF boards and power regulators.
  - **GPIO outputs**: LED control, step-attenuator control bits.
  - **Analog inputs** (via ADC): IF power detectors, 100 MHz reference power detector, temperature sensors.
  - **UART output**: board-level test/engineering console, one-way (output only), 115200/8/N/1.

> 🧩 CD-NOTE: The SOW never explicitly numbers "up to 4 RF boards" as a fixed design point — it's a repeated upper bound ("up to 4 RF boards") appearing at every subsystem (status GPIO, synthesizer, multiplier, IF attenuator, IF detector, RF-board temp sensor). The PRD should state this as a **hard architectural constant: support for 1–4 RF boards, each independently addressable**, and treat "which of the 4 are physically populated" as runtime-detectable (via the status GPIO weak-pullup behavior, see §2.2) rather than configured at build time only.

> 🧩 CD-NOTE: A clean internal software architecture implied by the SOW's structure: (1) Ethernet/M&C protocol stack (REST or SNMPv3) → (2) a device/business logic layer (alarms, warnings, event recorder, calibration, state machine) → (3) hardware abstraction layer (SPI driver, GPIO driver, ADC driver, UART logger) → (4) per-RF-board instance objects (1..4) each owning its own ADF4368, ADAR2001, HMC425A, ADL5906, MCP9700A. This maps directly to a PRD "Software Architecture" section.

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
  - Ethernet: **2× Ethernet, up to 10/100/1000** (note: page text was truncated to "10/103" — almost certainly "10/100/1000" MAC capability; should be confirmed against the full datasheet, but this is consistent with SOW's 1000 Base-T requirement)
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

  > 🧩 CD-NOTE: The CPU has **2 built-in Ethernet MACs**. The SOW requires only a single externally exposed Ethernet port (§3.1), but the dual-MAC capability could be a hardware path for a future redundant/dual-homed network requirement, or could simply mean one MAC is unused. PRD should note this as available-but-unused headroom, and confirm with AmpliTech whether the second MAC has any board-level connection (relevant to the referenced but un-rendered block diagram).
  > 🧩 CD-NOTE: With **99 ADC channels** and **102 timed I/O (GPIO) channels** available on the host MCU, the processor has enormous I/O headroom relative to the 4-RF-board maximum design point. The PRD's pin budget should be computed explicitly (see §9 "Pin/Channel Budget Analysis" below) to confirm this processor choice is not a bottleneck even at the 4-board maximum.
  > 🧩 CD-NOTE: Built-in hardware crypto + Cortex-M7 with FPU strongly supports running a TLS 1.3 stack (§3.2.1.3) and SHA-2/SHA-3 firmware signature verification (§7.6.4) without a companion secure element, but the PRD should explicitly decide whether a hardware security module / secure boot root-of-trust is required for firmware authentication, since the SOW only specifies the hashing/signing *algorithm family*, not a secure-boot architecture.
  > ⚠️ CD-FLAG: "DSP Functionality: Yes" is listed on the product page but the SOW makes no DSP-related requirement. Likely irrelevant to this project (no signal processing of RF/IF waveforms is requested — only detector *voltage* reads) but worth a one-line confirmation in the PRD that no DSP firmware is in scope.

### 2.2 Ethernet PHY — Texas Instruments DP83869HMRGZR (candidate)

- SOW link: https://www.ti.com/product/DP83869HM/part-details/DP83869HMRGZR
- Verified via TI: **"DP83869HM High Immunity 10/100/1000 Ethernet Physical Layer Transceiver With Copper and Fiber Interface"** — datasheet currently at Rev. E.
  > 🔗 CD-REF: Direct datasheet PDF: https://www.ti.com/lit/gpn/DP83869HM (and HTML viewer: https://www.ti.com/document-viewer/DP83869HM/datasheet)
  > 🧩 CD-NOTE: Part name confirms **both copper (RJ45/magnetics) and fiber (SGMII/SFP-style) interface support** at the PHY level. The SOW only discusses a copper 1000 Base-T / 100 Base-T interface — the fiber capability of this specific PHY appears unused in this design. PRD should confirm copper-only is in scope and that no SFP/fiber path exists on the target board.
  > 🧩 CD-NOTE: "High Immunity" in the product name typically denotes extended **surge/EMI/ESD robustness**, consistent with outdoor ground-station deployment (lightning/EMI exposure on long Ethernet/PoE cable runs). This reinforces that environmental robustness is an (unstated) design driver — the PRD's "Reliability/Environmental" section should mention this to connect the hardware rationale to software requirements like watchdog recovery and link-state fault handling, which the SOW does **not** explicitly call out as a software requirement (see Gaps, §12).
  > ⚠️ CD-FLAG: The SOW does not specify a software requirement to detect/report **Ethernet link-down/link-up events** or **PHY fault conditions** to the event log or UART, despite dedicating this much hardware attention to PHY robustness. This looks like a gap — recommend adding to PRD as an inferred requirement.

### 2.3 PoE Controller — Analog Devices MAX5969A (candidate)

- SOW link: https://www.analog.com/en/products/max5969a.html
  > ⚠️ CD-FLAG: analog.com was unreachable from this research environment (network/bot-blocking) during this pass, so its exact electrical specs could not be independently verified here. From general industry knowledge, the **MAX5969A is an IEEE 802.3at (PoE+) powered-device (PD) interface controller** supporting classification and up to ~25.5W class 4 PoE+ delivery — consistent with the SOW's IEEE 802.3at reference (§4). **Action item: re-fetch/verify datasheet specifics (classification class, inrush limiting, UVLO thresholds, isolation requirements) in a follow-up pass** — these affect whether the software needs to read/report PoE class/power-good status via GPIO (a plausible additional status input not explicitly listed among the "up to 12 LT80603 + 1 POE controller" status pins in §9.1, but worth double-checking against the actual MAX5969A status pin behavior).
  > 🧩 CD-NOTE: The SOW already defines one software-visible status signal from the "POE controller, MAX5969A" (§9.1 below, open-drain, pull-up to 3.3V, active-low fault) — so basic PoE fault monitoring IS in scope. The flag above is only about whether *additional* PoE telemetry (negotiated class, power budget) should also be exposed via M&C — currently not requested, but a reasonable PRD "nice to have."

### 2.4 Power Regulators — Analog Devices/Linear Technology LT80603 family (referenced, no datasheet link in SOW)

- SOW references "LT80603, up to 12" power regulator status signals (§9.1).
  > ⚠️ CD-FLAG: **No datasheet URL was provided in the SOW's References section for the LT80603**, unlike every other component. This is a gap — the PRT/PRD should request the exact orderable part number (the "LT8060" family from Analog Devices/Linear Technology is a µModule step-down regulator family with multiple voltage/current variants, e.g., LT8050/LT8060-series µModule regulators) so CD can confirm the **PGOOD/status pin electrical behavior** (open-drain active-low fault, per SOW text) actually matches the real part before committing to the GPIO-polarity assumptions in §9.3.
  > 🧩 CD-NOTE: 12 instances of a single regulator status line is a lot of discrete fault inputs (3 per RF board × 4 boards, or a mix of board + shared rails) — PRD should ask AmpliTech/Union for the power-rail map (which 12 rails, powering what) so the software's power-sequencing/fault-correlation logic (e.g., "which fault caused the LED to go red, and should it suppress nuisance faults during power-up sequencing") can be designed correctly. The SOW's "no fault" state requirement for LED (§9.4) depends on *all* of these 12 + 1 PoE + up to 4 RF-board status pins being simultaneously healthy — this is effectively a 17-input AND-gate implemented in firmware, and the state machine should debounce/sequence these to avoid false "fault" indications during normal power-up transients.

### 2.5 RF-Chain Components (per RF board, up to 4 boards)

| Component | Function | SOW Link | Verified Notes |
|---|---|---|---|
| **ADF4368** | Microwave synthesizer (LO generation) | https://www.analog.com/en/products/adf4368.html | 🔗 Known publicly as ADI's ultralow jitter, 12.15 GHz fundamental, fractional-N PLL. SPI-controlled register interface. Not independently re-verified this pass (analog.com blocked); confirm full register map and jitter/phase-noise specs in next pass. |
| **ADAR2001** | "Multiplier" (per SOW's wording) | https://www.analog.com/en/products/adar2001.html | ⚠️ CD-FLAG: ADI's ADAR2001 is actually marketed as a **4-channel, 5.9–10.2 GHz True Time Delay (beamforming) IC**, not a classic analog "multiplier" in the mixer sense. The SOW's use of the word "multiplier" may be the customer's informal term for this part's role in the signal chain (e.g., LO multiplication/distribution for up to 4 channels) rather than its literal datasheet classification. **Recommend clarifying with Union/AmpliTech exactly what RF function the ADAR2001 performs in this design** — this affects what register fields the firmware needs to expose/manage, and whether "multiplier" implies any software-controllable phase/gain-per-channel behavior beyond the fixed power-up register values stated in §9.5.2. |
| **HMC425A** | IF step attenuator (per RF board) | https://www.analog.com/en/products/hmc425a.html | 6-bit digital step attenuator, 0.5–31.5 dB range in 0.5 dB steps (per truth table in SOW, reproduced in §9.6 below). GaAs MMIC, positive-logic control per SOW's truth table. |
| **HMC472A** | 100 MHz reference step attenuator (single, shared) | https://www.analog.com/en/products/hmc472a.html | Same 6-bit truth table topology as HMC425A; used specifically for a 100 MHz reference signal path, with an automatic self-calibrating attenuation algorithm (§9.7) rather than Ethernet-commanded control. |
| **ADL5906** | IF (3.5–5.5 GHz band) RMS power detector + integrated temp sensor | https://www.analog.com/en/products/adl5906.html | Log-linear RMS detector; SOW specifies slope ~42–52 mV/dB (reference only) and 0.3–3.8V output range against a 4V ADC reference. Also referenced again in §9.10 as "ADL5906A temp sensor" — see flag below. |
| **ADL5904** | 100 MHz reference power detector (single, processor board) | https://www.analog.com/en/products/adl5904.html | Output range 0.45–1.45V against 1.8V ADC reference; ~35 mV/dB typical slope (reference only). |
| **MCP9700A** | Analog temperature sensor (processor board + each RF board) | https://www.microchip.com/en-us/product/mcp9700a | Linear analog temp sensor, 10.0 mV/°C, rated -40 to +125°C. |

> ⚠️ CD-FLAG (important): §9.10.4 of the SOW labels a sensor as **"ADL5906A"** ("One ADL5906A temp sensor on each RF board") with a *different* scale factor (4.8 mV/°C, 1400 mV @ 25°C) than the ADL5906 power detector described in §9.8 (which the References section links as plain "ADL5906", no "A" suffix, with the 42–52 mV/dB *power* slope). This is almost certainly **the same physical IC's secondary analog temperature-monitor output (VTMP pin)** being described twice under two slightly different names/section contexts, not two different chips. **This needs explicit clarification from Union**: (a) confirm there is only one ADL5906 per RF board (not an "ADL5906" for power + a separate "ADL5906A" for temperature), and (b) confirm which ADC channel/pin map corresponds to the power-detector output vs. the temperature output of the same device. Getting this wrong would cause the PRD to spec a nonexistent component.

> ⚠️ CD-FLAG: Similarly, §9.10 lists **MCP9700A offset voltage as 400 mV at 0°C**. Public MCP9700/MCP9700A datasheets (not independently re-confirmed in this network-restricted pass, but well-established in general industry knowledge) typically state **V₀°C ≈ 500 mV typical** with a 10.0 mV/°C slope. If the SOW's "400 mV" is accurate it may reflect a deliberately different reference design or an error in the SOW; either way, **the exact calibration constant must be taken from the real datasheet (or empirically calibrated), not assumed from the SOW prose**, since a 100 mV error at the offset directly shifts every reported temperature by ~10°C — directly relevant to the 105°C/125°C warning/alarm thresholds (§9.11, §9.12), where a 10°C error could cause false/missed alarms. **High-priority verification item.**

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

> ⚠️ CD-FLAG: RFC 7630 defines **four** HMAC-SHA-2 auth protocols: `usmHMAC128SHA224AuthProtocol`, `usmHMAC192SHA256AuthProtocol`, `usmHMAC256SHA384AuthProtocol`, and `usmHMAC384SHA512AuthProtocol`. The SOW simply says **"SNMP USM Authentication: SHA-512."** There is no auth protocol literally called "SHA-512" in RFC 7630 — the closest/strongest is `usmHMAC384SHA512AuthProtocol` (384-bit truncated output over SHA-512). **The PRD must pin down the exact protocol OID/name (almost certainly usmHMAC384SHA512AuthProtocol) with Union before implementation**, since SNMP client/manager interoperability depends on exact protocol identification, not just the hash family name.

> 🧩 CD-NOTE: Because RFC 3413/3414/3415/3826/7630 collectively require a **full SNMPv3 agent stack** (USM + VACM + AES-CFB + HMAC-SHA2), and the RESTful alternative requires **OpenAPI 3.1.1 + HTTPS/TLS1.3 + a customer-approved auth scheme + RBAC**, this is effectively **two complete, mutually exclusive M&C subsystems** that satisfy the same functional requirement set. The PRD should treat "REST vs. SNMPv3" as a **Day-1 architectural decision gate**, not a feature to build in parallel — each path has a very different protocol stack, security model, and third-party library dependency (e.g., an embedded SNMPv3 agent library like Net-SNMP's agentx or a from-scratch USM/VACM implementation, vs. an embedded HTTPS server + TLS 1.3 library like mbedTLS/wolfSSL + a REST routing layer). **Recommend this be an explicit open question posed back to Union/AmpliTech/end-customer early, since it gates nearly the entire software architecture.**

> 🧩 CD-NOTE: Given the Infineon XMC7200 has a hardware crypto accelerator (§2.1), both TLS 1.3 and AES-CFB/HMAC-SHA2 crypto operations can likely be hardware-accelerated — worth a specific feasibility/benchmarking spike early in the project since SNMP's "≥4 measurements/second" M&C responsiveness requirement (§5.2 below) could be affected by crypto overhead per request if TLS/USM crypto is done in software.

---

## 4. Ethernet / Network Interface Requirements (SOW §1.x)

- **Req 1.1 — Physical layer**: Single **1000 Base-T** Ethernet interface (gigabit copper). **100 Base-T is under consideration** as a simplification; vendor (CD) explicitly asked to provide **cost/schedule comparison of 1000BASE-T vs 100BASE-T** implementations as part of the proposal.
  > 🧩 CD-NOTE: This is an open decision point requiring a CD deliverable (a short trade study) *before or during* the proposal stage, not just an engineering nice-to-have. The PRD/proposal response should explicitly address: MAC/PHY driver complexity delta, RJ45 magnetics/PoE interaction differences, achievable throughput headroom for firmware updates (§7.6) and event-log retrieval, and the DP83869's support for both speeds (it supports 10/100/1000 natively, so no PHY change is implied — the cost/schedule delta is almost purely software/driver scope and possibly test time).
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
  > 🧩 CD-NOTE: "Error ≤1s" and "precision ≤1ms" are two different metrics (accuracy vs. resolution) — worth the PRD distinguishing: the clock's *tick resolution*/representation must support ≤1ms granularity (straightforward given a 350MHz CPU with a hardware RTC/timers), while the *absolute accuracy vs. true time* must stay within 1 second even without continuous NTP sync (e.g., during network outages) — this implies a requirement for the local RTC's drift characteristics (crystal tolerance, typically ±20-100ppm for standard crystal oscillators) to be evaluated against how long the unit might run un-synced, since 1 second of drift at 50ppm would take roughly 5.5 hours — the PRD should ask AmpliTech what NTP-outage duration must be tolerated while staying within 1 second, as this could necessitate a higher-stability oscillator (e.g., TCXO) — a hardware, not just software, consideration worth flagging back.
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

- Three fields, each settable only once via the Ethernet interface at factory test: **LNB Model Number, LNB Serial Number, Hardware LNB Revision**.
- Each field: **max 32 alphanumeric characters**, **leading-zero padded**, **dashes (-) allowed**.
  > ⚠️ CD-FLAG: "Leading zero padded" is an unusual constraint for an *alphanumeric* field (zero-padding normally applies to purely numeric fields/counters). Clarify with Union: does this mean (a) if the value is numeric, pad with leading zeros to a fixed width (and if so, what width — is it always 32, or some other field-specific width?), or (b) all fields must literally always be exactly 32 characters long with zero-padding on the left regardless of content type (which is unusual for e.g. a serial number containing letters and dashes)? This directly affects the data-validation/storage format the PRD must specify.
  > 🧩 CD-NOTE: These three values must survive firmware updates, reboots, and (per the Factory Reset gap in §4.5) very likely must survive factory reset too, since they're tied to the physical hardware unit, not to configuration state — the PRD should explicitly classify these (and calibration constants, §9.8–9.9) as "hardware-identity data," stored in a protected/separate non-volatile region from "user-configurable data" (network settings, accounts, roles) that *would* be cleared by factory reset.
  > 🧩 CD-NOTE: Because these are "set at test" and presumably **write-once** (or write-rarely, access-controlled) fields, the PRD should specify whether the write operation is idempotent/overwritable at all post-test, or truly one-time-programmable (OTP) at the software level — this affects whether a software bug could accidentally allow re-writing a serial number in the field, which would be a quality-escape risk for any serialized/traceable hardware product.

---

## 5. Internal LNB Requirements — SOW Section 2 (hardware-facing control loops)

### 5.1 Ethernet PHY Interface (§2.1 / orig. 2.1)
- Processor interfaces to the Ethernet PHY IC to satisfy all stated Ethernet/M&C requirements (§4 above). No additional electrical detail given (e.g., RMII/RGMII/SGMII interconnect between XMC7200 and DP83869 is not specified in the SOW text).
  > ⚠️ CD-FLAG: The MAC-to-PHY interconnect (RGMII is the most likely choice for a gigabit design with this PHY) is not specified — needed for schematic pinout review support (explicitly a CD responsibility per the Scope of Work, "schematic review of processor pin-out"). Recommend confirming RGMII (vs. other MII variants) early, since it affects which XMC7200 pins/peripheral instance are used and feeds directly into the SOW-mandated pinout review task.

### 5.2 Status GPIO Inputs from RF Board(s) (§2.2)
- One status signal **per RF board**, up to 4.
- **Active-high-good** logic: high = no fault, low = fault.
- **Weak pull-up** on each pin so an unconnected/absent RF board reads as "status good" (no fault) by default.
  > ⚠️ CD-FLAG: This default-pull-up-to-good behavior means the firmware **cannot distinguish "RF board not installed" from "RF board installed and healthy"** using the status pin alone. Combined with the "up to 4 RF boards" variable-population design (§1), this is a real gap: how does the software know how many RF boards are actually present, in order to decide which SPI devices/attenuator GPIOs/ADC channels to poll in the first place? Likely answers: (a) board presence is a build-time/config-time setting (not auto-detected), or (b) there's a separate presence-detect mechanism (e.g., an I2C EEPROM, a dedicated presence pin, or SPI device NACK/readback failure used as a presence proxy) not described in this SOW. **This should be explicitly raised with Union** — it directly affects whether "board count" is a runtime-discoverable property or a fixed configuration parameter per deployed unit, which in turn affects the M&C reporting model (should "Read all alarms" report alarms for boards that don't exist?).
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
  - Frequencies: **3.5 GHz, 4.5 GHz, 5.5 GHz** (modulated IF signal, not CW — relevant because RMS detectors respond differently to modulated vs. CW signals, and the datasheet's crest-factor correction tables are typically referenced to specific modulation types — **gap: which modulation/waveform type is used for calibration is unspecified**, should be clarified since it affects calibration repeatability).
  - Temperatures: **-40°C, +23°C, +55°C**.
  - Reference-only typical slope: **42–52 mV/dB**.
- Operational measurement: **average intercept/slope across frequency**, **interpolated across temperature**.
- Reference-only: a **20 dB coupler** is used in the signal path, so actual IF output power = detected power + 20 dB (i.e., the detector sees a sampled/coupled-down replica of the true output, and firmware's final "Output IF signal power level" M&C value (§4.4.1) must add this 20dB offset back in before reporting).
  > ⚠️ CD-FLAG: The 20dB coupler offset is called "reference only," meaning it's **not an authoritative firmware constant from the SOW** but background context — the actual coupling factor must be obtained from AmpliTech's hardware design/BOM (coupler part number/spec) and should not simply be hardcoded as "20dB" without confirmation, since real couplers have frequency-dependent coupling-factor tolerance (e.g., ±0.5-1dB variation across 3.5-5.5GHz) that could matter against the "≤2.0dB uncertainty" M&C spec (§4.4.2).
  > 🧩 CD-NOTE: The calibration/measurement scheme described (average slope/intercept across frequency, interpolated across temperature) is a **2-D (frequency × temperature) piecewise-linear calibration model** — the PRD should specify the exact interpolation method (linear is implied, but should state: linear interpolation between the 3 calibrated temperature points, and what extrapolation policy applies outside -40°C to +55°C, since operational temperature range could exceed this per the MCU's -40 to +125°C rating) and the exact data structure for storing the 27 (or fewer) calibration constants per detector per board (non-volatile storage requirement, tied into the factory-test calibration procedure that must be a documented step in SDRL 09/10 build-and-test instructions).
- IF detector readings logged to UART.

### 5.9 ADC Input for 100 MHz Reference Power Detector — ADL5904 (§2.9)
- Single unit on processor board (not per-RF-board).
- Output: **0.45–1.45 VDC** against **1.8V ADC reference**.
- **Factory calibration** (2-point, single frequency/temperature):
  - Set the 100MHz reference attenuator (HMC472A) to **12 dB** fixed during calibration.
  - Input power levels: **-18 dBm, +10 dBm** (at the attenuator input) → equivalent to **-22 dBm and +6 dBm at the detector input** (i.e., the 12dB attenuator setting shifts input levels down by 12dB to the detector — wait, -18-12=-30, not -22; **this arithmetic doesn't reconcile cleanly** — ⚠️ CD-FLAG: -18dBm input through a 12dB attenuator should yield -30dBm at the detector, not the stated -22dBm; similarly +10dBm through 12dB attenuation should yield -2dBm, not the stated +6dBm. **This is a numerical inconsistency in the SOW that should be raised directly with Union for correction** — either the attenuator setting, the stated input levels, or the stated detector-input-equivalent levels contain a typo/error. This matters because it directly defines the two-point calibration curve for this detector.)
  - Calibration frequency: **100 MHz**; temperature: **23°C** (single temperature only — no multi-temperature correction for this detector, unlike the ADL5906, which the PRD should note as an accepted asymmetry, not an oversight, given reference-only language).
  - Reference-only typical slope: **35 mV/dB**.
- **Detected power calculation** (2-step, explicitly defined method):
  1. ADC counts → voltage (via known ADC reference voltage, 1.8V).
  2. Voltage → power level via the 2-point calibration (interpolated, presumably linear between the two points, extrapolated outside).
- This measured power feeds back into the HMC472A auto-cal loop (§5.7).
- Readings logged to UART.

### 5.10 ADC Inputs for Temperature Monitoring (§2.10)
- **MCP9700A**: 1 per RF board (up to 4) + 1 on processor board (so up to 5 total instances).
  - ADC ref: **2.5V**. Output: **400mV @ 0°C** (⚠️ see discrepancy flag in §2.5 table above — likely should be verified against real datasheet, commonly 500mV), **10.0 mV/°C** slope, rated **-40 to +125°C**.
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
  > 🧩 CD-NOTE: This restriction has direct technical consequences for the SNMPv3-vs-REST architectural decision (§3 note): common embedded open-source SNMP stacks (e.g., **Net-SNMP** is dual-licensed but has BSD-style licensing for its core; however many embedded TLS stacks have licensing nuances — e.g., **mbedTLS** is Apache 2.0/GPL-2.0 dual-licensed, **wolfSSL** is GPLv2 or commercial). **The PRD/SBOM planning must explicitly vet every candidate third-party library (TLS stack, TCP/IP stack, SNMP agent, JSON/OpenAPI tooling, RTOS if used) against this GPL/AGPL restriction before selection**, likely steering toward permissively-licensed or commercially-licensed options (e.g., commercial wolfSSL license, or an Apache/BSD-licensed mbedTLS usage) to avoid licensing rework later. This should be an explicit, early PRD/architecture decision with a documented license-compliance checklist.
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
> 🧩 CD-NOTE: SDRL 09/10's "description and version of all tools used to develop and build" implies a need for a **reproducible build environment** specification — e.g., a documented toolchain version (compiler, IDE/SDK — likely Infineon's ModusToolbox for the XMC7200 family, worth confirming), build scripts (Makefile/CMake), and ideally a containerized or scripted environment-setup process (Docker, or a documented VM/package-manager recipe) so AmpliTech can reliably rebuild the exact same binary years later without CD's involvement. This should be an explicit PRD "Reproducible Build" requirement.

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

1. **Block diagrams are unreadable `.emf` files** — need conversion/extraction (§1).
2. **Mute/unmute function** referenced only via "unmuted operating hours" — never otherwise defined (§4.3.12). **Highest-priority functional gap.**
3. **"HMI"** referenced for Event Recorder download — no HMI (web UI, front panel, etc.) is otherwise specified anywhere (§4.6). **Second-highest-priority gap.**
4. **ADF4368 and ADAR2001 register values (Appendices A & B) are TBD** — blocks SPI bring-up and board-3/4 register sets aren't addressed even once supplied (§5.5, §11).
5. **"ADL5906" vs "ADL5906A"** — confirm single physical part with dual-purpose (power + temp) outputs, not two chips (§2.5, §5.10).
6. **MCP9700A 0°C offset stated as 400mV** — reconcile against actual datasheet value (commonly ~500mV) (§2.5, §5.10).
7. **ADL5904 2-point calibration arithmetic doesn't reconcile** (-18dBm/+10dBm input through 12dB attenuator should yield -30/-2dBm at detector, not the stated -22/+6dBm) (§5.9).
8. **"ADAR2001" called a "multiplier"** but its actual datasheet role (per general industry knowledge) is a true-time-delay/beamformer IC — clarify actual RF function in this design (§2.5).
9. **LT80603 datasheet link missing** from References (only part referenced without a URL) (§2.4).
10. **MAX5969A datasheet not independently re-verified** this pass (network-blocked) — re-verify PoE class/power specs (§2.3).
11. **No DHCP/IPv6/static-IP policy stated** for "Set IP address" (§4.3.5).
12. **Out-of-range / non-standard-increment attenuator command behavior undefined** (reject? clamp? error code?) (§4.3.6, §5.6).
13. **Factory Reset scope undefined** — what state is wiped vs. preserved (§4.5).
14. **"Leading zero padded" alphanumeric set-at-test fields** — ambiguous requirement, needs field-width/format clarification (§4.9).
15. **RF-board presence/count detection mechanism undefined** — status-pin pull-ups make "absent" indistinguishable from "healthy," yet board count is variable 1–4 (§5.2).
16. **No hysteresis/clear-threshold defined** for 105°C warning / 125°C alarm (§5.12).
17. **No PHY link-state/Ethernet-fault reporting requirement**, despite PHY being explicitly "high immunity" (§2.2).
18. **No account security policy** (lockout, password complexity, session timeout) for RBAC/authentication (§4.1).
19. **Exact SNMPv3 auth protocol OID** ("SHA-512" vs. RFC 7630's `usmHMAC384SHA512AuthProtocol`) needs pinning down (§3).
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

1. Convert and insert the two `.emf` block diagrams as the first substantive technical artifact in the next PTS pass — they likely resolve several gaps above (board topology, power-rail map, RF-chain signal flow) without needing to ask Union at all.
2. Re-attempt direct datasheet fetches for the Analog Devices parts (ADF4368, ADAR2001, HMC425A, HMC472A, ADL5906, ADL5904, MAX5969A) and the LT80603 family from a network path that isn't blocked by analog.com's bot protection, to pull exact register maps, pinouts, and electrical tolerances into the next PTS revision.
3. Send the consolidated Gap/Open-Question list (§12) to Union/AmpliTech as a formal RFI (Request for Information) ahead of PDR, since several items (Appendices A/B, the mute function, the HMI reference) are true blockers to producing a complete PRD.
4. Use this PTS as direct input to draft the PRD's requirement-numbering scheme, ideally preserving traceability back to the SOW's own section numbers (1.1, 1.3.1.6, 2.8, etc.) for the milestone-acceptance RTM described in §9.

---

*End of pts-run-01.md — first-pass extraction. Awaiting review and markup for run 02.*
