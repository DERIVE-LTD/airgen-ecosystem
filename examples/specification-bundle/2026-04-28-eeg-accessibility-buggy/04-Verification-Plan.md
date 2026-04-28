# Verification Plan

**Project:** se-eeg-accessibility-buggy
**Generated:** 2026-04-28

---

# Verification Requirements

## [VER] Verification Requirements

:::requirement{#VER-REQ-040 title="VER-REQ-040"}
Verify SUB-REQ-008: stream 100 pre-annotated EEG epochs (50 containing known EMG bursts, 50 containing known 50Hz mains interference) through the Artifact Rejection Engine. Measure rejection rate on contaminated epochs (≥90% required) and false rejection rate on clean epochs (≤10% required). Process all 100 epochs; confirm end-to-end epoch latency ≤20ms measured from epoch DMA transfer start to clean-epoch availability on output queue.
**Verification:** Test
:::

:::requirement{#VER-REQ-041 title="VER-REQ-041"}
Verify SUB-REQ-009: recruit five representative test subjects (each with a previously completed calibration session). For each subject, run a 10-minute continuous BCI driving session. Extract rolling 2-minute accuracy windows at 30-second intervals. Calculate mean accuracy per subject and across cohort. Pass criterion: all five subjects achieve ≥75% mean classification accuracy across the session; no 2-minute window falls below 60% (single-window floor).
**Verification:** Test
:::

:::requirement{#VER-REQ-042 title="VER-REQ-042"}
Verify IFC-REQ-011: connect a logic analyser to the inter-process shared memory bus between EEG Acquisition Module and Artifact Rejection Engine. Stream 256 sample/second × 32 channels at rated throughput for 5 minutes. Measure: (a) actual sample delivery rate must be 256 ±2 Hz; (b) maximum inter-process latency measured per epoch must be ≤5ms; (c) replay 1000 epochs and count frames with dropped channels — loss rate must be ≤0.1%.
**Verification:** Test
:::

:::requirement{#VER-REQ-103 title="VER-REQ-103"}
Verify SUB-REQ-079: Configure the Main Application Processor to accept BCI-derived navigation commands via external software interface with HMAC-SHA256 authentication. (a) Send 100 valid signed commands and confirm all are accepted and executed. (b) Send 20 commands with tampered signatures and confirm all are rejected and logged with REJECTED_CMD fault code. (c) Send 20 commands with replayed nonces and confirm rejection. Pass: zero valid commands rejected, zero tampered commands accepted, each rejection logged within 50ms.
**Verification:** Test
:::

## BCI Processing Subsystem

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-001 title="VER-BCIPROCESSINGSUBSYSTEM-001"}
The BLE 5.0 EEG headset interface SHALL be verified by: (a) streaming 32-channel 24-bit EEG data at 250Hz for 30 minutes and measuring packet loss rate across 5 runs at 5m and 10m range, confirming < 0.1% at 5m and < 1% at 10m; (b) measuring BLE connection establishment time across 20 trials confirming < 3s; (c) confirming firmware lock on BLE advertisement interval prevents interference with safety BLE channel.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-002 title="VER-BCIPROCESSINGSUBSYSTEM-002"}
The BCI Processing Subsystem SNR degradation response SHALL be verified by injecting an attenuated noise baseline into the EEG Acquisition Module input, measuring elapsed time from SNR threshold crossing to STOP command on the Drive Subsystem CAN bus. Pass: STOP emitted within 3000ms on ≥10/10 runs; no drive commands emitted during suppression window.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-003 title="VER-BCIPROCESSINGSUBSYSTEM-003"}
The BCI Processing Subsystem command pipeline latency SHALL be verified by replaying a known motor imagery EEG epoch at the acquisition input and measuring end-to-end latency from BLE injection to validated CAN command across 50 consecutive epochs. Pass: 95th percentile latency ≤150ms; no individual measurement >200ms.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-004 title="VER-BCIPROCESSINGSUBSYSTEM-004"}
The Command Arbitration Module CAN bus interface SHALL be verified by capturing 1000 command frames during a live BCI session. Pass: all frames comply with CAN 2.0B format; status counter increments monotonically; no inter-frame gap >55ms; maximum transmission latency ≤5ms from software send to bus dominant.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-005 title="VER-BCIPROCESSINGSUBSYSTEM-005"}
Verify SUB-REQ-042: Inject out-of-bounds BCI command pattern (velocity > physical maximum) into the BCI Processing Subsystem output and measure time to safe-stop signal assertion. Pass: safe-stop asserted within 100ms. Also block watchdog kick for 250ms and verify safe-stop assertion.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-006 title="VER-BCIPROCESSINGSUBSYSTEM-006"}
Verify the Feature Extraction Processor loads and applies per-user CSP spatial filter matrices from encrypted calibration storage within 5 seconds of user session initialisation.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-007 title="VER-BCIPROCESSINGSUBSYSTEM-007"}
Verify the Artifact Rejection Engine watchdog interface causes the BCI Processing Subsystem supervisor to reset the engine and log the event when no valid epoch output is received for more than 500ms.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-008 title="VER-BCIPROCESSINGSUBSYSTEM-008"}
Verify the Main Application Processor encrypts EEG biometric data using AES-256 at rest and TLS 1.3 in transit, enforces role-based access control with at least three roles, and maintains an append-only audit log.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-009 title="VER-BCIPROCESSINGSUBSYSTEM-009"}
Verify the Artifact Rejection Engine operates from the 3.3V logic rail with maximum 250mA peak current draw during active processing and maintains operation for supply voltage variation between 3.0V and 3.6V.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-010 title="VER-BCIPROCESSINGSUBSYSTEM-010"}
Verify electrode interface components contacting user scalp meet ISO 10993-1 (Biological evaluation of medical devices, Part 1) biocompatibility requirements: (a) inspect material data sheets confirming each scalp-contact material has been classified per ISO 10993-1 risk framework for prolonged skin contact; (b) measure contact impedance at 1kHz before and after 50 decontamination cycles with specified clinical disinfectant. Pass: all materials carry ISO 10993-1 biocompatibility classification; contact impedance change ≤ 10% after 50 cycles. Fail: any unclassified material; impedance change > 10%.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-011 title="VER-BCIPROCESSINGSUBSYSTEM-011"}
Verify the Artifact Rejection Engine occupies no more than 30% of available Feature Extraction Processor CPU cycles during continuous EEG processing at 250 SPS per channel, with deterministic maximum latency of 4ms per processing frame.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-012 title="VER-BCIPROCESSINGSUBSYSTEM-012"}
Verify the Artifact Rejection Engine transfers cleaned 32-channel EEG epochs as float32 arrays in 1-second windows with 50% overlap at a throughput of 2 epochs/second per channel to the Feature Extraction Processor.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-013 title="VER-BCIPROCESSINGSUBSYSTEM-013"}
Verify the BCI Classifier passes command probability vectors for four navigation classes as float32 with per-class confidence, SNR index, and rolling 2-minute accuracy metric at minimum 2Hz to the Command Arbitration Module.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-014 title="VER-BCIPROCESSINGSUBSYSTEM-014"}
Verify IFC-REQ-013: Configure a test harness injecting synthetic CSP-projected feature vectors at 100Hz from the Feature Extraction Processor to the BCI Classifier. Verify that 1000 consecutive feature vectors are received, decoded, and classification results returned within the 10ms latency budget. Pass: all 1000 vectors received with CRC intact; latency p99 ≤ 10ms measured by timestamp on both ends; no dropped vectors. Fail: any CRC error, dropped vector, or latency exceedance.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-015 title="VER-BCIPROCESSINGSUBSYSTEM-015"}
The USB-C Diagnostic Port functionality SHALL be verified by executing the automated diagnostic test suite from a service laptop and measuring: (a) all test categories complete within 20 minutes; (b) pass/fail results individually reported with timestamped log entries; (c) graceful reconnection without data loss after disconnect during a test run.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-016 title="VER-BCIPROCESSINGSUBSYSTEM-016"}
Verify SYS-REQ-007 system-level integration: With the fully integrated EEG Accessibility Buggy operating in Normal Navigation mode at 4 km/h, inject a sustained SNR degradation below the classification threshold via a BCI test signal source. (a) Confirm the system does NOT transition to Degraded/Assisted mode within the first 4.9 seconds of sustained low SNR. (b) Confirm the system transitions to Degraded/Assisted mode within 5.5 seconds of sustained low SNR onset (5-second threshold plus 500ms tolerance). (c) Verify speed is clamped to 3 km/h within 200ms of mode transition. (d) Verify BCI command set reduces to binary go/stop — inject a left-turn motor imagery pattern and confirm it is NOT executed. (e) Restore SNR above threshold and verify the system returns to Normal Navigation mode within 10 seconds. Fail: transition before 4.9s, transition after 5.5s, speed exceeds 3 km/h, or non-binary command accepted.
**Verification:** Test
:::

