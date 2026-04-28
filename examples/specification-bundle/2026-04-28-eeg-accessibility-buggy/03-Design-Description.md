# Design Description

**Project:** se-eeg-accessibility-buggy
**Generated:** 2026-04-28

---

# Subsystem Requirements

## [SUB] Subsystem Requirements

## BCI Processing Subsystem

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-001 title="SUB-BCIPROCESSINGSUBSYSTEM-001"}
The Artifact Rejection Engine SHALL reject electromagnetic and muscle artefact from 32-channel EEG within 20ms of epoch receipt, with false rejection of true neural signals not exceeding 10%.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-002 title="SUB-BCIPROCESSINGSUBSYSTEM-002"}
The BCI Classifier SHALL achieve a minimum four-class motor imagery and SSVEP classification accuracy of 75% across a 2-minute rolling window during normal operation.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-003 title="SUB-BCIPROCESSINGSUBSYSTEM-003"}
The Command Arbitration Module SHALL emit a validated drive command to the Drive Subsystem interface within 150ms of the corresponding EEG signal epoch being acquired by the EEG Acquisition Module.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-004 title="SUB-BCIPROCESSINGSUBSYSTEM-004"}
When BCI signal-to-noise ratio remains below the classification threshold for more than 3 consecutive seconds, the Command Arbitration Module SHALL emit a STOP command to the Drive Subsystem and suppress further drive commands until SNR recovers.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-005 title="SUB-BCIPROCESSINGSUBSYSTEM-005"}
The Feature Extraction Processor SHALL load and apply per-user CSP spatial filter matrices from encrypted calibration storage within 5 seconds of user session initialisation.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-006 title="SUB-BCIPROCESSINGSUBSYSTEM-006"}
The Artifact Rejection Engine SHALL expose a watchdog interface to the BCI Processing Subsystem supervisor; when no valid epoch output is received for more than 500ms, the supervisor SHALL reset the Artifact Rejection Engine and log the event for post-session analysis.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-007 title="SUB-BCIPROCESSINGSUBSYSTEM-007"}
The Main Application Processor SHALL expose a USB-C service port accessible on the Electronics Bay panel that provides a diagnostic test suite interface, executing the full motor response, brake torque, sensor calibration, battery cell health, and BCI pipeline integrity test sequence within 20 minutes via authenticated USB connection.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-008 title="SUB-BCIPROCESSINGSUBSYSTEM-008"}
The Main Application Processor SHALL encrypt all EEG biometric data using AES-256 at rest and TLS 1.3 in transit, enforce role-based access control with at least three roles (clinical, maintenance, user), and maintain an append-only audit log of all data access events with timestamp, user identity, and operation type.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-009 title="SUB-BCIPROCESSINGSUBSYSTEM-009"}
The Artifact Rejection Engine SHALL operate from the 3.3V logic rail supplied by the DC-DC Converter Array, with a maximum peak current draw of 250mA during active signal processing, and SHALL maintain operation without error for supply voltage variation between 3.0V and 3.6V.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-010 title="SUB-BCIPROCESSINGSUBSYSTEM-010"}
While in Normal Navigation mode, the BCI Processing Subsystem SHALL provide a software watchdog that monitors BCI command output validity and SHALL halt command generation and assert a safe-stop signal within 100ms if command outputs deviate from expected bounds or if the watchdog is not kicked within a 200ms window.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-011 title="SUB-BCIPROCESSINGSUBSYSTEM-011"}
The EEG Accessibility Buggy electrode interface components that contact the user scalp SHALL be manufactured from materials meeting ISO 10993-1 (Biological evaluation of medical devices — Part 1: Evaluation and testing within a risk management process) biocompatibility requirements for skin-contacting medical devices, and SHALL support decontamination with standard clinical disinfectants between users without degradation of electrical performance.
**Verification:** Analysis
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-012 title="SUB-BCIPROCESSINGSUBSYSTEM-012"}
The Artifact Rejection Engine SHALL execute as a software module on the Feature Extraction Processor hardware, occupying no more than 30% of available CPU cycles during continuous EEG processing at 250 SPS per channel, with a deterministic maximum latency of 4ms per processing frame.
**Verification:** Test
:::

:::requirement{#SUB-BCIPROCESSINGSUBSYSTEM-013 title="SUB-BCIPROCESSINGSUBSYSTEM-013"}
The Main Application Processor SHALL validate all BCI-derived navigation commands using a cryptographic message authentication code (HMAC-SHA256) when received from any external software interface, and SHALL reject and log any command that fails authentication, missing a valid session token, or arrives via an interface not in the authorised interface table defined during commissioning.
**Verification:** Test
:::

## Communication Subsystem

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-001 title="SUB-COMMUNICATIONSUBSYSTEM-001"}
The Bluetooth LE Module SHALL maintain a stable BLE 5.2 connection to the paired EEG headset at 2.4GHz with a connection interval of 7.5ms, sustaining 32-channel EEG data at 8kHz sample rate (effective throughput 512kbps) within an operating range of 3m with no physical obstructions.
**Verification:** Test
:::

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-002 title="SUB-COMMUNICATIONSUBSYSTEM-002"}
The Communication Controller SHALL enforce a firewall rule that prevents all data traffic originating from the Cellular Modem from reaching the internal CAN bus or I2C/SPI control networks.
**Verification:** Inspection
:::

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-003 title="SUB-COMMUNICATIONSUBSYSTEM-003"}
When Emergency Stop or seizure detection triggers, the Communication Controller SHALL transmit a BLE 5.0 iBeacon-compatible alert to the facility BLE gateway within 500ms, including device ID, alert type, and GPS coordinates, and simultaneously notify the paired caregiver smartphone.
**Verification:** Test
:::

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-004 title="SUB-COMMUNICATIONSUBSYSTEM-004"}
The Cellular Modem SHALL authenticate to the remote telemetry server using mutual TLS 1.3 with a device-unique X.509 certificate provisioned at manufacture, rejecting any server certificate not signed by the system's root CA.
**Verification:** Test
:::

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-005 title="SUB-COMMUNICATIONSUBSYSTEM-005"}
The Cellular Modem SHALL transmit session telemetry (location, BCI state, battery, error events) to the remote server at a minimum interval of 10 seconds with a maximum uplink latency of 2 seconds under nominal 4G coverage.
**Verification:** Test
:::

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-006 title="SUB-COMMUNICATIONSUBSYSTEM-006"}
The Communication Subsystem SHALL provide a USB-C service port that exposes a diagnostic API enabling technicians to execute motor response verification tests and receive pass/fail results with logged timestamps within 10 s of command initiation.
**Verification:** Test
:::

