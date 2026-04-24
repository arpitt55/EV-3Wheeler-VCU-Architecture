# Software-Defined Vehicle Control Unit (VCU) for 3-Wheeled EVs 🛺⚡

![Status](https://img.shields.io/badge/Status-Completed-success)
![Focus](https://img.shields.io/badge/Focus-Control_Systems_%7C_Vehicle_Dynamics-blue)
![Safety](https://img.shields.io/badge/Standard-ISO_26262_ASIL--D-red)

## Overview
This repository contains the comprehensive system architecture, control logic, and functional safety documentation for an advanced Vehicle Control Unit (VCU). Designed specifically for commercial Delta-footprint (1F2R) three-wheeled electric vehicles, this system replaces the traditional mechanical differential with an **Active Electronic Differential System (EDS)** utilizing independent rear hub motors.

Developed as a deep-level control concept for the **Bajaj Campus Challenge 2026**.

## The Engineering Challenge
The electrification of commercial auto-rickshaws through independent rear motors introduces profound dynamic challenges. The removal of the mechanical rear axle eliminates natural torque equalization, transferring the entire burden of longitudinal traction, lateral stability, and yaw manipulation to the software. 

The VCU must guarantee stable, predictable behavior under extreme commercial constraints:
* **Radical Mass Variation:** Operating from an unloaded 400 kg to a fully loaded 900 kg.
* **Inherent Instability:** Mitigating rollover risks associated with a high center of gravity and triangular footprint.
* **Complex Terrain:** Maintaining traction and driver intent across $\mu$-split surfaces, sharp cornering, and steep road gradients.

## Core System Architecture
The system utilizes a Hierarchical Control Architecture, bridging driver intent, vehicle dynamics estimation, and distributed motor actuation without relying on cost-prohibitive sensors. 

### 1. State Estimation (UKF + RLS)
* **Unscented Kalman Filter (UKF):** Tracks non-linear vehicle states and rejects sensor bias, utilizing the un-driven front wheel as a true velocity reference. 
* **Recursive Least Squares (RLS):** Provides robust, real-time vehicle mass estimation to handle payload fluctuations.
* *Note on Signal Flow:* In the architecture block diagrams, the road angle ($\theta_{road}$) estimation originates from the UKF block and points back to the Gradient Handler to dynamically compensate for incline-induced load transfers.

### 2. Upper Controller: Yaw & Stability Management 
* **Sliding Mode Controller (SMC):** Actively manages yaw dynamics. Utilizes a "Switching Gain" tuned for maximum inertia, ensuring robust turn-in response and eliminating understeer without requiring adaptive tuning for payload changes.
* **Predictive Lateral Load Transfer Ratio (PLTR):** Continuously calculates impending rollover risks. If the LTR exceeds the critical 0.8 threshold (indicating inner-wheel lift), the SMC executes a massive negative braking torque on the outer wheel to realign the chassis.

### 3. Torque Allocator (Electronic Differential)
Coordinates left and right motor torque to meet driver longitudinal demand while executing regenerative torque vectoring to stabilize the turn and recover kinetic energy.

## Functional Safety & Failsafes (ISO 26262)
Given the potential for catastrophic system collapse (e.g., VCU freezing at maximum speed on a downgrade), the architecture incorporates rigorous hardware-level redundancies:
* **Brake Override System (BOS):** Resolves pedal plausibility conflicts by dropping accelerator commands when brake pressure crosses safety thresholds.
* **Independent Watchdog & E-Stop:** An independent System Basis Chip (SBC) monitors the primary VCU heartbeat.
* **Thermal-Safe Active Short Circuit (ASC):** A hardware multivibrator applies a fixed, duty-cycled PWM to ASC pins to shed kinetic energy without overheating the inverters during a total software failure.

## Repository Structure

📂 **Stage-1-Concept**
* `electronic_differential_stage1.pdf`: Initial stability control concept and problem definition.

📂 **Stage-2-Advanced-Architecture**
* `Analog_Arpitpatel_2.pdf`: Deep-level VCU block diagrams, torque allocation laws, and condition coverage.

📂 **System-Design-and-Implementation**
* `Advanced Vehicle Control System Design.pdf`: Component-level implementation and VESC/CAN bus communication strategies.
* `Vehicle Control for 3-Wheeler.pdf`: Hierarchical control breakdown and sensor suite definitions.

📂 **Research-and-Feasibility-Studies**
* `Mechanical vs. Electronic Differentials.pdf`: Economic and kinematic comparison of drivetrains.
* `Systemic Vulnerabilities of Mechanical Differentials...pdf`: Analysis of friction scrub, drivetrain wear, and EDS efficiency gains.

📂 **Functional-Safety-and-CDR**
* `VCU Architecture CDR Report.pdf`: Critical design review addressing mathematical vulnerabilities and control constraints.
* `EV Failsafe for Total System Collapse.pdf`: ISO 26262 ASIL-D hardware topology analysis for terminal failure states.

---
*Disclaimer: This repository contains conceptual engineering documents and architectural designs intended for academic and competitive evaluation.*