:::requirement{#VER-BCIPROCESSINGSUBSYSTEM-017 title="VER-BCIPROCESSINGSUBSYSTEM-017"}
Verify SYS-REQ-008 system-level integration: With the fully integrated EEG Accessibility Buggy operating in Normal Navigation mode, inject a declining BCI accuracy profile via a test signal source so that rolling 2-minute accuracy crosses the 70% threshold. (a) Confirm the system does NOT transition to Degraded/Assisted mode while rolling accuracy remains at 71% or above. (b) Confirm the system transitions to Degraded/Assisted mode within 2 seconds of rolling accuracy dropping to 69% or below. (c) Verify an audible alert of minimum 65 dBA is generated within 1 second of mode transition. (d) Verify speed is limited to 3 km/h. (e) Restore accuracy above 70% for a full 2-minute window and verify Normal Navigation mode resumes. Fail: premature transition, transition delayed beyond 2 seconds, no audible alert, speed exceeds 3 km/h in Degraded mode, or no recovery after accuracy restoration.
**Verification:** Test
:::

## Communication Subsystem

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-001 title="VER-COMMUNICATIONSUBSYSTEM-001"}
The Communication Subsystem cellular modem network isolation SHALL be verified by injecting UDP packets addressed to the CAN bus gateway IP range from the modem interface while monitoring all internal bus traffic. Pass: zero packets from the cellular modem interface appear on CAN bus segments across 100 injection attempts.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-002 title="VER-COMMUNICATIONSUBSYSTEM-002"}
Verify SUB-REQ-038: Trigger simulated Emergency Stop event in the Communications Controller and measure BLE advertisement receipt timestamp at BLE gateway sniffer. Pass: alert received within 500ms of trigger for 10 consecutive tests with gateway at 10m range.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-003 title="VER-COMMUNICATIONSUBSYSTEM-003"}
Verify IFC-REQ-027: connect Communication Controller and Cellular Modem via USB, issue AT+CGDCONT APN configuration command, initiate LTE data session, and measure round-trip ping latency to a test server over LTE. Pass: USB enumeration succeeds as CDC-ECM, APN configured, ping RTT <= 100ms in 95th percentile over 100 measurements.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-004 title="VER-COMMUNICATIONSUBSYSTEM-004"}
Verify IFC-REQ-028: pair BLE module with EEG headset reference device, stream 8-channel EEG data for 60 seconds, timestamp each packet at BLE receive and at BCI Acquisition Module input. Pass: connection interval 7.5ms confirmed in BLE sniffer trace, packet loss < 0.1%, end-to-end latency <= 20ms in 99th percentile.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-005 title="VER-COMMUNICATIONSUBSYSTEM-005"}
Verify SUB-REQ-067: Trigger a simulated Seizure Emergency Stop on the EEG Accessibility Buggy while the facility emergency system BLE receiver is active. Measure time from E-Stop trigger to BLE alert packet receipt at the receiver. Pass if packet received within 500ms with correct device ID, alert type code, GPS coordinates, and UTC timestamp on 10 consecutive trials.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-006 title="VER-COMMUNICATIONSUBSYSTEM-006"}
Verify the Bluetooth LE Module maintains a stable BLE 5.2 connection sustaining 32-channel EEG data at 8kHz sample rate (512kbps) within a 3m operating range with no physical obstructions.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-007 title="VER-COMMUNICATIONSUBSYSTEM-007"}
Verify the Cellular Modem authenticates to the remote telemetry server using mutual TLS 1.3 with a device-unique X.509 certificate, rejecting server certificates not signed by the system root CA.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-008 title="VER-COMMUNICATIONSUBSYSTEM-008"}
Verify the Cellular Modem transmits session telemetry to the remote server at a minimum interval of 10 seconds with a maximum uplink latency of 2 seconds under nominal 4G coverage.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-009 title="VER-COMMUNICATIONSUBSYSTEM-009"}
Verify IFC-REQ-005: Connect a service laptop via USB-C cable and execute firmware flash, configuration export, fault log download, and calibration data upload operations. Pass: (a) each operation completes successfully without data corruption (hash check); (b) firmware flash completes within 10 minutes; (c) fault log export produces structured JSON with all required fields; (d) calibration data import round-trips without any field alteration. Fail: any operation fails, hash mismatch, or log missing required fields.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-010 title="VER-COMMUNICATIONSUBSYSTEM-010"}
Verify SUB-REQ-056: Connect a test host to the Communication Subsystem USB-C service port. Issue each motor response verification command individually from the defined diagnostic API. Measure time from command issuance to pass/fail result with logged timestamp. Pass: all commands return a result with timestamp within 10 seconds of issuance on each of 10 consecutive command cycles. Fail: any result arrives after 10 seconds or lacks a timestamp.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-011 title="VER-COMMUNICATIONSUBSYSTEM-011"}
Verify IFC-REQ-004: Pair the vehicle with a test smartphone running the companion app via BLE 5.0. (a) Confirm status display (operating mode, battery level, session history) updates at ≥1 Hz. (b) Trigger a simulated emergency stop and verify BLE emergency alert reaches the companion app within 500ms, measured from E-stop relay actuation to app notification. (c) Capture BLE traffic with a sniffer and confirm all advertising and connection PDUs use LE Secure Connections pairing with AES-128 link-layer encryption (no legacy pairing). Pass: all three criteria met. Fail: update rate <1Hz, alert latency >500ms, or unencrypted PDUs detected.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-012 title="VER-COMMUNICATIONSUBSYSTEM-012"}
Verify IFC-REQ-006: Configure a test CMMS server endpoint on the facility Wi-Fi (802.11ac) network with TLS 1.3 and API key authentication. (a) Trigger a maintenance event and confirm a REST API POST is received by the test server within 30 seconds with the correct JSON schema (maintenance log, fault code, fleet ID). (b) Configure an invalid API key on the server and confirm the vehicle logs an CMMS_AUTH_FAIL event and ceases retry attempts after 3 failures. (c) Capture Wi-Fi traffic and confirm all HTTP traffic is TLS 1.3 — no cleartext requests. Pass: all three criteria met.
**Verification:** Test
:::