:::requirement{#SUB-COMMUNICATIONSUBSYSTEM-007 title="SUB-COMMUNICATIONSUBSYSTEM-007"}
When a Seizure Emergency Stop event is triggered, the Communication Controller SHALL transmit a BLE 5.0 emergency alert packet containing device ID, alert type code, GPS coordinates (WGS-84 decimal), and UTC timestamp to the facility emergency system receiver within 500ms of the Emergency Stop command.
**Verification:** Test
:::

## Drive Subsystem

:::requirement{#SUB-DRIVESUBSYSTEM-001 title="SUB-DRIVESUBSYSTEM-001"}
The Motor Controller Unit SHALL implement closed-loop velocity control for each drive motor channel, updating the velocity setpoint at 100 Hz using 1024 PPR quadrature encoder feedback, with a steady-state velocity error not exceeding ±0.1 km/h at any commanded speed.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-002 title="SUB-DRIVESUBSYSTEM-002"}
The Motor Controller Unit SHALL enforce a vehicle speed limit of 6 km/h in Normal Navigation mode and 2 km/h in Restricted mode, clamping any velocity command exceeding these thresholds before the command is applied to the motor drive stages.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-003 title="SUB-DRIVESUBSYSTEM-003"}
When the Motor Controller Unit receives a kill signal via the CAN safety frame or detects loss of CAN heartbeat for more than 100ms, the Motor Controller Unit SHALL apply regenerative braking to bring both motors to zero velocity within 250ms and hold zero-velocity command until a valid restart sequence is received.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-004 title="SUB-DRIVESUBSYSTEM-004"}
The Drive Power Stage SHALL trip the hardware overcurrent protection circuit and isolate the motor phase outputs within 5ms when per-channel motor current exceeds 30A, independent of Motor Controller Unit firmware.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-005 title="SUB-DRIVESUBSYSTEM-005"}
The Left Drive Motor Assembly and the Right Drive Motor Assembly SHALL each provide a minimum continuous shaft output of 250W at 48V DC supply voltage across the operating temperature range of 0°C to 40°C, with the driven wheel maintaining traction on level indoor surfaces up to a 5% gradient.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-006 title="SUB-DRIVESUBSYSTEM-006"}
When the Drive Subsystem detects Motor Controller Unit CAN heartbeat absence exceeding 100 ms, the Drive Subsystem SHALL command both motor drivers to coast-to-stop state and assert MCU_FAULT to the Safety Monitor Processor within 50 ms of timeout detection.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-007 title="SUB-DRIVESUBSYSTEM-007"}
The Motor Controller Unit SHALL implement hardware overcurrent protection independent of the Main Application Processor that disconnects motor phase outputs within 5ms if phase current exceeds 120% of rated maximum, with no firmware execution required for this protection to operate.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-008 title="SUB-DRIVESUBSYSTEM-008"}
While in Degraded/Assisted mode, the Drive Subsystem SHALL enforce a maximum speed cap of 3 km/h by limiting PWM duty cycle to 50% of the Normal Navigation maximum, with the speed cap applied in firmware prior to the Motor Controller Unit command, independent of any BCI or joystick input value.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-009 title="SUB-DRIVESUBSYSTEM-009"}
While operating in Care Attendant Override mode, the Drive Subsystem SHALL respond to joystick steering and throttle inputs within 100ms, maintain obstacle detection response within 200ms, limit maximum speed to 6 km/h, and sustain override operation for the full battery duration specified by SUB-REQ-014 without consuming more than 15% additional power versus Normal Navigation mode at equivalent speed.
**Verification:** Test
:::

:::requirement{#SUB-DRIVESUBSYSTEM-010 title="SUB-DRIVESUBSYSTEM-010"}
The Drive Subsystem, including the Motor Controller Unit and Motor Power Isolation Relay, SHALL comply with IEC 60601-1 (Medical electrical equipment — General requirements for basic safety and essential performance) for protection against electrical hazards, and all motor drive electronics SHALL meet the EMC requirements of IEC 60601-1-2 (Electromagnetic compatibility — Requirements and tests for medical electrical equipment).
**Verification:** Inspection
:::

## HMI Subsystem

:::requirement{#SUB-HMISUBSYSTEM-001 title="SUB-HMISUBSYSTEM-001"}
The Audio Alert Module SHALL produce an audible tone of minimum 80dB SPL at 1m when an emergency stop is activated, within 100ms of the E-stop signal being asserted, and SHALL sustain this tone until the system transitions out of the E-stop state.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-002 title="SUB-HMISUBSYSTEM-002"}
The Status LED Array SHALL display a distinct colour state for each of: Normal Navigation (green), Degraded/Reduced Mode (amber), E-stop Active (red), Charging (blue), and BCI Calibration (pulsing white), with state transitions completing within 200ms of the triggering event.
**Verification:** Demonstration
:::

:::requirement{#SUB-HMISUBSYSTEM-003 title="SUB-HMISUBSYSTEM-003"}
When the care attendant activates the override switch, the HMI Subsystem SHALL transfer joystick command authority to the Drive Subsystem within 100ms, suppressing all BCI command outputs for the duration of override mode.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-004 title="SUB-HMISUBSYSTEM-004"}
The Display Unit SHALL render BCI classification accuracy, vehicle speed, battery state of charge, and active operating mode on a sunlight-readable screen with minimum 300 cd/m2 brightness, updating at minimum 2Hz in Normal Navigation mode.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-005 title="SUB-HMISUBSYSTEM-005"}
The Status LED Array SHALL be visible from any angle within a 120-degree horizontal arc to the rear of the vehicle, with a minimum luminous intensity of 10 mcd per LED element, to allow care attendants approaching from behind to read vehicle state.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-006 title="SUB-HMISUBSYSTEM-006"}
When the care attendant activates the rear-mounted override switch, the HMI Subsystem SHALL assert a hardwired CARER_OVERRIDE signal to the Safety Monitor Processor within 50 ms and maintain the signal for the duration of physical switch engagement.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-007 title="SUB-HMISUBSYSTEM-007"}
When CARER_OVERRIDE is asserted, the HMI Subsystem SHALL route joystick control commands directly to the Drive Subsystem motor controller at a 50 Hz command rate with latency not exceeding 20 ms from joystick input to CAN frame transmission.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-008 title="SUB-HMISUBSYSTEM-008"}
When the care attendant activates the rear-mounted override switch, the HMI Subsystem SHALL complete authority transfer from BCI commands to joystick steering and throttle within 100ms of switch actuation, measured from the falling edge of the CARER_OVERRIDE signal to the first joystick command accepted by the Drive Subsystem.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-009 title="SUB-HMISUBSYSTEM-009"}
When the rolling 2-minute BCI classification accuracy drops below 70%, the Audio Alert Module SHALL emit a 85 dBSPL (at 1m) pulsed tone at 880Hz within 200ms of the accuracy threshold crossing, sustained until the mode transitions or care attendant acknowledges via the HMI.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-010 title="SUB-HMISUBSYSTEM-010"}
When the rear-mounted override switch is activated, the HMI Subsystem SHALL transfer steering and throttle authority to the rear joystick, disable BCI movement command processing, and confirm handover to the Drive Subsystem within 100ms of the override switch activation signal.
**Verification:** Test
:::

