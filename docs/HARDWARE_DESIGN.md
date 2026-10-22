# CORE-328PB Hardware Design Notes

Revision A · Schematic dated 2026-10-21 · Altium Designer

This document describes each functional block of the CORE-328PB and records the findings of a documentation review. It is derived from the Rev A schematic and bill of materials.

## Contents

1. [Power Architecture](#1-power-architecture)
2. [MCU Core](#2-mcu-core)
3. [USB-UART Bridge](#3-usb-uart-bridge)
4. [Indicators and User Interface](#4-indicators-and-user-interface)
5. [I/O Breakout](#5-io-breakout)
6. [Mechanical and Assembly](#6-mechanical-and-assembly)
7. [Design Review Findings](#design-review-findings)

---

## 1. Power Architecture

| Reference | Part | Role |
|:--|:--|:--|
| J1 | USB micro-B, through-hole | USB bus power (`VUSB`) and data |
| D1 | Schottky, 0.5 A / 20 V | Couples `VUSB` onto the +5 rail and blocks reverse flow |
| C1 | 4.7 uF polymer tantalum, 63 V | Bulk capacitance on `VUSB` |
| C2 | 1 uF X5R | `VUSB` decoupling |
| U3 | LM1117IMPX-5.0, SOT-223 | 5 V LDO from the `VCC` pad |
| C3 | 1 uF X5R | Regulator output capacitor |
| R1, LED1 | 1 k, green | Power-on indicator |

### Source behaviour

- **USB only:** the +5 rail equals `VUSB` minus the forward drop of D1. The MCU and FT232RL operate from this rail.
- **`VCC` only:** U3 regulates the input to 5 V. D1 is reverse-biased by the higher rail voltage and the USB side is isolated.
- **Both present:** the regulated rail sits above the USB-derived rail, so U3 is expected to carry the load while D1 stays reverse-biased. Verify this behaviour during bring-up.

### Thermal consideration

An LDO dissipates `P = (VIN - 5 V) x ILOAD`. At higher input voltages or loads drawn from the `+5V` pad, SOT-223 power dissipation limits apply. Check the LM1117 datasheet for dropout, input voltage limits, and thermal resistance for the chosen copper area.

## 2. MCU Core

| Reference | Part | Role |
|:--|:--|:--|
| U1 | ATmega328PB-MUR | 8-bit AVR, 32 KB Flash, VFQFN-32 |
| X1 | 16 MHz SMT crystal (Aker C5S) | Main clock |
| C9, C11 | 18 pF C0G | Crystal load capacitors |
| C4, C5 | 1 uF X5R | `VCC` / `AVCC` decoupling |
| C8 | 100 nF X5R | `AREF` decoupling |
| R8 | 1 k | `RESET` pull-up |
| SW1 | Tactile switch | Manual reset |

At 5 V supply the ATmega328PB supports operation up to 20 MHz, so the 16 MHz crystal is within the specified frequency-versus-voltage envelope.

### Reset network

```text
 +5 ──[R8 1k]──┬────── RESET (U1 pin 29)
               │
   FT232RL     ├──[SW1]── GND
   DTR# ──[C7 100 nF]──┘
```

A falling edge on `DTR#` is AC-coupled through C7 to produce a short reset pulse, which triggers the bootloader on host connect.

## 3. USB-UART Bridge

| Reference | Part | Role |
|:--|:--|:--|
| U2 | FT232RL, SSOP-28 | USB to UART bridge |
| R6, R7 | 1 k | Series resistors on `RX` / `TX` lines |
| C7 | 100 nF | DTR auto-reset coupling |
| C10 | 100 nF | `3V3OUT` decoupling |

- The FT232RL is powered from the +5 rail and `VCCIO` is tied to +5, so UART signalling is at **5 V levels**.
- USB `D+` / `D-` connect directly to J1 pins 3 and 2.
- The series resistors limit contention current if an external device drives `D0` / `D1` while the bridge is active.

## 4. Indicators and User Interface

| Reference | Colour | Driven By | Purpose |
|:--|:--|:--|:--|
| LED1 | Green | +5 rail via R1 | Power present |
| LED2 | Orange | `D13` (PB5) via R5 | User LED |
| LED3 | Red | FT232RL `CBUS0` via R3 | USB-UART activity |
| LED4 | Green | FT232RL `CBUS1` via R4 | USB-UART activity |

The function of the `CBUS` pins is determined by the FT232RL internal EEPROM configuration; confirm the programmed function with FTDI configuration tooling if the LED behaviour differs from expectations.

## 5. I/O Breakout

26 castellated pads expose 14 digital pins, 8 analog inputs, `AREF`, `+5V`, `VCC`, and `GND`. See [`PINOUT.md`](PINOUT.md) for the complete mapping.

## 6. Mechanical and Assembly

- Components are placed on **both sides**: MCU, crystal, switch, status LEDs, and USB connector on top; FT232RL, regulator, and tantalum capacitor on the bottom.
- Rounded-corner outline with two diagonal mounting holes.
- Castellated pads on three edges; the USB connector edge is free of pads.

---

## Design Review Findings

Severity: **High** = fix before ordering, **Medium** = fix or verify before Rev B, **Low** = observation.

| # | Severity | Finding | Suggested Resolution |
|:-:|:-:|:--|:--|
| 1 | High | **BOM library error.** `C7`, `C8`, `C10` are listed as 100 nF, but the description and MPN (`CC0603MRX5R8BB105`) correspond to a **1 uF** part. | Assign a correct 100 nF 0603 X5R part (e.g., 16-25 V) in the library and regenerate the BOM. |
| 2 | Medium | **RESET is not routed to a castellated pad or test point.** ISP of a blank ATmega328PB needs access to RESET. | Add a RESET pad or test point in Rev B; for Rev A, program via the `SW1` pad or `U1` pin 29 using a fixture. |
| 3 | Medium | **Reset pull-up is 1 kOhm** (`R8`). A 10 kOhm value is more common and reduces switch-press current (about 5 mA at 5 V). The R8 / C7 time constant also sets the auto-reset pulse. | Confirm the reset pulse width against the MCU minimum reset pulse requirement during bring-up. Evaluate 10 kOhm. |
| 4 | Low | **PE0 / PE1 (SDA1 / SCL1) are no-connect**; second I2C, SPI1, USART1, and TC3 / TC4 resources are not accessible. | Consider exposing additional peripherals in Rev B if pad budget allows. |
| 5 | Low | **LDO reverse-current behaviour** when the +5 rail is powered from USB while `VCC` is unpowered is not characterised. | Check the LM1117 datasheet protection recommendations; measure current on `VCC` during bring-up. |
| 6 | Low | **Native Altium sources are not in the repository.** | Publish `.PrjPcb` / `.SchDoc` / `.PcbDoc` and fabrication outputs with the first validated release. |