:::requirement{#VER-COMMUNICATIONSUBSYSTEM-013 title="VER-COMMUNICATIONSUBSYSTEM-013"}
Verify IFC-REQ-025: Connect a UART analyser to the Communication Controller to Bluetooth LE Module UART link. (a) Issue a sequence of 500 HCI commands at maximum throughput and measure end-to-end transfer latency — Pass: ≤5ms latency for 95th percentile across 500 commands. (b) Disable hardware flow control (RTS/CTS) lines and confirm data corruption is detected — Pass: module reports HCI error code within 100ms of buffer overrun. (c) Measure actual baud rate using frequency counter — Pass: 1.000 Mbps ±0.5%. Fail: latency >5ms at P95, error not detected within 100ms, or baud error >0.5%.
**Verification:** Test
:::

## Drive Subsystem

:::requirement{#VER-DRIVESUBSYSTEM-001 title="VER-DRIVESUBSYSTEM-001"}
Verify IFC-REQ-018: Apply 20kHz PWM gate-drive signals to the Left Drive Motor Assembly and measure encoder feedback period at 10kHz sample rate with an oscilloscope. Pass criteria: PWM frequency within ±1% of 20kHz, encoder signal received within 1ms propagation delay, and velocity closed-loop error converges to within ±0.1 km/h in steady state.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-002 title="VER-DRIVESUBSYSTEM-002"}
Verify IFC-REQ-019: Apply 20kHz PWM gate-drive signals to the Right Drive Motor Assembly and measure encoder feedback period at 10kHz sample rate. Pass criteria: symmetric with IFC-REQ-018 verification — PWM frequency within ±1% of 20kHz, encoder delay within 1ms, velocity error within ±0.1 km/h. Additionally verify left-right encoder latency difference does not exceed 0.5ms.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-003 title="VER-DRIVESUBSYSTEM-003"}
Verify IFC-REQ-020: Inject an overcurrent condition on the Drive Power Stage test bench and measure the time from overcurrent event to logic-level trip signal at the Motor Controller Unit. Pass criteria: trip signal received within 5ms, pre-charge sequence completes without bus voltage overshoot exceeding 5% above 48V nominal, MCU logs a fault code within 10ms of receiving trip signal.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-004 title="VER-DRIVESUBSYSTEM-004"}
The Motor Controller Unit CAN heartbeat watchdog SHALL be verified by injecting a CAN heartbeat loss and measuring elapsed time to zero motor velocity. Pass: both motors reach zero velocity within 250ms, MCU remains in safe state until a valid restart CAN frame is received, and no uncommanded restart occurs within a 30-second observation window.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-005 title="VER-DRIVESUBSYSTEM-005"}
Verify SUB-REQ-058: Suppress MCU CAN heartbeat and measure time to coast-to-stop state and MCU_FAULT assertion to Safety Monitor Processor. Pass: coast-to-stop commanded within 100 ms, MCU_FAULT asserted within 50 ms of timeout detection, 10 trials.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-006 title="VER-DRIVESUBSYSTEM-006"}
The EEG Accessibility Buggy SHALL be verified to confirm that in Care Attendant Override mode the Drive Subsystem responds to joystick inputs within 100ms, obstacle detection remains active with ≤200ms response, maximum speed is limited to 6 km/h, and the system sustains override operation for ≥30 minutes at 50% throttle.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-007 title="VER-DRIVESUBSYSTEM-007"}
Verify the Motor Controller Unit implements closed-loop velocity control at 100 Hz with 1024 PPR quadrature encoder feedback, achieving steady-state velocity error not exceeding ±0.1 km/h at any commanded speed.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-008 title="VER-DRIVESUBSYSTEM-008"}
Verify the Motor Controller Unit enforces the 6 km/h Normal Navigation mode speed limit and 2 km/h Restricted mode speed limit by clamping velocity commands exceeding each threshold before motor drive stage application.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-009 title="VER-DRIVESUBSYSTEM-009"}
Verify the Drive Power Stage trips the hardware overcurrent protection and isolates motor phase outputs within 5ms when per-channel motor current exceeds 30A, independent of Motor Controller Unit firmware.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-010 title="VER-DRIVESUBSYSTEM-010"}
Verify the Left and Right Drive Motor Assemblies each deliver a minimum continuous shaft output of 250W at 48V DC across 0°C to 40°C with traction maintained on level indoor surfaces up to 5% gradient.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-011 title="VER-DRIVESUBSYSTEM-011"}
Verify the Motor Controller Unit hardware overcurrent protection disconnects motor phase outputs within 5ms when phase current exceeds 120% of rated maximum, with no firmware execution required.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-012 title="VER-DRIVESUBSYSTEM-012"}
Verify the Drive Subsystem enforces a maximum speed cap of 3 km/h in Degraded/Assisted mode by limiting PWM duty cycle to 50% of Normal Navigation maximum, independent of BCI or joystick input value.
**Verification:** Test
:::