:::requirement{#SUB-HMISUBSYSTEM-011 title="SUB-HMISUBSYSTEM-011"}
While the system is in Degraded/Assisted mode, the Status LED Array SHALL display a continuous amber pattern at 2Hz pulse rate and the Display Unit SHALL present a persistent text notification reading 'DEGRADED MODE — BCI ACCURACY LOW — SPEED LIMITED TO 3 KM/H' on the primary status screen within 100ms of mode entry, remaining visible until mode change.
**Verification:** Demonstration
:::

## Perception Subsystem

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-001 title="SUB-PERCEPTIONSUBSYSTEM-001"}
The Forward Depth Sensor Array SHALL measure distances in the forward 120-degree arc with a range of 0.05m to 2.0m and an accuracy of plus or minus 50mm at no less than 10Hz, under all operating lighting conditions from 0 lux to 10,000 lux.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-002 title="SUB-PERCEPTIONSUBSYSTEM-002"}
The Perception MCU SHALL process all sensor inputs and emit an obstacle alert frame to the Safety Monitor Processor within 50ms of sensor data arrival, sustaining this throughput at full 10Hz sensor update rate.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-003 title="SUB-PERCEPTIONSUBSYSTEM-003"}
The Side Proximity Sensor Pair SHALL detect objects within 0.5m laterally on each side of the vehicle and deliver a TTL-level alert signal to the Perception MCU within 30ms of intrusion.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-004 title="SUB-PERCEPTIONSUBSYSTEM-004"}
When the Perception MCU fails to produce an obstacle alert frame within 150ms of its scheduled transmission window, the Safety Monitor Processor SHALL treat the timeout as an obstacle-present condition and initiate the emergency stop sequence.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-005 title="SUB-PERCEPTIONSUBSYSTEM-005"}
While vehicle tilt measured by the Inclinometer Tilt Sensor Unit exceeds 15 degrees on any axis, the Perception Subsystem SHALL assert a tilt-hazard signal to the Safety Monitor Processor within 50ms of threshold crossing.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-006 title="SUB-PERCEPTIONSUBSYSTEM-006"}
The Inclinometer Tilt Sensor Unit SHALL operate from a regulated 3.3V supply derived from the DC-DC Converter Array, with a maximum continuous current draw of 10mA, and SHALL maintain measurement accuracy within specification for supply voltage variation of 3.0V to 3.6V.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-007 title="SUB-PERCEPTIONSUBSYSTEM-007"}
While vehicle tilt measured by the onboard inclinometer exceeds 15 degrees in any axis, the Perception Subsystem SHALL continuously publish tilt angle, axis identity, and confidence score to the Safety Monitor Processor at no less than 20 Hz.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-008 title="SUB-PERCEPTIONSUBSYSTEM-008"}
When one or more elements of the Forward Depth Sensor Array fail to deliver a valid distance frame within 200ms, the Perception MCU SHALL reduce commanded maximum vehicle speed to 1.5 km/h, assert a sensor-fault alert to the Safety Monitor Processor, and maintain this reduced-speed safe state until all sensor elements are confirmed operational.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-009 title="SUB-PERCEPTIONSUBSYSTEM-009"}
When the Perception MCU fails to emit an obstacle-alert frame to the Safety Monitor Processor for more than 150ms, the Safety Monitor Processor SHALL assert a perception-fault condition: engage emergency braking, disable all drive motor commands, and inhibit restart until the Perception MCU heartbeat is confirmed operational.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-010 title="SUB-PERCEPTIONSUBSYSTEM-010"}
When either Side Proximity Sensor element fails to respond to its health-check poll within 200ms, the Perception MCU SHALL assert a side-sensor-fault flag to the Safety Monitor Processor and restrict maximum vehicle speed to 0.5 km/h until the fault is cleared by an authorised operator or maintenance reset.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-011 title="SUB-PERCEPTIONSUBSYSTEM-011"}
When the Inclinometer Tilt Sensor Unit fails to deliver a valid tilt measurement to the Perception MCU within 200ms (sensor timeout or invalid checksum on three consecutive frames), the Safety Monitor Processor SHALL treat the failure as a tilt-threshold-exceeded condition and command emergency stop, maintaining halt until the inclinometer is confirmed operational.
**Verification:** Test
:::

