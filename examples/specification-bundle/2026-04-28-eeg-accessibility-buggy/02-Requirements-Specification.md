# Requirements Specification

**Project:** se-eeg-accessibility-buggy
**Generated:** 2026-04-28

---

# Stakeholder Requirements

## [STK] Stakeholder Requirements

:::requirement{#STK-REQ-001 title="STK-REQ-001"}
The EEG Accessibility Buggy SHALL enable the user to navigate independently between locations within a care facility and along outdoor paved paths using only EEG brain signals, without requiring continuous attendant assistance.
**Verification:** Demonstration
:::

:::requirement{#STK-REQ-002 title="STK-REQ-002"}
The EEG Accessibility Buggy SHALL protect the user from injury during all operating modes, including autonomous collision avoidance, emergency stopping within 200ms of safety trigger detection, and automatic seizure response.
**Verification:** Test
:::

:::requirement{#STK-REQ-003 title="STK-REQ-003"}
The EEG Accessibility Buggy SHALL detect declining cognitive performance and adapt its operating mode to prevent dangerous loss of BCI control accuracy, alerting the user and carer before accuracy drops below safe thresholds.
**Verification:** Test
:::

:::requirement{#STK-REQ-004 title="STK-REQ-004"}
The EEG Accessibility Buggy SHALL provide a care attendant manual override operable by untrained personnel within 60 seconds of a ≤5-minute orientation, transferring full steering and throttle authority within 100ms while maintaining obstacle detection.
**Verification:** Demonstration
:::

:::requirement{#STK-REQ-005 title="STK-REQ-005"}
The EEG Accessibility Buggy SHALL generate a ≥75 dBA audible alert and a colour-coded LED distinguishable at ≥5 m (amber: Degraded mode, red: Emergency Stop) within 1 second of any mode-transition event.
**Verification:** Test
:::

:::requirement{#STK-REQ-006 title="STK-REQ-006"}
The EEG Accessibility Buggy SHALL provide a wired diagnostic interface enabling the maintenance technician to execute a complete automated safety-critical subsystem test suite within 20 minutes per buggy.
**Verification:** Demonstration
:::

:::requirement{#STK-REQ-007 title="STK-REQ-007"}
The EEG Accessibility Buggy SHALL support over-the-air and wired firmware updates with automatic rollback to the last-verified firmware version if the update fails post-flash verification, installable by a trained facility technician.
**Verification:** Test
:::

:::requirement{#STK-REQ-008 title="STK-REQ-008"}
The EEG Accessibility Buggy SHALL allow the clinical prescriber to configure patient-specific BCI parameters including speed limits, available command palette, maximum session duration, and classification sensitivity thresholds via a secure authenticated interface.
**Verification:** Demonstration
:::

:::requirement{#STK-REQ-009 title="STK-REQ-009"}
The EEG Accessibility Buggy SHALL record session data (duration, BCI accuracy, fatigue events, mode transitions, distance) and export it as JSON via the maintenance interface within 60 seconds of session end.
**Verification:** Test
:::

:::requirement{#STK-REQ-010 title="STK-REQ-010"}
The EEG Accessibility Buggy SHALL provide a complete design history file, risk management file, and clinical evaluation report compliant with IEC 60601-1 (Medical electrical equipment — General requirements for basic safety and essential performance), IEC 62304 (Medical device software — Software life cycle processes), ISO 14971 (Application of risk management to medical devices), and EN 12184 (Electrically powered wheelchairs, scooters and their chargers — Requirements and test methods).
**Verification:** Inspection
:::

:::requirement{#STK-REQ-011 title="STK-REQ-011"}
The EEG Accessibility Buggy SHALL support post-market surveillance data collection including automated incident logging, usage statistics aggregation, adverse event reporting, and software version tracking across the deployed fleet.
**Verification:** Inspection
:::

:::requirement{#STK-REQ-012 title="STK-REQ-012"}
The EEG Accessibility Buggy SHALL integrate with the facility's Computerised Maintenance Management System (CMMS) via REST API for automated maintenance scheduling, fault reporting, and fleet utilisation tracking.
**Verification:** Test
:::

:::requirement{#STK-REQ-013 title="STK-REQ-013"}
The EEG Accessibility Buggy SHALL operate within existing facility infrastructure without requiring structural modifications beyond standard accessibility provisions compliant with the Disability Discrimination Act and Building Regulations.
**Verification:** Analysis
:::

:::requirement{#STK-REQ-014 title="STK-REQ-014"}
The EEG Accessibility Buggy SHALL maintain safe stopping distances and exhibit predictable, smooth movement patterns to avoid startling or injuring bystanders sharing indoor corridors and outdoor pathways.
**Verification:** Test
:::

:::requirement{#STK-REQ-015 title="STK-REQ-015"}
The EEG Accessibility Buggy SHALL provide audible low-speed travel tones and visible direction indicators to alert nearby pedestrians of the vehicle's presence and intended direction of travel in shared indoor and outdoor spaces.
**Verification:** Test
:::

:::requirement{#STK-REQ-016 title="STK-REQ-016"}
The EEG Accessibility Buggy SHALL maintain safe operation in electromagnetically noisy environments including hospital corridors with MRI fringe fields (5 gauss line), electrosurgical units, diathermy equipment, and 50Hz fluorescent lighting ballast harmonics.
**Verification:** Test
:::

:::requirement{#STK-REQ-017 title="STK-REQ-017"}
The EEG Accessibility Buggy SHALL operate safely on outdoor paved paths in ambient temperatures from -5°C to 40°C, with rain exposure protection to IP44 minimum for all outdoor-rated components, and on gradients up to 8 degrees.
**Verification:** Test
:::

:::requirement{#STK-REQ-018 title="STK-REQ-018"}
The EEG Accessibility Buggy SHALL handle, store, and transmit EEG biometric data in compliance with GDPR and applicable data protection regulations, including encryption at rest and in transit, user consent management, and data retention policies.
**Verification:** Inspection
:::

---

# System Requirements

## [SYS] System Requirements

:::requirement{#SYS-REQ-001 title="SYS-REQ-001"}
The EEG Accessibility Buggy SHALL classify user motor imagery and SSVEP EEG signals with a minimum accuracy of 85% across a 4-class command set (forward, left, right, stop) during Normal Navigation mode, measured over a rolling 2-minute window.
**Verification:** Test
:::

:::requirement{#SYS-REQ-002 title="SYS-REQ-002"}
When any safety trigger is detected (complete BCI signal loss, imminent collision, system fault, or manual emergency stop button press), the EEG Accessibility Buggy SHALL de-energise drive motors and engage mechanical brakes within 200ms of trigger detection.
**Verification:** Test
:::

:::requirement{#SYS-REQ-003 title="SYS-REQ-003"}
The EEG Accessibility Buggy SHALL limit maximum travel speed to 6 km/h in Normal Navigation mode and 3 km/h in Degraded/Assisted mode.
**Verification:** Test
:::

:::requirement{#SYS-REQ-004 title="SYS-REQ-004"}
The EEG Accessibility Buggy SHALL provide a minimum of 4 hours continuous operation and 15 km range on a single battery charge at an average speed of 4 km/h with a 120 kg user payload.
**Verification:** Test
:::

:::requirement{#SYS-REQ-005 title="SYS-REQ-005"}
When generalised spike-and-wave EEG activity exceeding 3 Hz is detected across all acquisition channels, the EEG Accessibility Buggy SHALL trigger Emergency Stop within 150ms and transmit a BLE emergency alert containing device ID, alert type, and GPS coordinates to the facility emergency system.
**Verification:** Test
:::

:::requirement{#SYS-REQ-006 title="SYS-REQ-006"}
The EEG Accessibility Buggy SHALL detect obstacles and persons within a 2m forward detection zone and 1m lateral detection zone and prevent collision by reducing speed or stopping, with zero false-negative detections for obstacles larger than 50mm height above floor level.
**Verification:** Test
:::

:::requirement{#SYS-REQ-007 title="SYS-REQ-007"}
When BCI signal-to-noise ratio drops below the classification threshold for more than 5 seconds, the EEG Accessibility Buggy SHALL transition to Degraded/Assisted mode with speed limited to 3 km/h and BCI simplified to binary go/stop commands.
**Verification:** Test
:::

:::requirement{#SYS-REQ-008 title="SYS-REQ-008"}
When rolling 2-minute BCI classification accuracy drops below 70%, the EEG Accessibility Buggy SHALL transition to Degraded/Assisted mode and generate an audible alert to the care attendant.
**Verification:** Test
:::

:::requirement{#SYS-REQ-009 title="SYS-REQ-009"}
When the care attendant activates the rear-mounted override switch, the EEG Accessibility Buggy SHALL transfer steering and throttle authority to the joystick within 100ms, ignore BCI movement commands, and maintain obstacle detection in active mode.
**Verification:** Test
:::

:::requirement{#SYS-REQ-010 title="SYS-REQ-010"}
The EEG Accessibility Buggy SHALL have a maximum vehicle footprint of 700mm wide by 1200mm long, an unladen mass not exceeding 80 kg, a user payload capacity of at least 120 kg, and a turning radius not exceeding 1500mm.
**Verification:** Inspection
:::

:::requirement{#SYS-REQ-011 title="SYS-REQ-011"}
When the onboard inclinometer detects vehicle tilt exceeding 15 degrees in any axis, the EEG Accessibility Buggy SHALL apply brakes and prevent further motion in the direction of the hazardous slope.
**Verification:** Test
:::

:::requirement{#SYS-REQ-012 title="SYS-REQ-012"}
When battery cell temperature exceeds 60°C or cell voltage imbalance exceeds 100mV, the EEG Accessibility Buggy SHALL open the battery disconnect relay within 500ms, activate the fire suppression blanket, sound an 85 dB alarm, and transmit a facility emergency alert.
**Verification:** Test
:::

:::requirement{#SYS-REQ-013 title="SYS-REQ-013"}
The EEG Accessibility Buggy SHALL comply with IEC 60601-1-2 (Electromagnetic compatibility — Requirements and tests for medical electrical equipment) for both radiated emissions and immunity, maintaining safe operation in environments containing MRI fringe fields, electrosurgical units, and 50 Hz fluorescent lighting ballast harmonics.
**Verification:** Test
:::

:::requirement{#SYS-REQ-014 title="SYS-REQ-014"}
The EEG Accessibility Buggy SHALL charge from 20% to 100% state of charge within 4 hours when connected to a 230V 50Hz or 120V 60Hz mains supply via the facility charging dock.
**Verification:** Test
:::

:::requirement{#SYS-REQ-015 title="SYS-REQ-015"}
The EEG Accessibility Buggy SHALL provide an automated diagnostic test suite accessible via USB-C service port that verifies motor response, brake torque, sensor calibration, battery cell health, and BCI pipeline integrity, completable within 20 minutes.
**Verification:** Demonstration
:::

:::requirement{#SYS-REQ-016 title="SYS-REQ-016"}
The EEG Accessibility Buggy SHALL support dual-bank flash firmware updates with automatic rollback to the previous verified firmware image if the new image fails post-update integrity verification.
**Verification:** Test
:::

:::requirement{#SYS-REQ-017 title="SYS-REQ-017"}
When Emergency Stop or seizure detection triggers, the EEG Accessibility Buggy SHALL transmit a BLE 5.0 iBeacon-compatible alert containing device ID, alert type, and location to the facility BLE gateway within 500ms, and simultaneously notify the user's paired smartphone.
**Verification:** Test
:::

:::requirement{#SYS-REQ-018 title="SYS-REQ-018"}
The EEG Accessibility Buggy SHALL complete power-on self-diagnostics and user-specific EEG calibration within a combined time of 5 minutes, achieving a classification accuracy threshold of at least 80% before entering Normal Navigation mode.
**Verification:** Test
:::

:::requirement{#SYS-REQ-019 title="SYS-REQ-019"}
The EEG Accessibility Buggy SHALL encrypt all EEG biometric data using AES-256 at rest and TLS 1.3 in transit, implement role-based access control for clinical and maintenance interfaces, and maintain an audit log of all data access events.
**Verification:** Test
:::

:::requirement{#SYS-REQ-020 title="SYS-REQ-020"}
The EEG Accessibility Buggy SHALL use EEG electrode contacts and headset materials certified as biocompatible per ISO 10993-1 (Biological evaluation of medical devices — Part 1: Evaluation and testing within a risk management process) for prolonged skin contact (greater than 24 hours cumulative daily use), with no materials classified as cytotoxic, sensitising, or irritant in direct contact with the scalp.
**Verification:** Inspection
:::

:::requirement{#SYS-REQ-021 title="SYS-REQ-021"}
The EEG Accessibility Buggy SHALL be physically housed in a single integrated chassis enclosure providing IP54 environmental protection per IEC 60529 (protection against dust ingress and water splashing from any direction), with all electronics, battery, and BCI processing mounted within a sealed Electronics Bay.
**Verification:** Inspection
:::

:::requirement{#SYS-REQ-022 title="SYS-REQ-022"}
While operating in Degraded/Assisted mode due to BCI accuracy below 70%, the EEG Accessibility Buggy SHALL limit maximum speed to 3 km/h (50% of Normal Navigation maximum), maintain full obstacle detection and emergency stop capability, and display a continuous amber alert on the Status LED Array and a text notification on the Display Unit.
**Verification:** Test
:::

:::requirement{#SYS-REQ-023 title="SYS-REQ-023"}
When the Main Application Processor fails (watchdog timeout exceeding 50ms, voltage rail failure, or CRC fault detected), the Safety Monitor Processor SHALL assert SAFE_STOP, de-energise drive motors, and engage mechanical brakes within 200ms of fault detection, independent of Main Application Processor firmware state.
**Verification:** Test
:::

---
