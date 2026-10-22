# CORE-328PB Pinout

Pad-to-MCU mapping derived from the Rev A schematic. MCU pin numbers refer to the **ATmega328PB-MUR (VFQFN-32)** package.

## Castellated Pad Map

### Power and Reference

| Pad | Net | Description |
|:--|:--|:--|
| `VCC` | VCC | Unregulated input to the LM1117-5.0 regulator (U3) |
| `+5V` | +5 | Regulated 5 V rail, shared with the USB-derived supply |
| `GND` | GND | Ground |
| `AREF` | AREF | ADC reference, MCU pin 20, with 100 nF to GND (C8) |

### Digital I/O

| Pad | MCU Port | MCU Pin | Primary / Alternate Functions |
|:--|:--|:-:|:--|
| `D0/RX` | PD0 | 30 | USART0 RXD, via 1 k series resistor (R6) to FT232RL |
| `D1/TX` | PD1 | 31 | USART0 TXD, via 1 k series resistor (R7) to FT232RL |
| `D2` | PD2 | 32 | INT0, OC3B / OC4B |
| `D3` | PD3 | 1 | INT1, OC2B |
| `D4` | PD4 | 2 | XCK0, T0 |
| `D5` | PD5 | 9 | OC0B, T1 |
| `D6` | PD6 | 10 | OC0A, AIN0 |
| `D7` | PD7 | 11 | AIN1 |
| `D8` | PB0 | 12 | ICP1, CLKO |
| `D9` | PB1 | 13 | OC1A |
| `D10` | PB2 | 14 | SS0, OC1B |
| `D11/MOSI` | PB3 | 15 | MOSI0, OC2A |
| `D12/MISO` | PB4 | 16 | MISO0 |
| `D13/SCK` | PB5 | 17 | SCK0, on-board LED2 (orange) via 1 k (R5) |

### Analog Inputs

| Pad | MCU Port | MCU Pin | Primary / Alternate Functions |
|:--|:--|:-:|:--|
| `A0` | PC0 | 23 | ADC0 |
| `A1` | PC1 | 24 | ADC1 |
| `A2` | PC2 | 25 | ADC2 |
| `A3` | PC3 | 26 | ADC3 |
| `A4` | PC4 | 27 | ADC4, SDA0 (I2C) |
| `A5` | PC5 | 28 | ADC5, SCL0 (I2C) |
| `A6` | PE2 | 19 | ADC6 |
| `A7` | PE3 | 22 | ADC7 |

> [!NOTE]
> `A6` and `A7` are **analog-only** inputs on the ATmega328PB (ADC6 / ADC7); they cannot be used as digital I/O.

## Physical Pad Order (Top View)

| Edge | Order |
|:--|:--|
| Left (top to bottom) | `VCC`, `+5V`, `GND`, `D0/RX`, `D1/TX`, `D2`, `D3`, `D4` |
| Bottom (left to right) | `D5`, `D6`, `D7`, `D8`, `D9`, `D10`, `D11/MOSI`, `D12/MISO`, `D13/SCK` |
| Right (top to bottom) | `A5`, `A4`, `A3`, `A2`, `A1`, `A0`, `A7`, `AREF`, `A6` |

## Internal Connections (Not on Pads)

| Function | MCU Pin | Connection |
|:--|:-:|:--|
| `RESET` (PC6) | 29 | 1 k pull-up to +5 (R8), tactile switch SW1 to GND, 100 nF to FT232RL `DTR#` (C7) |
| `XTAL1` / `XTAL2` (PB6 / PB7) | 7 / 8 | 16 MHz crystal X1 with 18 pF load capacitors C9 / C11 |
| `PE0` / `PE1` (SDA1 / SCL1) | 3 / 6 | Marked no-connect, not routed to pads |
| `AVCC` / `VCC` | 18 / 4 | +5 rail, decoupled by C4 and C5 (1 uF) |
| `GND` | 5 / 21 | Ground |

## Serial Link Wiring

| FT232RL Pin | Signal | Connected To |
|:--|:--|:--|
| 1 `TXD` | UART TX from host | R6 (1 k) to MCU `PD0` (RXD0) |
| 5 `RXD` | UART RX to host | R7 (1 k) to MCU `PD1` (TXD0) |
| 2 `DTR#` | Auto-reset | C7 (100 nF) to MCU `RESET` |
| 23 `CBUS0` | Indicator | R3 (1 k) to LED3 (red) |
| 22 `CBUS1` | Indicator | R4 (1 k) to LED4 (green) |
| 17 `3V3OUT` | Internal 3.3 V | 100 nF to GND (C10), not exported |
