# Changelog

All notable changes to this project are documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Planned
- Correct BOM data for 100 nF capacitors (C7, C8, C10)
- Publish Altium sources and fabrication outputs
- Rev B: expose RESET, evaluate additional ATmega328PB peripherals

## [Rev A] - 2026-10-21

### Added
- Initial schematic and PCB layout (Altium Designer)
- ATmega328PB core with 16 MHz crystal and tactile reset switch
- FT232RL USB-UART bridge with DTR auto-reset
- LM1117-5.0 regulator with USB Schottky OR-ing
- 26 castellated I/O pads
- Status LEDs: power, user (D13), and two FT232RL activity indicators
- Documentation: hardware design notes, pinout, bring-up checklist, BOM