:::requirement{#SUB-PERCEPTIONSUBSYSTEM-012 title="SUB-PERCEPTIONSUBSYSTEM-012"}
When the Perception Subsystem fails to publish valid tilt data to the Safety Monitor Processor for more than 100ms during active navigation, the Safety Monitor Processor SHALL treat the publication silence as a tilt-threshold-exceeded condition and command emergency stop, maintaining halt until tilt data publication is confirmed restored.
**Verification:** Test
:::

## Power Subsystem

:::requirement{#SUB-POWERSUBSYSTEM-001 title="SUB-POWERSUBSYSTEM-001"}
The Battery Management System SHALL disconnect the battery pack from the 48V bus within 200ms when any cell temperature exceeds 60°C or any cell voltage exceeds 3.65V.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-002 title="SUB-POWERSUBSYSTEM-002"}
The Lithium Iron Phosphate Battery Pack SHALL deliver a minimum of 4 hours continuous operation at rated system load (drive motors, BCI processing, HMI, safety systems combined) before state of charge falls below 10%.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-003 title="SUB-POWERSUBSYSTEM-003"}
The DC-DC Converter Array SHALL maintain each output rail (12V, 5V, 3.3V) within ±5% of nominal voltage under all load conditions from 10% to 100% rated current, with output ripple not exceeding 50mV peak-to-peak.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-004 title="SUB-POWERSUBSYSTEM-004"}
The Charge Controller SHALL charge the battery pack from 20% to 100% state of charge within 3 hours when connected to a 240V AC facility supply.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-005 title="SUB-POWERSUBSYSTEM-005"}
The Charge Controller SHALL accept charging input from a facility charging dock providing 230V 50Hz or 120V 60Hz AC mains, and SHALL complete pack charge from 20% to 100% state of charge within 4 hours at each nominal supply voltage.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-006 title="SUB-POWERSUBSYSTEM-006"}
When connected to a facility charging dock providing 230V 50Hz AC supply, the Power Subsystem onboard charger SHALL limit inrush current to less than 16A peak, regulate charging current to maintain cell temperature below 45°C, and complete charge from 20% to 100% SoC within 4 hours.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-007 title="SUB-POWERSUBSYSTEM-007"}
The Charge Controller SHALL accept 230V 50Hz or 120V 60Hz mains input via the facility charging dock connector and charge the Lithium Iron Phosphate Battery Pack from 20% to 100% State of Charge within 4 hours using CC-CV charging profile, with automatic termination and trickle maintenance.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-008 title="SUB-POWERSUBSYSTEM-008"}
When the Battery Management System fails to disconnect the battery pack from the 48V bus within 250ms of an overvoltage or overtemperature detection, the Safety Monitor Processor SHALL detect the BMS watchdog timeout and assert system-wide emergency stop within 50ms.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-009 title="SUB-POWERSUBSYSTEM-009"}
When the Lithium Iron Phosphate Battery Pack state of charge reaches 10%, the Power Subsystem SHALL activate a safe-stop sequence: halt all drive motor commands within 5 seconds, engage electromechanical parking brakes, sustain Safety Monitor Processor and emergency braking power for a minimum of 5 minutes, and assert a low-battery HMI alert.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-010 title="SUB-POWERSUBSYSTEM-010"}
When any DC-DC Converter Array output rail deviates more than 10% from nominal voltage for more than 100ms, the Safety Monitor Processor SHALL assert a power-fault condition, command emergency braking, disable all drive motor control signals, and maintain power-fault safe state until the rail recovers to within 5% of nominal and an authorised operator clears the fault.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-011 title="SUB-POWERSUBSYSTEM-011"}
When the Charge Controller detects a mains supply fault (supply voltage outside 207V-253V AC, frequency outside 47Hz-53Hz, or ground fault current exceeding 10mA RMS), it SHALL disconnect from the facility mains supply within 200ms, lock the charge port relay, and assert a charge-fault alert on the HMI display.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-012 title="SUB-POWERSUBSYSTEM-012"}
When the Charge Controller detects mains supply voltage outside 207V-253V AC at 50Hz or 102V-132V AC at 60Hz at the facility dock connector, it SHALL reject the connection, maintain galvanic isolation from the supply, and display a supply-incompatible warning on the HMI.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-013 title="SUB-POWERSUBSYSTEM-013"}
When the 3.3V supply to the Inclinometer Tilt Sensor Unit deviates outside the 3.0V-3.6V operating range for more than 50ms, the DC-DC Converter Array health monitor SHALL assert a sensor-power fault to the Safety Monitor Processor, causing immediate vehicle halt and inhibiting motion until the 3.3V rail returns to within specification.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-014 title="SUB-POWERSUBSYSTEM-014"}
When inrush current to the Power Subsystem charger exceeds 16A peak or cell temperature during charging exceeds 45°C for more than 10 seconds, the Charge Controller SHALL interrupt the charge cycle within 100ms, isolate from the facility mains supply, and assert a charge-fault alert that persists until acknowledged by an authorised operator.
**Verification:** Test
:::