:::requirement{#VER-DRIVESUBSYSTEM-013 title="VER-DRIVESUBSYSTEM-013"}
Verify SUB-REQ-077: Submit Drive Subsystem assembly (Motor Controller Unit and Motor Power Isolation Relay) for IEC 60601-1-2 (Medical Electrical Equipment — Electromagnetic disturbances) conducted and radiated emissions and immunity testing by an accredited test laboratory. Pass: test report from accredited lab confirms compliance with all applicable limits and no performance degradation during immunity tests. Fail: any emissions exceed limits or immunity test causes motor controller malfunction.
**Verification:** Test
:::

## HMI Subsystem

:::requirement{#VER-HMISUBSYSTEM-001 title="VER-HMISUBSYSTEM-001"}
Verify SUB-REQ-032: trigger E-stop via software command and measure Audio Alert Module SPL with a calibrated sound level meter at 1m. Record onset time from E-stop signal assertion to first audible tone. Pass: SPL >= 80dB at 1m, onset <= 100ms, sustained tone until E-stop cleared, in 5 consecutive tests.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-002 title="VER-HMISUBSYSTEM-002"}
The Carer Override Module switch activation latency SHALL be verified using a hardware override switch simulator, measuring the timestamp delta from switch assertion to first joystick command accepted by the Drive Subsystem via CAN bus analyser. Pass: delta ≤100ms on 10 consecutive trials.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-003 title="VER-HMISUBSYSTEM-003"}
Verify IFC-REQ-029: inject alert-code 0x01 (Emergency Stop) via I2C at 400kHz from the application processor, measure time from I2C transaction completion to first audio sample output from Audio Alert Module. Pass: onset latency <= 50ms in 10 consecutive trials, tone frequency and duration match alert-code-0x01 specification.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-004 title="VER-HMISUBSYSTEM-004"}
Verify SUB-REQ-047: power up the display unit, enter Normal Navigation mode with a test BCI signal source, and measure display update frequency for BCI accuracy, speed, battery SoC, and mode fields over 30 seconds. Measure screen brightness with a calibrated photometer at 0.5m distance. Pass: all four fields update >= 2Hz, brightness >= 300 cd/m2.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-005 title="VER-HMISUBSYSTEM-005"}
Verify SUB-REQ-048: mount Status LED Array on vehicle rear at operational position. Using a calibrated photometer, measure luminous intensity at 11 angular positions spanning 120 degrees (0°, ±12°, ±24°, ±36°, ±48°, ±60°) at a distance of 1m in a darkened environment. Pass criterion: all 11 measurements ≥10 mcd per LED element for each of the three colour states (green, amber, red). Repeat at low battery (battery at 10% SoC) to confirm intensity is maintained.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-006 title="VER-HMISUBSYSTEM-006"}
Verify IFC-REQ-030: using SPI analyser on the MAP–LED Array bus, command a mode transition (Normal→Degraded) and measure elapsed time from transition command issue to first valid LED frame on SPI bus. Pass criterion: ≤20ms latency on 20 consecutive transitions. Confirm the frame carries independently addressable RGB values for minimum 6 elements by inspecting SPI frame structure against protocol specification.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-007 title="VER-HMISUBSYSTEM-007"}
The Carer Override Module rear switch activation latency SHALL be verified at 4 km/h vehicle speed, measuring elapsed time from switch edge to first accepted joystick command via oscilloscope on the CARER_OVERRIDE line and Drive Subsystem CAN bus. Pass: elapsed time ≤100ms across 20 consecutive activations; no single measurement >120ms.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-008 title="VER-HMISUBSYSTEM-008"}
Verify SUB-REQ-068: Inject a BCI accuracy threshold crossing event (accuracy dropping from 75% to 65%) while measuring Audio Alert Module response. Pass if 880Hz tone at >=85 dBSPL at 1m is audible within 200ms of threshold crossing and sustained until mode transition or HMI acknowledgement, on 5 consecutive trials.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-009 title="VER-HMISUBSYSTEM-009"}
Verify SUB-REQ-069: Activate the rear-mounted override switch while the buggy is in BCI navigation mode. Measure time from switch activation to joystick steering authority confirmed active and BCI commands confirmed disabled. Pass if handover confirmed within 100ms and BCI commands produce no motion response, on 10 consecutive trials including 3 during active BCI movement command.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-010 title="VER-HMISUBSYSTEM-010"}
Verify SUB-REQ-073: Inject a BCI accuracy drop to 65% (degraded mode trigger). Observe Status LED Array and Display Unit. Pass if amber 2Hz pulse is visible within 100ms of mode entry and Display Unit shows the specified text string within 100ms, both persisting until mode exit, on 5 consecutive trials.
**Verification:** Demonstration
:::

