# Vettic Sentry: Hardware-Native Green Box Implementation

**Status:** Tier 1 Research Prototype
**Protocol Alignment:** Green Box Protocol v1.1

## 📋 Executive Summary
Vettic Sentry is a hardware-enforced containment architecture for autonomous agents. It addresses the "Execution Without Governance" risk by moving the "Watchdog" layer out of user-space software and into a dedicated physical microcontroller.

In this architecture, the AI Agent has **zero** perception or execution authority until verified physical laws (thermal/humidity thresholds) authorize a "breach" state.

## 🏗 Architecture Mapping

This project implements the **Green Box Protocol** using a physical "Air Gap" strategy:

### 1. The Box (Execution Isolation)
* **Protocol Requirement:** "Dedicated hardware... No privileged containers."
* **Vettic Implementation:**
    * **Host:** NVIDIA Jetson Orin Nano (Physically air-gapped from corporate LAN).
    * **State:** The "Visual Cortex" (Camera/Vision Pipeline) remains powered down by default.

### 2. The Watchdog (Automated Circuit Breaker)
* **Protocol Requirement:** "A separate process monitors agent behavior... abnormal resource consumption triggers immediate termination."
* **Vettic Implementation:**
    * **Hardware:** ESP32 Microcontroller (The "Nervous System").
    * **Mechanism:** The Watchdog is not a software daemon; it is a separate physical device. It maintains a hard "Kill Switch" on the Agent's sensory inputs via USB Serial control.
    * **Logic:** The Agent cannot "see" or "think" until the Watchdog physically authorizes the data stream.

### 3. The Airlock (Input Sanitization)
* **Protocol Requirement:** "All inputs are scanned... Flagged inputs are quarantined."
* **Vettic Implementation:**
    * **Filter:** Physics.
    * **Sanitization:** Instead of scanning prompt text, the system sanitizes *reality*. The Agent is only presented with visual data when specific physical thresholds (e.g., Humidity > 80% representing breath/presence) are met.

## ⚡ Deployment & Logic Flow

**1. The "Cage" State (Idle)**
The Jetson (Brain) is running a specialized listener (`vettic_main.py`). It has no access to the camera or the Gemini API. It is effectively "blind."

**2. The Trigger (Physical Breach)**
The ESP32 (Watchdog) monitors environmental sensors (DHT11).
* `IF Humidity > 80%`: The Watchdog asserts a **Hardware Interrupt**.

**3. The Execution (Agent Activation)**
Only upon receiving the hardware key does the Jetson:
1.  Wake the Visual Cortex (Warm-up routine for exposure stability).
2.  Capture the scene.
3.  Upload to **Gemini 2.0 Flash** for threat assessment.
4.  Log the event to the local immutable ledger.

## 📊 Telemetry
* **Heartbeat:** Bi-directional Serial Handshake (`0x41` / 'A') ensures Watchdog and Box are synced.
* **Logs:** All activation triggers ("Breaches") are permanently recorded with sensor context (Temp/Hum).

## 🛡 Security Note
This is a **Tier 1 (Research)** deployment intended for internal validation. It is designed to demonstrate that **Physical Governance** can be more reliable than Software Governance for high-risk autonomous agents.

---
*Based on the Green Box Protocol v1.1 by Michael Lee / asASource.*