:::requirement{#SUB-POWERSUBSYSTEM-015 title="SUB-POWERSUBSYSTEM-015"}
When the Charge Controller fails to reach 100% state of charge within 5 hours of charge initiation, it SHALL terminate the charge cycle, log a battery-health fault event with timestamp to the CMMS interface, and alert the facility operator via the HMI display.
**Verification:** Test
:::

## Safety Subsystem

:::requirement{#SUB-SAFETYSUBSYSTEM-001 title="SUB-SAFETYSUBSYSTEM-001"}
The Safety Monitor Processor SHALL operate on a processor with no shared memory, clock domain, or power rail with the main application processor, and SHALL maintain its safety state machine execution at 200Hz even when the main application processor is in fault state.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-002 title="SUB-SAFETYSUBSYSTEM-002"}
The Motor Power Isolation Relay SHALL disconnect the 48V motor drive power rail within 20ms of receiving the de-energise command from the Safety Monitor Processor, under all load conditions from 0A to 200A.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-003 title="SUB-SAFETYSUBSYSTEM-003"}
The Seizure Detection Module SHALL detect generalised spike-and-wave EEG activity exceeding 3 Hz amplitude and >30µV peak-to-peak across all acquisition channels and output a seizure flag to the Safety Monitor Processor within 150ms of seizure onset, with a false positive rate not exceeding 1 per 8 hours of operation.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-004 title="SUB-SAFETYSUBSYSTEM-004"}
The Inclinometer Tilt Sensor Unit SHALL measure vehicle pitch and roll continuously at 100Hz with accuracy of ±0.5° and SHALL trigger an E-stop command via the Safety Monitor Processor when tilt in any axis exceeds 15 degrees for more than 200ms.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-005 title="SUB-SAFETYSUBSYSTEM-005"}
The Manual Emergency Stop Button SHALL be hardwired directly to the Motor Power Isolation Relay coil circuit in series, such that button actuation disconnects motor power without software mediation, within 5ms of button contact closure.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-006 title="SUB-SAFETYSUBSYSTEM-006"}
When any safety trigger is received by the Safety Monitor Processor — including BCI signal loss notification, seizure flag, tilt threshold exceeded, manual E-stop, or main processor heartbeat failure — the Safety Subsystem SHALL transition to and maintain Emergency Stop state within 200ms, including relay de-energisation, brake command, and HMI alert outputs.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-007 title="SUB-SAFETYSUBSYSTEM-007"}
The Safety Monitor Processor SHALL monitor a heartbeat signal from the main application processor, and SHALL trigger Emergency Stop if the heartbeat is absent for more than 500ms or if the heartbeat period deviates by more than 20% from the nominal 100ms period.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-008 title="SUB-SAFETYSUBSYSTEM-008"}
The Safety Subsystem SHALL be implemented as a physically separate electronics module from the Main Application Processor, housed within the Electronics Bay on an independent PCB with independent power input, to ensure that a hardware fault in the main processing chain cannot corrupt safety function execution.
**Verification:** Inspection
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-009 title="SUB-SAFETYSUBSYSTEM-009"}
When the facility emergency system transmits a halt command via the 2.4 GHz wireless interface, the Safety Subsystem SHALL apply both motor power isolation relays and engage electromagnetic braking within 150 ms of signal receipt.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-010 title="SUB-SAFETYSUBSYSTEM-010"}
When the Safety Monitor Processor detects Main Application Processor watchdog timeout exceeding 200 ms, the Safety Subsystem SHALL assert SAFE_STOP, halt all motor commands, and log a processor fault record to non-volatile storage.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-011 title="SUB-SAFETYSUBSYSTEM-011"}
The Safety Subsystem Safety Monitor Processor SHALL be developed and validated in accordance with IEC 61508-3 (Functional Safety of Electrical/Electronic/Programmable Electronic Safety-related Systems — Part 3: Software requirements) at SIL 3, with documented V-model lifecycle artefacts including hazard and risk analysis, software safety requirements specification, and independent verification.
**Verification:** Inspection
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-012 title="SUB-SAFETYSUBSYSTEM-012"}
The Manual Emergency Stop Button SHALL use a dual-channel NC (normally-closed) contact circuit wired in series with the Motor Power Isolation Relay coil, such that loss of either channel independently de-energises the relay and halts motor drive within 20ms.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-013 title="SUB-SAFETYSUBSYSTEM-013"}
When the Safety Monitor Processor detects Main Application Processor failure (watchdog timeout, CRC fault, or voltage rail collapse), the Safety Subsystem SHALL assume sole command authority, hold current velocity to zero, and maintain Emergency Stop state until manual reset, with no reliance on MAP firmware for this transition.
**Verification:** Test
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-014 title="SUB-SAFETYSUBSYSTEM-014"}
The Safety Subsystem SHALL be certified to Safety Integrity Level 3 (SIL 3) per IEC 61508 (Functional safety of electrical/electronic/programmable electronic safety-related systems), with a third-party functional safety assessment covering hardware fault tolerance, systematic capability, and software development process.
**Verification:** Inspection
:::

:::requirement{#SUB-SAFETYSUBSYSTEM-015 title="SUB-SAFETYSUBSYSTEM-015"}
The Inclinometer Tilt Sensor Unit SHALL measure vehicle pitch and roll at 20Hz, and when tilt exceeds 12 degrees in any single axis or 10 degrees combined vector, SHALL issue a pre-warning signal to the Safety Monitor Processor; when tilt exceeds 15 degrees in any axis the Safety Monitor Processor SHALL command immediate brake application and motion inhibit within 50ms.
**Verification:** Test
:::

## Vehicle Platform

:::requirement{#SUB-VEHICLEPLATFORM-001 title="SUB-VEHICLEPLATFORM-001"}
The Chassis Frame SHALL withstand a static load of 150kg distributed across the seat mounting points without permanent deformation, and a dynamic impact load of 3g vertical shock without fracture, in accordance with ISO 7176-8 (Static, impact, and fatigue strengths).
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-002 title="SUB-VEHICLEPLATFORM-002"}
The Electronics Bay SHALL maintain an internal temperature below 70°C for all electronics mounted within it while dissipating up to 40W total under an ambient temperature of 40°C, with the vehicle stationary and no forced air flow.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-003 title="SUB-VEHICLEPLATFORM-003"}
The Seat and Postural Support System SHALL accommodate occupants from 40kg to 120kg body mass and from 1400mm to 1900mm stature, providing a minimum of 15 degrees of backrest recline adjustment and a lap belt that tightens to resist forward displacement under 3g deceleration.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-004 title="SUB-VEHICLEPLATFORM-004"}
The Wheel and Caster Assembly SHALL provide a turning radius not exceeding 600mm (measured from vehicle centre to outer wheel track) to allow navigation through standard 900mm accessible doorways with a minimum 150mm clearance on each side, while maintaining traction on surfaces inclined up to 6 degrees.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-005 title="SUB-VEHICLEPLATFORM-005"}
The Electronics Bay SHALL provide IP54 environmental protection per IEC 60529 (IEC 60529: protected against dust ingress at IP5X — wire probe 1mm dia. does not penetrate; protected against water spray from any direction at IP4X), maintaining this rating through all maintenance access cycles, with a minimum 100 maintenance access cycles before seal degradation below IP54.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-006 title="SUB-VEHICLEPLATFORM-006"}
The Vehicle Platform Electronics Bay SHALL provide a USB-C 3.1 Gen 1 service port that exposes an automated diagnostic test suite capable of verifying motor response (peak torque within 5% of nominal), brake torque (hold force >= 80N), sensor calibration (all axes within 2% of reference), battery cell health (internal resistance and capacity), and BCI pipeline integrity (signal-to-noise ratio >= 20dB), with full suite completion within 20 minutes.
**Verification:** Demonstration
:::