:::requirement{#VER-HMISUBSYSTEM-011 title="VER-HMISUBSYSTEM-011"}
Verify the Status LED Array displays the correct distinct colour state for each operating mode and completes state transitions within 200ms of the triggering event.
**Verification:** Demonstration
:::

:::requirement{#VER-HMISUBSYSTEM-012 title="VER-HMISUBSYSTEM-012"}
Verify SUB-REQ-052: Activate the rear-mounted override switch and measure elapsed time from switch edge to CARER_OVERRIDE signal assertion at the Safety Monitor Processor input pin, using an oscilloscope trigger on the switch and SMP GPIO. Pass: assertion time ≤ 50ms on all 20 consecutive activations; signal maintained continuously while switch is held. Fail: any measurement > 50ms or signal drop during hold.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-013 title="VER-HMISUBSYSTEM-013"}
The Carer Override Module joystick command latency SHALL be verified with CARER_OVERRIDE asserted by measuring elapsed time from joystick deflection input to CAN frame transmission on the Drive Subsystem bus via logic analyser. Pass: CAN frame transmitted within 20ms on ≥19/20 activations; command rate ≥50Hz sustained over 10 seconds.
**Verification:** Test
:::

:::requirement{#VER-HMISUBSYSTEM-014 title="VER-HMISUBSYSTEM-014"}
Verify IFC-REQ-026: Connect an SPI logic analyser to the Main Application Processor to Display Unit link. (a) Command display to render a full-frame 800x480 16-bit image and measure SPI clock frequency — Pass: 40 MHz ±2%. (b) Render 60 consecutive frames and measure frame rate — Pass: ≥30 fps with no frame drops (chip-select transitions counted). (c) Send a chip-select pulse while bus is idle and confirm display responds without bus contention. Fail: clock frequency out of tolerance, frame rate <30fps, or bus contention detected.
**Verification:** Test
:::

## Perception Subsystem

:::requirement{#VER-PERCEPTIONSUBSYSTEM-001 title="VER-PERCEPTIONSUBSYSTEM-001"}
Verify SUB-REQ-023: place calibrated targets at 0.1m, 0.5m, 1.0m, 1.5m, and 2.0m in the forward arc under lighting conditions from 0 lux (IR illumination only) to 10,000 lux. Confirm distance readings are within 50mm of ground truth at 10Hz sustained over 30 seconds. Pass: all five distances within tolerance at all three lighting levels.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-002 title="VER-PERCEPTIONSUBSYSTEM-002"}
Verify IFC-REQ-023: inject an obstacle scenario via test fixture and capture the SPI bus between the Perception MCU and Safety Monitor Processor with a logic analyser. Confirm 4-byte frame format, 10Hz nominal rate, rolling counter incrementing correctly, and that after two missed frames the Safety Monitor Processor asserts communication fault within 250ms. Pass: all frame format, rate, counter, and fault assertions met in 100 consecutive frames.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-003 title="VER-PERCEPTIONSUBSYSTEM-003"}
Verify SUB-REQ-026: with system running, halt the Perception MCU firmware (power off the MCU or inject a software lockup). Monitor Safety Monitor Processor output. Confirm E-stop sequence is initiated within 150ms of last valid frame. Pass: E-stop asserted within 150ms in 5 consecutive tests from normal operating state.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-004 title="VER-PERCEPTIONSUBSYSTEM-004"}
Verify SUB-REQ-035: Mount vehicle on tilting test rig; rotate to 15.5 degrees at 5 degrees/second and record timestamp when tilt-hazard signal is asserted on Safety Monitor Processor I2C bus. Pass: signal asserted within 50ms of 15-degree threshold crossing for 5 trials in each axis.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-005 title="VER-PERCEPTIONSUBSYSTEM-005"}
Verify SUB-REQ-054: Apply vehicle tilt exceeding 15 degrees on test fixture and monitor tilt data publication rate and content to Safety Monitor Processor. Pass: data rate >= 20 Hz, includes tilt angle, axis identity, and confidence score, maintained for 30 s trial.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-006 title="VER-PERCEPTIONSUBSYSTEM-006"}
Verify the Perception MCU processes all sensor inputs and emits an obstacle alert frame to the Safety Monitor Processor within 50ms of sensor data arrival at full 10Hz sensor update rate.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-007 title="VER-PERCEPTIONSUBSYSTEM-007"}
Verify the Side Proximity Sensor Pair detects objects within 0.5m laterally on each side and delivers a TTL-level alert signal to the Perception MCU within 30ms of intrusion.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-008 title="VER-PERCEPTIONSUBSYSTEM-008"}
Verify SUB-REQ-040: Connect a calibrated power supply to the Inclinometer Tilt Sensor Unit 3.3V input. Measure supply voltage at the sensor under nominal and maximum load conditions. Introduce a ±5% supply ripple at 1kHz and verify sensor output stability. Pass: supply voltage 3.3V ± 3% (3.20V–3.40V) under all load conditions; inclinometer output tilt reading stable ± 0.1° during ripple injection. Fail: voltage outside tolerance or tilt reading drift > 0.1° during ripple.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-009 title="VER-PERCEPTIONSUBSYSTEM-009"}
Verify IFC-REQ-022: Position a 300mm × 300mm flat obstacle at 0.5m, 0.45m, and 0.6m on each lateral side of the vehicle. Pass: (a) alert asserted as TTL HIGH within 30ms of obstacle at 0.5m or 0.45m (≥ 20/20 placements per side); (b) no alert when obstacle at 0.6m (≥ 10/10 placements per side); (c) GPIO line returns LOW within 30ms of obstacle removal. Fail: any missed detection at ≤ 0.5m, any false positive at 0.6m, or delayed deassertion.
**Verification:** Test
:::

