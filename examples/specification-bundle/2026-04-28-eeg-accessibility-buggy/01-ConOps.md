# Concept of Operations

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
