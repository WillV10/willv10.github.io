---
layout: project
title: "UAV Ground Control Station and Telemetry protocol"
description: "PyQt6 GCS with custom telemetry protocol"
hero: /assets/images/projects/gcs-screenshot.png
order: 3
tags: [PyQt6, Telemetry, UAV]
---

# Custom PyQt6 Ground Control Station (GCS)

## Overview

This project is a **custom Ground Control Station (GCS)** developed in **Python using PyQt6** to support fixed-wing UAV development, flight-test instrumentation, and control-law evaluation.  
The system is designed to be **lightweight, deterministic, and extensible**, with a focus on real-time telemetry visualization and reliable command and control.



![UAV Design](/assets/images/projects/gcs-screenshot.png)

![GCS Screenshot](/assets/images/projects/IMG_0639.jpeg)

---

## GCS Architecture

The GCS uses a **modular, signal-based architecture**:

- **Transport Layer**
  - Serial and UDP telemetry support (serial for flight test using RFD900 radios, UDP for desktop simulation and testing)
  - Single packet protocol shared across all links
- **Telemetry Pipeline**
  - Binary packets parsed using JSON-defined schemas
  - Telemetry distributed internally via Qt signals
- **Visualization**
  - Primary flight data (attitude, rates, airspeed)
  - Time-history plots and numerical readouts
  - Offline-capable moving map
- **Command & Control**
  - Deterministic command packets with acknowledgment tracking
  - Retry logic and safety gating based on live telemetry
- **Status & Diagnostics**
  - Packet rate monitoring
  - Dropped packet detection
  - Human-readable command and ACK feedback

---

# Custom Telemetry & Command Protocol

## Design Philosophy

The communication protocol is a **compact binary format** designed for:
- Low-bandwidth links
- High-rate flight dynamics data
- Explicit unit handling

All packet layouts are defined in **JSON schema files**, enabling consistent parsing across the flight controller and GCS.

---

## Common Packet Structure

All packets share the same basic layout:

- **Header**
  - Start byte
  - Packet type
  - Sequence ID
  - Payload length
- **Payload**
  - Structured data fields
- **Footer**
  - Checksum
  - End byte

Sequence IDs and checksums provide packet tracking and integrity checking.

---

## Telemetry Packets

### High-Frequency Telemetry

Used for **flight dynamics and control analysis**, including:
- Attitude and angular rates
- Linear accelerations
- Airspeed
- Pilot inputs
- Control surface commands

### Low-Frequency Telemetry

Used for **navigation and system health**, including:
- GPS position and fix status
- Battery voltage and current
- Vehicle mode
- Local position and velocity
- Environmental data

---

## Command & Acknowledgment Packets

### Command Packets

Commands are sent from the GCS to the flight controller to:
- Arm or disarm flight-test modes
- Inject test signals
- Trigger safety or fault scenarios

### Acknowledgment Packets

Each command is explicitly acknowledged by the flight controller:
- ACK packets reference the original command sequence ID
- Status codes indicate success or failure
- Automatic retry on missed acknowledgments