:::requirement{#VER-PERCEPTIONSUBSYSTEM-010 title="VER-PERCEPTIONSUBSYSTEM-010"}
Verify IFC-REQ-021: Scan I2C bus at 400 kHz (400 kHz I2C mode per UM10204) and enumerate all three depth sensor addresses. Drive each sensor with a ranging command and verify response timing. Pass: (a) all three sensors enumerate at correct addresses; (b) ranging responses received within 10ms of command; (c) no bus contention (SDA/SCL monitored with logic analyser) across 1000 ranging cycles. Fail: any address absent, latency > 10ms, or SDA collision detected.
**Verification:** Test
:::

## Power Subsystem

:::requirement{#VER-POWERSUBSYSTEM-001 title="VER-POWERSUBSYSTEM-001"}
The Battery Management System thermal fault response SHALL be verified by forcing a cell temperature sensor input above 60°C via calibrated resistance substitution and measuring time to 48V bus de-energisation. Pass: bus de-energised within 200ms and CAN fault code broadcast within 200ms on ≥5/5 test runs.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-002 title="VER-POWERSUBSYSTEM-002"}
The BMS-to-Safety Monitor Processor GPIO fault interface SHALL be verified by injecting a 55°C temperature threshold crossing and measuring: (a) GPIO fault line transitions low within 50ms; (b) transition is independent of CAN bus availability. Pass: both criteria met on ≥5/5 runs with CAN connected and ≥5/5 runs with CAN disconnected.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-003 title="VER-POWERSUBSYSTEM-003"}
Verify SUB-REQ-071: Connect the buggy to both a 230V 50Hz supply and (separately) a 120V 60Hz supply at 20% SoC. Measure time to reach 100% SoC, confirming CC-CV profile is applied and charge terminates automatically. Pass if charge completes within 4 hours on both supply voltages, SoC measurement accuracy ±2%, and trickle maintenance engages on completion.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-004 title="VER-POWERSUBSYSTEM-004"}
Verify the Lithium Iron Phosphate Battery Pack delivers a minimum of 4 hours continuous operation at rated system load: (a) charge pack to 100% SoC at 25°C, (b) discharge at combined rated system load (drive motors at 3 km/h on flat surface, BCI processing active, HMI and Safety Subsystem energised), (c) record elapsed time from start of discharge to first alarm at 10% SoC. Repeat at 40°C ambient. Pass: elapsed time ≥ 4 hours at both 25°C and 40°C. Fail: either condition below 4 hours.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-005 title="VER-POWERSUBSYSTEM-005"}
Verify the DC-DC Converter Array maintains each output rail (12V, 5V, 3.3V) within ±5% of nominal voltage and with output ripple not exceeding 50mV peak-to-peak across 10% to 100% rated load range.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-006 title="VER-POWERSUBSYSTEM-006"}
Verify the Charge Controller charges the battery pack from 20% to 100% state of charge within 3 hours when connected to a 240V AC facility supply.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-007 title="VER-POWERSUBSYSTEM-007"}
Verify the Battery Management System transmits pack voltage, individual cell voltages, SoC, temperature summary, and pack current to the Main Application Processor via CAN 2.0B at 250 kbit/s at 100ms intervals.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-008 title="VER-POWERSUBSYSTEM-008"}
Verify IFC-REQ-002: Connect the vehicle to a 230V 50Hz facility charging dock and measure galvanic isolation resistance between vehicle chassis and mains supply. Pass: (a) isolation resistance ≥ 2MΩ at 500V DC (per IEC 60601-1 measurement method); (b) charge current flows within 60 seconds of dock connection; (c) charge terminates automatically at 100% SoC with charger LED indicating complete. Fail: isolation resistance < 2MΩ, no charging within 60s, or no auto-termination.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-009 title="VER-POWERSUBSYSTEM-009"}
Verify SUB-REQ-036: Connect the charge controller to a 230V 50Hz supply and then a 120V 60Hz supply. Measure input power acceptance and output to battery. Pass: (a) charging initiates within 60s at 230V 50Hz; (b) charging initiates within 60s at 120V 60Hz; (c) no fault codes generated at either input; (d) BMS reports valid charge current at both supply voltages. Fail: failure to accept either supply or fault code generated.
**Verification:** Test
:::

:::requirement{#VER-POWERSUBSYSTEM-010 title="VER-POWERSUBSYSTEM-010"}
Verify SUB-REQ-055: Connect the Power Subsystem to a 230V 50Hz AC supply at 20% SoC via a calibrated current probe on the mains lead. (a) Measure peak inrush current during first 100ms of connection across 5 trials — Pass: peak ≤16 A on all 5 trials. (b) Monitor cell temperature via thermocouple array at 1Hz during full charge cycle — Pass: no cell exceeds 45°C. (c) Time charge completion from 20% to 100% SoC — Pass: completion within 6 hours. Fail: inrush >16 A, cell temperature >45°C, or charge time >6 hours on any trial.
**Verification:** Test
:::

## Safety Subsystem

:::requirement{#VER-SAFETYSUBSYSTEM-001 title="VER-SAFETYSUBSYSTEM-001"}
Verify SUB-REQ-001: Inject a simulated main processor fault (power rail pull-down to 0V) while Safety Monitor Processor is executing safety state machine. Verify via logic analyser that safety state machine continues to execute at 200Hz for ≥5 seconds post-fault, and that relay de-energise command is issued within 500ms of heartbeat loss detection. Pass: no execution gap >6ms in safety loop; relay command issued; all outputs set to safe state. Fail: execution halt, missed heartbeat detection, or relay remains energised.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-002 title="VER-SAFETYSUBSYSTEM-002"}
Verify SUB-REQ-002: Apply de-energise command to Motor Power Isolation Relay under 4 load conditions (0A, 30A, 60A steady, 200A pulse). Measure with oscilloscope the time from control-line falling edge to relay contact open confirmed by feedback contact. Pass: all measurements ≤20ms. Fail: any measurement >20ms, or relay fails to open within 50ms under any load condition.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-003 title="VER-SAFETYSUBSYSTEM-003"}
Verify SUB-REQ-003: Replay 50 CHB-MIT annotated seizure EEG segments through the Seizure Detection Module and measure time-to-detection flag from annotated onset. Also replay 8 hours of normal EEG operation and count false positive seizure flags. Pass: ≥95% of seizure segments detected within 150ms; false positive count ≤1 across full 8-hour replay. Fail: detection latency >150ms for >5% of segments, or false positive count >1.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-004 title="VER-SAFETYSUBSYSTEM-004"}
Verify IFC-REQ-007: Inject a simulated main processor heartbeat halt (hold GPIO low). Measure on oscilloscope from last valid heartbeat to Safety Monitor Processor E-stop output assertion. Separately, verify no data payload is transmitted on the heartbeat line using a protocol analyser. Pass: E-stop asserted within 500ms of last heartbeat; no data payload detected on heartbeat GPIO. Fail: E-stop delayed >500ms or data content detected.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-005 title="VER-SAFETYSUBSYSTEM-005"}
Verify IFC-REQ-008: With relay energised, open (disable) one control channel at a time. Verify relay de-energises within 20ms for each single-channel failure. Also verify relay does not energise when both channels are driven independently by separate fault-injected outputs. Pass: relay opens on either single-channel fault; relay does not energise with mismatched channels. Fail: relay remains closed on single-channel fault.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-006 title="VER-SAFETYSUBSYSTEM-006"}
Verify SYS-REQ-002 end-to-end: In a fully integrated vehicle at maximum speed (6 km/h), inject each of the 5 safety triggers (BCI signal loss, seizure EEG replay, tilt >15°, manual E-stop button, processor heartbeat loss) and measure from trigger event to vehicle coming to rest (wheels stopped). Pass: vehicle comes to rest within 200ms plus brake stopping distance for all 5 triggers. Fail: any trigger results in continued motion beyond 200ms relay actuation time.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-007 title="VER-SAFETYSUBSYSTEM-007"}
The Inclinometer Tilt Sensor Unit SHALL be verified by mounting the buggy on a calibrated tilt table, incrementally inclining from 0° to 20° in 1° steps at 1°/s, and confirming: (a) angle readings within ±0.5° of reference at each step, (b) 100Hz output rate continuous across full range, (c) E-stop command issued by Safety Monitor Processor within 200ms of crossing 15° sustained for 200ms.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-008 title="VER-SAFETYSUBSYSTEM-008"}
The Manual Emergency Stop Button hardwire circuit SHALL be verified by: (a) measuring contact-to-relay-coil de-energisation time using an oscilloscope across 10 actuations with motor at rated load, confirming all measurements ≤5ms, (b) inspecting the wiring schematic to confirm no software-mediated path in the disconnect circuit.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-009 title="VER-SAFETYSUBSYSTEM-009"}
The Safety Subsystem E-stop state machine SHALL be verified by injecting each of the five defined safety triggers (BCI signal loss, seizure flag, tilt threshold, manual E-stop, heartbeat failure) individually and in combination, confirming: relay de-energisation, brake command, and HMI alert all activated within 200ms of trigger, measured from trigger signal assertion to final output using a logic analyser.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-010 title="VER-SAFETYSUBSYSTEM-010"}
The Safety Monitor Processor heartbeat watchdog SHALL be verified by: (a) halting the main application processor and confirming E-stop triggers within 600ms (500ms watchdog timeout + 100ms response margin), (b) injecting heartbeat period jitter at +20% and +21% of nominal and confirming E-stop triggers only at the 21% case, (c) repeating 50 times to confirm deterministic behaviour.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-011 title="VER-SAFETYSUBSYSTEM-011"}
The emergency BLE iBeacon interface SHALL be verified by: (a) triggering each of five E-stop conditions and measuring time from trigger to iBeacon advertisement transmission at facility BLE gateway, confirming < 500ms in 50 consecutive tests; (b) confirming advertisement contains correct device ID, alert type, and GPS coordinates by sniffing the BLE advertisement payload with a protocol analyser.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-012 title="VER-SAFETYSUBSYSTEM-012"}
The SPI interface between Inclinometer Tilt Sensor Unit and Safety Monitor Processor SHALL be verified by: (a) capturing 1000 consecutive SPI transactions at 1MHz with a logic analyser, confirming 6-byte frame format, roll/pitch/timestamp fields, and CRC validity in all frames; (b) injecting a corrupted CRC and confirming the Safety Monitor Processor discards the frame and logs a sensor fault within one SPI cycle (1ms).
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-013 title="VER-SAFETYSUBSYSTEM-013"}
Verify SUB-REQ-043: Inspect electronic assembly against design documentation. Confirm Safety Monitor Processor PCB is mechanically separate from Main Application Processor PCB, has independent connector to DC-DC Converter Array power rail, and has no shared power nets with the Main Application Processor on the backplane.
**Verification:** Inspection
:::

