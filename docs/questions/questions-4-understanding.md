# Management and Control (M&C) channel.

Two ways to control and manage the LNBs: RESTful API (Open API) or SNMP (v3)

## Questions:

- [ ] Q: The choice will be made by Union engineers?
- [ ] Q: How many and what type of pins are needed for one RF module?
- [ ] Q: 2.5.1.5 The ADF4368 synthesizer needs to not be adjustable using SNMP or RESTful API? The settings are locked in?
- [ ] Q: 1.3.2 4 samples/sec. Do we apply any sort of averaging?
- [ ] Note: We need a factory test mode for calibration and loading settings.
- [ ] Note: Automated testing will involve the environment chamber.
- [ ] Note: Firmware updates use MCUboot. Each image carries a SHA-256 hash and a digital signature, satisfying 1.6.4. AmpliTech generates and holds the production signing key; CD uses a development key during the project. The release bootloader is built with AmpliTech’s production public key, and the signing procedure is documented in SDRL 09.