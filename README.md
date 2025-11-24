# Harlech XL PG2.0 User Guide

**Product:** Harlech XL LED Control Board
**Revision:** PG2.0
**Manufacturer:** TerrainTronics

---

## Overview

Harlech XL PG2.0 is a multi-channel LED control and power distribution board designed for tabletop terrain, architectural miniature lighting, and modular scenery systems. It provides precise control over up to 36 lighting channels, allowing for both decorative low-current LEDs and higher-power lighting modules to be managed from a single controller via I2C.

The board separates logic control power from LED power, ensuring stable communication and minimizing electrical noise when driving high-current loads.

---

## Key Features

* 36 total controllable outputs
* I2C control via Qwiic interface
* Dual-rail power design (3.3V logic / 5V LED power)
* Integrated AW21036 LED driver
* Four high-power load-switched outputs
* Stackable I2C addressing via solder jumper selection
* Clearly numbered LED outputs (1–36) for simple software mapping

---

## Host Controller Requirement

Harlech XL PG2.0 is a controlled peripheral board and does not generate lighting behaviour on its own. It requires a host microcontroller to send commands over I2C to define brightness levels, animations, and lighting effects.

### What acts as the host?

A compatible controller can be any microcontroller or development board capable of I2C communication, such as:

* Arduino (Uno, Nano, Pro Mini, etc.)
* Wemos D1 Mini / ESP8266
* ESP32-based boards
* Raspberry Pi or other embedded controllers

The host device:

* Supplies command data to the AW21036 driver
* Determines LED brightness, patterns, and timing
* Can be programmed to create dynamic lighting effects such as flicker, pulsing, or scene changes

Without a host microcontroller, the Harlech XL board will power up but will not actively drive lighting sequences.

---

## Code Examples & Reference Implementations

Example firmware and reference sketches are provided in the Harlech XL GitHub repository. These examples demonstrate how to:

* Communicate with the AW21036 over I2C
* Set individual LED brightness levels
* Create animation patterns and lighting effects
* Control the high-power load-switched outputs
* Integrate Harlech XL with common controllers such as Arduino and Wemos D1 Mini

These code samples are intended as practical starting points and can be adapted directly into your own projects to accelerate setup and experimentation.

---

## Power Architecture

Harlech XL PG2.0 uses two independent power domains:

### 1. Logic Power (3.3V via Qwiic)

The board is designed to receive its primary 3.3V logic supply from the Qwiic (I2C) interface. This rail powers:

* AW21036 control logic
* I2C communication circuitry
* Address selection logic

A small onboard 3.3V regulator is present as a backup measure but is not intended as the primary power source.

### 2. LED Power (5V Rail)

A dedicated 5V rail supplies power to all LED outputs and load-switched channels. This separation prevents high-current LED noise from interfering with I2C communications or control logic.

⚠️ Always ensure both power domains are correctly supplied for full operation.

---

## How Harlech XL Drives LEDs (Common Anode Explained)

The Harlech XL uses the AW21036 LED driver, which operates in a **common anode configuration** and controls brightness by varying the **cathode** side of each LED channel. This is an important concept for correct wiring and expectations.

### Simple explanation (for hobbyists)

Think of each LED as having two sides:

* One side always connects to the **+5V LED power rail** (this is the common anode)
* The other side is controlled by the board (this is the cathode)

The Harlech XL does not push power into the LED to turn it on. Instead, it turns the LED "on" by providing a path to ground through the cathode. The brighter the LED, the more the board allows current to flow through that path.

So in practical terms:

* All LED positives (+) are tied to 5V
* The board controls the lighting effect by adjusting the negative (-) side

This is normal behavior and is exactly how professional LED matrices and architectural lighting systems are designed.

### Technical explanation (for engineers)

The AW21036 is a constant-current driver using open-drain outputs configured for common-anode LED arrays. Each channel modulates LED intensity by sinking current through the cathode using PWM-controlled current regulation. The anode of each LED is tied to the shared 5V supply rail, while channel control occurs via dynamic low-side switching.

This architecture:

* Reduces EMI and supply noise
* Simplifies high-density LED routing
* Enables precise per-channel brightness control
* Supports scalable LED matrices

Understanding this behavior is critical when integrating external LED modules or when debugging perceived non-functioning channels where incorrect polarity has been applied.

---

## Output Channels

Harlech XL provides 36 outputs divided into two functional groups:

### Standard LED Outputs (1–32)

* Controlled directly by the AW21036 LED driver
* Designed for low to moderate current LEDs
* Ideal for windows, torches, ambient lighting, and effects lighting

### High-Power Switched Outputs (33–36)

* Routed through dedicated onboard load switches
* Intended for:

  * High-power LED banks
  * External lighting boards
  * Auxiliary power distribution modules

These outputs function as switched power channels rather than direct current-regulated LED drivers.

---

## Output Grouping

Outputs are organized into labelled banks and headers for easier wiring:

* OUTPUT1 – LED1–LED8
* OUTPUT2 – LED9–LED16
* OUTPUT3 – LED17–LED24
* OUTPUT4 – LED25–LED32
* HIGH-POWER OUTPUTS – LED33–LED36

Each LED is numbered on the silkscreen to match its corresponding software channel.

---

## I2C Address Configuration