:::requirement{#VER-SAFETYSUBSYSTEM-014 title="VER-SAFETYSUBSYSTEM-014"}
Verify SYS-REQ-020: review EEG headset manufacturer's biological safety evaluation report against ISO 10993-1. Confirm biocompatibility testing covers cytotoxicity (ISO 10993-5), sensitisation (ISO 10993-10), and skin irritation (ISO 10993-23) for all skin-contacting electrode and headset materials. Pass: manufacturer provides valid ISO 10993-1 conformance certificate with no Category I or Category II adverse findings for prolonged skin contact.
**Verification:** Inspection
:::

:::requirement{#VER-SAFETYSUBSYSTEM-015 title="VER-SAFETYSUBSYSTEM-015"}
Verify SUB-REQ-051: Simulate facility emergency system halt command via 2.4 GHz wireless interface and measure elapsed time from signal receipt to motor isolation relay actuation and electromagnetic brake engagement. Pass: both events complete within 150 ms across 20 consecutive trials at operating temperature.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-016 title="VER-SAFETYSUBSYSTEM-016"}
Verify SUB-REQ-057: Halt MAP watchdog refresh and measure elapsed time to SAFE_STOP assertion and non-volatile fault log write. Pass: SAFE_STOP asserted within 200 ms, fault record present in NV storage, across 10 trials.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-017 title="VER-SAFETYSUBSYSTEM-017"}
Verify SUB-REQ-060: Using a test fixture that monitors relay coil continuity, open each NC contact channel independently (channel A, then channel B) and confirm Motor Power Isolation Relay de-energises within 20ms measured from contact open to relay drop-out on oscilloscope. Pass criterion: de-energisation within 20ms for both channels independently, zero relay hold when either channel is open.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-018 title="VER-SAFETYSUBSYSTEM-018"}
Verify SUB-REQ-061: In a controlled test environment, inject a simulated MAP watchdog timeout by halting MAP firmware execution, and verify that the Safety Monitor Processor asserts Emergency Stop state and zeros velocity command within 250ms (200ms watchdog + 50ms propagation) with no dependency on MAP recovery. Pass criterion: velocity = 0 within 250ms across 10 injected fault events, and vehicle does not resume motion without manual reset.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-019 title="VER-SAFETYSUBSYSTEM-019"}
Verify SUB-REQ-063: Review certification documentation from an accredited IEC 61508 assessor covering Safety Subsystem hardware fault tolerance (HFT), systematic capability SC3, and software development process documentation (lifecycle plan, V&V evidence). Pass criterion: certificate issued by a Notified Body or TUV-equivalent confirming SIL-3 suitability and no open safety defects.
**Verification:** Inspection
:::

:::requirement{#VER-SAFETYSUBSYSTEM-020 title="VER-SAFETYSUBSYSTEM-020"}
Verify SUB-REQ-070: Place the buggy on a tilt platform and gradually increase pitch to 12 degrees (confirm pre-warning signal issued to Safety Monitor), then continue to 15 degrees (confirm brake application and motion inhibit). Measure time from 15-degree crossing to brake confirmed. Pass if pre-warning issued at 12±0.5 degrees, brake applied within 50ms of 15-degree crossing, and motion inhibited within 100ms, on 5 trials each axis.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-021 title="VER-SAFETYSUBSYSTEM-021"}
The MAP watchdog failsafe mechanism SHALL be verified by injecting a simulated MAP watchdog timeout (halt firmware refresh) and measuring time from last heartbeat to SAFE_STOP assertion and brake engagement; the total latency SHALL be ≤200ms measured on oscilloscope at the relay coil.
**Verification:** Test
:::

