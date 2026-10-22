# CORE-328PB Bring-Up Checklist

Use this procedure for first power-up of an assembled Rev A module. Record measured values in the **Result** column.

> [!CAUTION]
> Use a current-limited bench supply for the first `VCC` power-up. Do not connect USB and an external supply simultaneously until each source has been validated independently.

## 1. Visual and Passive Checks (Unpowered)

| # | Check | Expected | Result |
|:-:|:--|:--|:--|
| 1.1 | Inspect solder joints on U1 (VFQFN-32), U2 (SSOP-28), U3 (SOT-223) | No bridges, full wetting | |
| 1.2 | Verify LED polarity and colour (LED1 green, LED2 orange, LED3 red, LED4 green) | Matches silkscreen | |
| 1.3 | Verify polarity of tantalum capacitor C1 | Matches marking | |
| 1.4 | Resistance `+5V` to `GND` | Not a short | |
| 1.5 | Resistance `VCC` to `GND` | Not a short | |
| 1.6 | Confirm 100 nF capacitors (C7, C8, C10) are actually 100 nF | See [design review finding 1](HARDWARE_DESIGN.md#design-review-findings) | |

## 2. Power Validation

| # | Test | Expected | Result |
|:-:|:--|:--|:--|
| 2.1 | USB only: measure `+5V` pad | Approximately VUSB minus D1 drop | |
| 2.2 | LED1 state with USB only | On | |
| 2.3 | USB only: supply current at idle | Record value | |
| 2.4 | `VCC` only (bench supply, current-limited): measure `+5V` pad | 5 V, within regulator tolerance | |
| 2.5 | `VCC` only: regulator temperature after 5 min | Record value | |
| 2.6 | Both sources present: confirm no abnormal current on either source | Record values | |

## 3. USB-UART Bridge

| # | Test | Expected | Result |
|:-:|:--|:--|:--|
| 3.1 | Connect to host | Enumerates as a USB serial device | |
| 3.2 | LED3 / LED4 behaviour on host connect | Record behaviour (depends on FT232RL EEPROM) | |
| 3.3 | Loopback test: jumper `D0/RX` to `D1/TX`, send data from host | Data echoed only if firmware echoes; otherwise verify the TX/RX waveforms with a scope | |
| 3.4 | Scope `RESET` while opening the serial port | Short low pulse from DTR coupling, then recovery to 5 V | |

## 4. MCU Core

| # | Test | Expected | Result |
|:-:|:--|:--|:--|
| 4.1 | Read device signature over ISP | `1E 95 16` (ATmega328PB) | |
| 4.2 | Set fuses for external 16 MHz crystal, program bootloader | Programming verifies | |
| 4.3 | Scope crystal output at `XTAL2` | 16 MHz, stable oscillation | |
| 4.4 | Press `SW1` | `RESET` pulled low, MCU restarts | |

## 5. Firmware Smoke Tests

| # | Test | Expected | Result |
|:-:|:--|:--|:--|
| 5.1 | Toggle `D13` | LED2 blinks | |
| 5.2 | Upload firmware over UART using auto-reset | Upload succeeds without manual reset | |
| 5.3 | UART echo at 115200 baud (or chosen rate) | Characters returned | |
| 5.4 | Read `A0` to `A7` with a known reference voltage | Readings within expected ADC tolerance | |
| 5.5 | Measure `AREF` pad | Matches selected reference mode | |
| 5.6 | SPI / I2C loopback or device test (D11-D13, A4/A5) | Communication succeeds | |

## 6. Sign-Off

| Item | Value |
|:--|:--|
| Board serial / lot | |
| Date | |
| Tester | |
| Outcome | PASS / FAIL |
| Notes | |