Harlech XL uses a selectable I2C address defined by solder jumpers SJ1, SJ2, and SJ3. Refer to the AW21036 datasheet for address configuration details. This allows multiple boards to be used on the same I2C bus without conflicts.

Refer to the silkscreen markings for address configuration options before installation.

---

## Board Layout & Physical Reference

This section corresponds to the Harlech XL PG2.0 top-layer layout provided and is intended to help users visually identify major functional areas on the PCB.

### Major Layout Zones

When viewing the board from the top:

* **Upper Connector Edge** – Qwiic / I2C logic interface and logic power input zone
* **Central Area** – AW21036 LED driver and supporting control circuitry
* **Lower Output Region** – OUTPUT1–OUTPUT4 headers feeding LED1–LED32
* **High-Power Zone** – Area containing the four load-switch channels for LED33–LED36

### Identifying Key Elements

* **SJ1 / SJ2 / SJ3** – I2C address selection solder jumpers
* **OUTPUT1–OUTPUT4 Headers** – Standard LED outputs (LED1–LED32)
* **High-Power Output Header** – Load-switched channels (LED33–LED36)
* **5V Input Header** – Main LED power rail input
* **Qwiic Connector** – I2C communication and 3.3V logic power source

### Orientation Tips

* LED channel numbers printed on the silkscreen correspond directly to software channel numbering.
* The board is best oriented so the output headers appear along the lower edge for neat wiring when used inside terrain structures or buildings.

> Note: For final documentation, an annotated version of the PCB layout with callouts is strongly recommended for clarity.

---

## Connector & Pinout Reference

This table provides a practical reference for all user-accessible connectors on the Harlech XL PG2.0 board. Labels correspond to the silkscreen as shown on the PCB layout.

### Qwiic / I2C Connector

| Pin | Signal | Description                                      |
| --- | ------ | ------------------------------------------------ |
| 1   | GND    | Ground reference for logic and I2C communication |
| 2   | 3.3V   | Logic power input (primary logic supply)         |
| 3   | SDA    | I2C Data line                                    |
| 4   | SCL    | I2C Clock line                                   |

This connector supplies both control signals and the primary 3.3V logic power for the board.

### 5V LED Power Input

| Pin | Signal | Description                                 |
| --- | ------ | ------------------------------------------- |
| +   | +5V    | LED power rail input (high current capable) |
| -   | GND    | Ground return for LED power                 |

This connector powers all LED output channels and high-power switched outputs.

### Standard LED Output Headers (OUTPUT1–OUTPUT4)

Each header provides eight LED cathode-controlled outputs. LED positives should be connected to +5V.

| Header  | LED Channels  |
| ------- | ------------- |
| OUTPUT1 | LED1 – LED8   |
| OUTPUT2 | LED9 – LED16  |
| OUTPUT3 | LED17 – LED24 |
| OUTPUT4 | LED25 – LED32 |

Pin order follows numerical silk labels printed beside each connector column.

### High-Power Output Header

These outputs control onboard load switches for higher-current devices.

| Label | Channel | Function                   |
| ----- | ------- | -------------------------- |
| HP1   | LED33   | Load-switched power output |
| HP2   | LED34   | Load-switched power output |
| HP3   | LED35   | Load-switched power output |
| HP4   | LED36   | Load-switched power output |

### I2C Address Jumpers

| Jumper | Function               |
| ------ | ---------------------- |
| SJ1    | I2C Address bit select |
| SJ2    | I2C Address bit select |
| SJ3    | I2C Address bit select |

These define the I2C address according to AW21036 addressing rules.

---

## Quick Start

1. Connect 3.3V logic power via the Qwiic I2C cable.
2. Apply 5V to the LED power input.
3. Connect LEDs or external devices to the output headers.
4. Confirm I2C address selection if using multiple boards.
5. Power on the system and verify communication from your controller.

---

## Safety Notes

* Do not exceed the current rating of any output channel.
* High-power outputs should only be used with properly rated external devices.
* Always power down the system before connecting or disconnecting LEDs.
* Confirm both power rails are present before troubleshooting control failures.

---

## Troubleshooting

| Issue                            | Possible Cause           | Solution                           |
| -------------------------------- | ------------------------ | ---------------------------------- |
| LEDs not responding              | Missing 3.3V logic power | Verify Qwiic power source          |
| Some LEDs inactive               | Incorrect output mapping | Check silkscreen numbering         |
| High-power outputs not switching | Load switch wiring issue | Verify external device connections |
| Flickering lights                | Insufficient 5V supply   | Use regulated power source         |

---

## What's New in PG2.0

* Corrected enable behavior on high-power outputs 33–36
* Updated silkscreen with clearer LED numbering
* Revised 5V header spacing (2.54mm)
* Added optional 3.3V regulator for resilience

---

## Technical Specifications

| Parameter          | Value            |
| ------------------ | ---------------- |
| Logic Voltage      | 3.3V (via Qwiic) |
| LED Supply Voltage | 5V               |
| LED Outputs        | 36               |
| High-Power Outputs | 4                |
| Communication      | I2C              |
| Driver IC          | AW21036          |

---

## Intended Use Cases

* Castle lighting and terrain boards
* Modular tabletop environments
* Architectural miniature lighting
* Interactive diorama systems

---

### Notes Section

(For user-specific wiring or configuration notes)

---

*This document is an initial draft. Feel free to mark up sections for expansion, refinement, or stylistic adjustment.*