:::requirement{#VER-SAFETYSUBSYSTEM-022 title="VER-SAFETYSUBSYSTEM-022"}
Verify the Safety Subsystem Safety Monitor Processor is developed and validated in accordance with IEC 61508-3 at SIL 3, with documented V-model lifecycle artefacts.
**Verification:** Inspection
:::

:::requirement{#VER-SAFETYSUBSYSTEM-023 title="VER-SAFETYSUBSYSTEM-023"}
Verify the Seizure Detection Module updates the shared memory region on the Safety Monitor Processor at 50Hz with a 1-byte status word and that the Safety Monitor Processor reads this region within 10ms of each update.
**Verification:** Test
:::

## Vehicle Platform

:::requirement{#VER-VEHICLEPLATFORM-001 title="VER-VEHICLEPLATFORM-001"}
Verify SUB-REQ-027: load test the Chassis Frame per ISO 7176-8 static load procedure — apply 150kg distributed load via seat mounting points, hold for 5 minutes, inspect for permanent deformation. Apply 3g vertical impulse via drop test fixture. Pass: zero permanent deformation after static test; no fracture or crack after dynamic test.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-002 title="VER-VEHICLEPLATFORM-002"}
Verify SUB-REQ-049: perform minimum turning radius test per ISO 7176-3 test method. Mark vehicle centre on test floor, execute full 360-degree turning manoeuvre, measure outer wheel track swept radius. Verify navigation through a 900mm doorway simulation with 150mm marker barriers on each side. Pass: swept radius <= 600mm, no contact with barriers in 5 trials.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-003 title="VER-VEHICLEPLATFORM-003"}
The Electronics Bay IP54 ingress protection rating SHALL be verified by performing the IEC 60529 (Degrees of protection provided by enclosures) test method 13.4 dust wire probe and 14.2.4 water splash tests, plus fastener torque and gasket continuity inspection. Pass: no dust ingress at IP5X probe; no functional degradation after water splash; all bay fasteners at specified torque.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-004 title="VER-VEHICLEPLATFORM-004"}
The Chassis Frame SHALL be verified by static load testing per ISO 7176-8: apply 1.5× (maximum user mass 130kg + electronics bay 15kg) = 217.5kg to the seat mounting points and hold for 60 seconds; no permanent deformation >1mm shall be measured at any structural member. Impact test: drop a 7kg mass from 1m height onto each corner of the chassis platform and confirm no fracture or deformation of safety-critical welds.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-005 title="VER-VEHICLEPLATFORM-005"}
Verify the Electronics Bay maintains internal temperature below 70°C for all mounted electronics while dissipating up to 40W at 40°C ambient with vehicle stationary and no forced airflow.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-006 title="VER-VEHICLEPLATFORM-006"}
Verify the Electronics Bay provides IP54 environmental protection per IEC 60529 (Degrees of protection provided by enclosures): (a) expose assembled unit to IP5X dust chamber per IEC 60529 Clause 13.4 for 8 hours; (b) expose to IP54 water spray per IEC 60529 Clause 14.2.4 for 5 minutes at all orientations; (c) open and re-close access panel 100 times; (d) repeat dust and water tests. Pass: no dust ingress visible on internal surfaces after step (a); no water ingress after step (b); IP54 rating maintained after 100 seal cycles in step (d). Fail: any ingress.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-007 title="VER-VEHICLEPLATFORM-007"}
Verify SUB-REQ-074: Inspect the chassis frame material certificate and dimensional report. Pass: (a) material certificate confirms aluminium alloy 6061-T6 or equivalent per ISO 209 (Wrought aluminium and aluminium alloys); (b) cross-section dimensional report confirms wall thickness ≥ 3mm at all primary load-bearing members; (c) static load test to 1.5× rated occupant load (180kg) with no permanent deformation > 1mm. Fail: any non-conforming material, wall thickness < 3mm, or deformation > 1mm.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-008 title="VER-VEHICLEPLATFORM-008"}
Verify SUB-REQ-072: Connect a service laptop to the Electronics Bay USB-C 3.1 Gen 1 port and execute the automated diagnostic test suite. Pass: (a) enumeration completes within 10 seconds of cable connection; (b) diagnostic API returns responses for all five test categories (motor, brake, sensor, battery, BCI pipeline) within 20 minutes; (c) test suite returns PASS/FAIL result for each subsystem with numeric values and fault codes. Fail: any test category fails to return result or API returns null.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-009 title="VER-VEHICLEPLATFORM-009"}
The Electronics Bay IP54, thermal, and access-cycle durability SHALL be verified by: (a) IEC 60529 dust probe and 10-minute water spray test — Pass: no wire penetration, no functional degradation; (b) 4-hour operation at 55°C ambient at maximum component load — Pass: processor inlet temperature ≤55°C, no thermal shutdown; (c) 100 maintenance access cycles followed by re-test of (a) — Pass: IP54 rating maintained.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-010 title="VER-VEHICLEPLATFORM-010"}
Verify SUB-REQ-029: Using a calibrated load test rig with adjustable torso mannequins at the extremes (40 kg/1400mm stature and 120 kg/1900mm stature): (a) Confirm backrest adjusts ≥15 degrees of recline at both occupant configurations. (b) Apply 3g forward deceleration pulse via sled test and measure lap belt restraint force — Pass: belt remains engaged, forward displacement of 50th-percentile mannequin torso ≤50mm from seated position. (c) Confirm 4-point harness buckle engages audibly and cannot be released by a single-finger pull force <20N. Fail: range below 15°, torso displacement >50mm, or buckle release <20N.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-011 title="VER-VEHICLEPLATFORM-011"}
The Electronics Bay chassis mounting interface SHALL be verified by: (a) axial pull-out test at 50N/s ramp on four M6 fasteners with self-locking nuts and rubber isolators — Pass: no fastener pulls out below 500N, no isolator de-bonds below 200N; (b) 3g 11ms half-sine shock test — Pass: no isolator failure, no fastener torque loss >10%.
**Verification:** Test
:::

:::requirement{#VER-VEHICLEPLATFORM-012 title="VER-VEHICLEPLATFORM-012"}
Verify IFC-REQ-031: (a) Test quick-release mechanism on all four wheels using a calibrated force gauge — apply force in the release direction, measure peak force for removal. Pass: all wheels release below 15N. (b) Partially engage the mechanism by stopping at mid-travel and apply 200N axial load — Pass: mechanical interlock prevents partial engagement; wheel fully locks or fully releases, no intermediate state. (c) Install all four wheels and verify positive engagement (audible click, no rotational play under 50N lateral load). Fail: release force >15N, partial engagement holds under load, or wheel rotates under lateral load.
**Verification:** Test
:::

---