:::requirement{#SUB-VEHICLEPLATFORM-007 title="SUB-VEHICLEPLATFORM-007"}
The Chassis Frame SHALL be constructed from aluminium alloy (6061-T6 or equivalent) with a minimum wall thickness of 3mm at structural load points, providing a rated static load capacity of 150kg (user 120kg + equipment 30kg), with a maximum deflection of 2mm under full load, and shall be corrosion-resistant to humidity up to 95% RH for indoor care facility environments.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-008 title="SUB-VEHICLEPLATFORM-008"}
The Electronics Bay SHALL be constructed from steel or aluminium enclosure rated IP54 (dust-protected, splash-resistant), with internal thermal management maintaining component ambient temperature between 0°C and 55°C during operation, and shall withstand vibration levels up to 2g at 5-50Hz without component dislodgement or connector failure.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-009 title="SUB-VEHICLEPLATFORM-009"}
The Chassis Frame SHALL be designed and tested in accordance with ISO 7176-8 (Requirements for static, impact, and fatigue strengths for wheelchairs) for structural adequacy under static loading of 1.5× maximum user mass plus equipment, and shall withstand impact loads of 70J without permanent deformation of safety-critical members.
**Verification:** Test
:::

:::requirement{#SUB-VEHICLEPLATFORM-010 title="SUB-VEHICLEPLATFORM-010"}
After any chassis shock event exceeding 3g vertical acceleration detected by the inclinometer shock channel, the Safety Monitor Processor SHALL assert a structural-integrity-check-required flag, inhibit all vehicle propulsion, and require authorised maintenance acknowledgement before resuming normal operation.
**Verification:** Inspection
:::

---

# Interface Requirements

## [IFC] Interface Requirements

:::requirement{#IFC-REQ-005 title="IFC-REQ-005"}
The interface between the EEG Accessibility Buggy and the Service Diagnostic Laptop SHALL use USB-C wired connection for firmware updates, diagnostic log export, configuration changes, and calibration data, protected by service authentication key with role-based access control.
**Verification:** Demonstration
:::

## BCI Processing Subsystem

:::requirement{#IFC-BCIPROCESSINGSUBSYSTEM-001 title="IFC-BCIPROCESSINGSUBSYSTEM-001"}
The interface between the EEG Accessibility Buggy and the EEG Headset SHALL use Bluetooth Low Energy 5.0 to stream 16-32 channels of 24-bit EEG data at 250 Hz with end-to-end latency not exceeding 50ms from electrode to onboard processor.
**Verification:** Test
:::

:::requirement{#IFC-BCIPROCESSINGSUBSYSTEM-002 title="IFC-BCIPROCESSINGSUBSYSTEM-002"}
The interface between the EEG Acquisition Module and the Artifact Rejection Engine SHALL transfer 32-channel EEG sample arrays at 256 samples/second with a maximum inter-process latency of 5ms and sample loss rate not exceeding 0.1%.
**Verification:** Test
:::

:::requirement{#IFC-BCIPROCESSINGSUBSYSTEM-003 title="IFC-BCIPROCESSINGSUBSYSTEM-003"}
The interface between the Artifact Rejection Engine and the Feature Extraction Processor SHALL transfer cleaned 32-channel EEG epochs in 1-second windows with 50% overlap, formatted as float32 arrays, at a throughput of 2 epochs/second per channel.
**Verification:** Test
:::

:::requirement{#IFC-BCIPROCESSINGSUBSYSTEM-004 title="IFC-BCIPROCESSINGSUBSYSTEM-004"}
The interface between the Feature Extraction Processor and the BCI Classifier SHALL pass feature vectors containing CSP-projected band powers and SSVEP spectral amplitudes as float32 vectors, with timestamp and quality index, within 30ms of epoch receipt.
**Verification:** Test
:::

:::requirement{#IFC-BCIPROCESSINGSUBSYSTEM-005 title="IFC-BCIPROCESSINGSUBSYSTEM-005"}
The interface between the BCI Classifier and the Command Arbitration Module SHALL pass command probability vectors for four navigation classes (forward, left, right, stop) as float32 with per-class confidence values, SNR index, and rolling 2-minute accuracy metric, at minimum 2Hz.
**Verification:** Test
:::

:::requirement{#IFC-BCIPROCESSINGSUBSYSTEM-006 title="IFC-BCIPROCESSINGSUBSYSTEM-006"}
The interface between the Command Arbitration Module and the Drive Subsystem SHALL use a CAN 2.0B message at 500 kbit/s, transmitting validated drive command (4 discrete values) and a 16-bit cyclic status counter, with maximum transmission latency of 5ms.
**Verification:** Test
:::

## Communication Subsystem

:::requirement{#IFC-COMMUNICATIONSUBSYSTEM-001 title="IFC-COMMUNICATIONSUBSYSTEM-001"}
The EEG Accessibility Buggy SHALL communicate with the Companion App via BLE 5.0 LE Secure Connections (AES-CCM-128), providing status updates at ≥1 Hz and emergency notifications with ≤500ms latency.
**Verification:** Test
:::

:::requirement{#IFC-COMMUNICATIONSUBSYSTEM-002 title="IFC-COMMUNICATIONSUBSYSTEM-002"}
The interface between the EEG Accessibility Buggy and the Facility CMMS SHALL use a REST API over facility Wi-Fi (802.11ac) with TLS 1.3 encryption for automated maintenance log submission, fault reporting, and fleet telemetry, authenticated via API key or OAuth 2.0.
**Verification:** Test
:::

:::requirement{#IFC-COMMUNICATIONSUBSYSTEM-003 title="IFC-COMMUNICATIONSUBSYSTEM-003"}
The interface between the Communication Controller and the Bluetooth LE Module SHALL use UART at 1Mbps with hardware flow control (RTS/CTS), transferring HCI command and event packets per the Bluetooth HCI specification, with a maximum transfer latency of 2ms per HCI packet.
**Verification:** Test
:::

:::requirement{#IFC-COMMUNICATIONSUBSYSTEM-004 title="IFC-COMMUNICATIONSUBSYSTEM-004"}
The interface between the Communication Controller and the Cellular Modem SHALL use USB 2.0 (CDC-ECM class) at 480Mbps with AT-command control channel, providing the Communication Controller with Ethernet-over-USB connectivity for LTE data at a maximum round-trip latency of 100ms to the LTE base station.
**Verification:** Test
:::

:::requirement{#IFC-COMMUNICATIONSUBSYSTEM-005 title="IFC-COMMUNICATIONSUBSYSTEM-005"}
The interface between the Bluetooth LE Module and the EEG headset SHALL use BLE 5.2 with a connection interval of 7.5ms, carrying 8-channel 250Hz EEG sample packets encoded in the EEG Headset Profile (proprietary GATT service) with end-to-end latency from electrode to BCI Acquisition Module not exceeding 20ms.
**Verification:** Test
:::

## Drive Subsystem

:::requirement{#IFC-DRIVESUBSYSTEM-001 title="IFC-DRIVESUBSYSTEM-001"}
The interface between the Motor Controller Unit and the Left Drive Motor Assembly SHALL consist of three-phase 20kHz PWM gate-drive signals at 48V and a 1024 PPR quadrature encoder feedback channel operating at a minimum sample rate of 10kHz, with an encoder signal propagation delay not exceeding 1ms.
**Verification:** Test
:::

:::requirement{#IFC-DRIVESUBSYSTEM-002 title="IFC-DRIVESUBSYSTEM-002"}
The interface between the Motor Controller Unit and the Right Drive Motor Assembly SHALL consist of three-phase 20kHz PWM gate-drive signals at 48V and a 1024 PPR quadrature encoder feedback channel operating at a minimum sample rate of 10kHz, with an encoder signal propagation delay not exceeding 1ms.
**Verification:** Test
:::

:::requirement{#IFC-DRIVESUBSYSTEM-003 title="IFC-DRIVESUBSYSTEM-003"}
The interface between the Drive Power Stage and the Motor Controller Unit SHALL provide the 48V motor bus voltage to the gate driver circuits, return overcurrent trip status on a dedicated logic signal within 5ms of a trip event, and support a pre-charge sequencing signal from the Motor Controller Unit to enable soft-start of the 48V rail.
**Verification:** Test
:::

## HMI Subsystem

:::requirement{#IFC-HMISUBSYSTEM-001 title="IFC-HMISUBSYSTEM-001"}
The interface between the Main Application Processor and the Display Unit SHALL use SPI at 40MHz with a dedicated chip-select line, supporting a minimum frame rate of 30 fps at 800x480 resolution with 16-bit colour depth.
**Verification:** Test
:::

:::requirement{#IFC-HMISUBSYSTEM-002 title="IFC-HMISUBSYSTEM-002"}
The interface between the Main Application Processor and the Audio Alert Module SHALL use I2C at 400kHz carrying alert-code commands with a command-to-onset latency not exceeding 50ms, where each alert code maps to a distinct tone pattern (frequency, duration, duty cycle) stored in the Audio Alert Module firmware.
**Verification:** Test
:::

:::requirement{#IFC-HMISUBSYSTEM-003 title="IFC-HMISUBSYSTEM-003"}
The interface between the Main Application Processor and the Status LED Array SHALL use SPI at 10MHz carrying RGB colour and brightness commands with a frame update latency not exceeding 20ms, supporting minimum 6 independently addressable LED elements.
**Verification:** Test
:::

## Perception Subsystem

:::requirement{#IFC-PERCEPTIONSUBSYSTEM-001 title="IFC-PERCEPTIONSUBSYSTEM-001"}
The interface between the Forward Depth Sensor Array and the Perception MCU SHALL use I2C at 400 kHz (400 kHz I2C mode per UM10204) with three individually addressable sensor nodes, transferring an 8x8 depth grid per sensor at 10Hz with CRC-8 error detection on each transfer.
**Verification:** Test
:::

:::requirement{#IFC-PERCEPTIONSUBSYSTEM-002 title="IFC-PERCEPTIONSUBSYSTEM-002"}
The interface between the Side Proximity Sensor Pair and the Perception MCU SHALL use two dedicated GPIO lines (one per side), active-high TTL at 3.3V logic levels, with each sensor asserting the line within 30ms of detecting an object within 0.5m.
**Verification:** Test
:::

:::requirement{#IFC-PERCEPTIONSUBSYSTEM-003 title="IFC-PERCEPTIONSUBSYSTEM-003"}
The interface between the Perception MCU and the Safety Monitor Processor SHALL use SPI at 1MHz, transferring a 4-byte obstacle alert frame at 10Hz, containing a proximity status bitmask, minimum detected distance, and a rolling 8-bit frame counter, with the Safety Monitor Processor asserting a communication fault if two consecutive frames are missed.
**Verification:** Test
:::

## Power Subsystem

:::requirement{#IFC-POWERSUBSYSTEM-001 title="IFC-POWERSUBSYSTEM-001"}
The interface between the EEG Accessibility Buggy and the Facility Charging Infrastructure SHALL use galvanically isolated IEC 60320 C13/C14 connectors or proprietary docking contacts, accepting 230V 50Hz or 120V 60Hz mains input, with drive functions locked during charging.
**Verification:** Test
:::

:::requirement{#IFC-POWERSUBSYSTEM-002 title="IFC-POWERSUBSYSTEM-002"}
The interface between the Battery Management System and the Safety Monitor Processor SHALL transmit a thermal fault signal within 50ms of any cell temperature exceeding 55°C, using a dedicated GPIO hardwired fault line with active-low logic, independent of CAN bus.
**Verification:** Test
:::

:::requirement{#IFC-POWERSUBSYSTEM-003 title="IFC-POWERSUBSYSTEM-003"}
The interface between the Battery Management System and the Main Application Processor SHALL use CAN 2.0B at 250 kbit/s, transmitting pack voltage, individual cell voltages, SoC, temperature summary, and pack current at 100ms intervals.
**Verification:** Test
:::

## Safety Subsystem

:::requirement{#IFC-SAFETYSUBSYSTEM-001 title="IFC-SAFETYSUBSYSTEM-001"}
The interface between the EEG Accessibility Buggy and the Facility Emergency/Nurse Call System SHALL transmit iBeacon-compatible BLE advertisements containing device ID and alert type within 500ms of Emergency Stop or seizure detection trigger, receivable by facility BLE gateways at a minimum range of 30m.
**Verification:** Test
:::

:::requirement{#IFC-SAFETYSUBSYSTEM-002 title="IFC-SAFETYSUBSYSTEM-002"}
The dedicated GPIO watchdog interface between the Main Application Processor (producer) and the Safety Monitor Processor (consumer) SHALL carry a 20ms-period square-wave heartbeat signal only, with no command or data payload on this line.
**Verification:** Test
:::

:::requirement{#IFC-SAFETYSUBSYSTEM-003 title="IFC-SAFETYSUBSYSTEM-003"}
The interface between the Safety Monitor Processor and the Motor Power Isolation Relay SHALL use dual-channel opto-isolated control lines (one per relay coil), each driven by a dedicated GPIO output; both channels SHALL be required to be asserted simultaneously for relay energisation, such that single-channel failure results in relay de-energisation.
**Verification:** Test
:::

:::requirement{#IFC-SAFETYSUBSYSTEM-004 title="IFC-SAFETYSUBSYSTEM-004"}
The interface between the Inclinometer Tilt Sensor Unit and the Safety Monitor Processor SHALL be SPI at 1MHz, transferring a 6-byte data frame containing 16-bit pitch angle, 16-bit roll angle, and 16-bit CRC at 100Hz; the Safety Monitor Processor SHALL reject frames with CRC errors and SHALL trigger Emergency Stop if 5 consecutive frames are rejected.
**Verification:** Test
:::

:::requirement{#IFC-SAFETYSUBSYSTEM-005 title="IFC-SAFETYSUBSYSTEM-005"}
The interface between the Seizure Detection Module and the Safety Monitor Processor SHALL be a shared memory region on the Safety Monitor Processor, updated by the Seizure Detection Module at 50Hz with a 1-byte status word (bit 0 = seizure flag, bit 1 = algorithm health), protected by a mutex; the Safety Monitor Processor SHALL read this region within 10ms of each update.
**Verification:** Test
:::

## Vehicle Platform

:::requirement{#IFC-VEHICLEPLATFORM-001 title="IFC-VEHICLEPLATFORM-001"}
The interface between the Electronics Bay and the Chassis Frame SHALL use four M6 stainless steel fasteners with self-locking nuts, providing a pull-out strength of at least 500N per fastener, and shall include a rubber vibration isolator rated for 3g shock attenuation to protect electronic components during kerb crossing.
**Verification:** Test
:::

:::requirement{#IFC-VEHICLEPLATFORM-002 title="IFC-VEHICLEPLATFORM-002"}
The interface between the Wheel and Caster Assembly and the Chassis Frame SHALL use a tool-free quick-release mechanism requiring a maximum 15N force for removal and replacement, with a mechanical interlock preventing partial engagement (either fully locked or fully detached).
**Verification:** Test
:::

---

# Architecture Decisions

## [ARC] Architecture Decisions

:::requirement{#ARC-REQ-001 title="ARC-REQ-001"}
The BCI Processing Subsystem SHALL implement combined EEG signal acquisition and intent classification on a single embedded processor, sharing the BLE headset interface and neural signal pipeline to achieve end-to-end latency <50ms. Alternative: separate acquisition and classification boards — rejected because the 50ms latency budget leaves insufficient margin for inter-board data transfer and additional hardware increases failure points.
**Verification:** Analysis
:::

:::requirement{#ARC-REQ-002 title="ARC-REQ-002"}
ARC: Safety Subsystem — Independent safety channel with dedicated processor. IEC 61508 (Functional safety of E/E/PE safety-related systems) requires that SIL 3 safety functions be independent of the control path. The Safety Subsystem uses a separate watchdog processor and hardware-level motor power disconnect that operates even if the main application processor fails. Alternative considered: software-only safety monitor on the main processor — rejected because a single processor fault would disable both control and safety, violating SIL 3 independence.
**Verification:** Inspection
:::

:::requirement{#ARC-REQ-003 title="ARC-REQ-003"}
The Perception Subsystem SHALL use dedicated sensor hardware and processing independent of the Drive Subsystem, so that obstacle detection capability is preserved when the Drive Subsystem is in a fault state and Drive Subsystem operation is preserved when the Perception Subsystem fails. Alternative: integrated drive-and-perception controller — rejected because sensor processor lockup would simultaneously disable obstacle detection and emergency braking.
**Verification:** Analysis
:::

:::requirement{#ARC-REQ-004 title="ARC-REQ-004"}
ARC: Communication Subsystem — Separated from HMI to isolate wireless reliability from user feedback. BLE and Wi-Fi radios share antenna resources and protocol stacks but serve different latency requirements (500ms emergency alerts vs. background CMMS telemetry). Grouping all external data links together allows a single security boundary for TLS and GDPR compliance. Alternative considered: distributing BLE to BCI Processing and Wi-Fi to HMI — rejected because scattered radio hardware complicates EMC certification and security auditing.
**Verification:** Inspection
:::

:::requirement{#ARC-REQ-005 title="ARC-REQ-005"}
The Power Subsystem architecture SHALL implement dual-authority thermal protection: the Battery Management System SHALL signal the Safety Subsystem when cell temperature exceeds 60°C or cell voltage imbalance exceeds 100mV, and the Safety Subsystem SHALL independently command the 48V disconnect relay. Alternative: BMS-only disconnect — rejected because a BMS processor fault would prevent disconnect during thermal runaway (H-003, catastrophic severity).
**Verification:** Test
:::

:::requirement{#ARC-REQ-006 title="ARC-REQ-006"}
ARC: Drive Subsystem — Differential-drive 48V BLDC architecture with dedicated Motor Controller Unit. Four-component decomposition (Motor Controller Unit, Left/Right Drive Motor Assemblies, Drive Power Stage) chosen over a pre-integrated motor controller module. The dual-channel MCU architecture allows independent velocity closed-loop control on each motor, enabling differential steering without a mechanical differential. The Drive Power Stage is isolated from the 48V bus via the hardware Motor Power Isolation Relay (Safety Subsystem) rather than software-only interlock — this ensures safe-state is reachable even with MCU firmware failure, consistent with SIL 2 (IEC 61508) allocation.
**Verification:** Inspection
:::

:::requirement{#ARC-REQ-007 title="ARC-REQ-007"}
The Vehicle Platform architecture SHALL implement the Electronics Bay as a separate sealed IP54 enclosure mounted to the Chassis Frame, containing the CAN bus harness within a defined EMC boundary and enabling field serviceability without chassis disassembly. Alternative: electronics integrated into chassis weldment — rejected because weld-bonded enclosures prevent field serviceability and make EMC sealing impractical.
**Verification:** Inspection
:::

---
