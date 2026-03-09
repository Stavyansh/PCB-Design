# PDB Splitter — IRC26

**Thalamus Robotics Team** | IRC26

A power distribution board for the rover. Takes 3 independent high-current inputs (battery rails) and splits each into 2 outputs, giving 6 total output channels — all with inline current sensing and voltage monitoring brought out to ADC headers. Designed so the MCU (on a separate board) can read per-channel current and bus voltage over ADC without any external INA modules.

Two revisions were fabbed — Rev 1 (Tritium) and Rev 2 (Trident) — both via Blue Dart to VIT Vellore.

---

## What it does

Three separate power rails come in on XT60 male connectors (X1, X2, X3 — one per rail). Each rail splits into two XT60 female output connectors. On the way out, every channel passes through a 1mΩ shunt resistor, and the voltage across the shunt is tapped for current sensing. Each input rail also has a voltage divider that scales 24V down to something the MCU ADC can read directly.

So in total you get:
- 6 current sense ADC outputs (ADC11, ADC12, ADC21, ADC22, ADC31, ADC32)
- 3 voltage sense ADC outputs (ADC1, ADC2, ADC3 — one per input rail)
- All brought out to B6B-EH-A pin headers (J1, J2)

---

## What's on the board

| Part | Role |
|------|------|
| XT60-M (×3) | Power inputs — X1, X2, X3 |
| XT60-F (×6) | Power outputs — X11/X12, X21/X22, X31/X32 |
| HoJLR2512-3W-1mR-1% | 1mΩ shunt resistor, one per output channel (6 total) |
| CRGCQ0805J68K + CRGCQ0805J10K | Resistor voltage divider — scales 24V bus down to ADC-readable range (24V → ~3V) |
| B6B-EH-A(LF)(SN) (×2) | ADC output headers, J1 + J2 (6 pins each: GND, ADC signals) |

---

## Current Sensing

Each output channel has a **HoJLR2512 1mΩ shunt** in series on the high side. The voltage after the shunt is tapped as `out_adc`. At full load (say 20A), the drop across 1mΩ is 20mV — the MCU ADC reads this directly (no amplifier in v1/v2, raw differential read or single-ended depending on firmware).

The schematic sub-sheet `current_sense.SchDoc` is instantiated 6 times — once per output channel.

---

## Voltage Sensing

Each input rail (VCC1, VCC2, VCC3) has a `24vto3v.SchDoc` sub-sheet. This is just a two-resistor divider:
- **Rvd1 = 68kΩ** (CRGCQ0805J68K)
- **Rvd2 = 10kΩ** (CRGCQ0805J10K)

Divide ratio = 10/(68+10) ≈ 0.128 → 24V input gives ~3.07V at the ADC pin. Fits within 3.3V ADC rail.

---

## ADC Header Pinout

| Pin | Signal |
|-----|--------|
| 1 | GND |
| 2 | ADC2 (VCC2 voltage sense) |
| 3 | ADC22 (X22 current) |
| 4 | ADC3 (VCC3 voltage sense) |
| 5 | ADC32 (X32 current) |
| 6 | ADC31 (X31 current) |

(J1 has: GND, ADC2, ADC22, ADC3, ADC32, ADC31 — read from schematic)

---

## PCB Layout

- Rectangular, ~120×65mm, 4× M5 corner mounting holes
- 3 XT60-M inputs along the left edge (X1, X2, X3)
- 6 XT60-F outputs — X11/X12 on top, X21/X22 center-right, X31/X32 on bottom
- Shunt resistors placed inline between each input split and the corresponding output XT60
- ADC headers J1, J2 on the right side — plug directly into MCU board ADC pins

---

## Rev 1 vs Rev 2 — What Changed

| | Rev 1 (Tritium) | Rev 2 (Trident) |
|---|---|---|
| **Board name** | Tritium | Trident |
| **PCB color** | Black | Green |
| **Shunt footprint** | HoJLR2512 (2512 SMD) populated | Footprint revised — pads repositioned/resized in layout |
| **ADC headers** | B6B-EH-A connectors (2.5mm pitch) | Connector footprints updated, same signal mapping |
| **Silkscreen** | Minimal labels | Cleaner net labels, polarity markings more visible |
| **File name** | PDB_splitter_rev_1 | PDB_splitter_rev_2 |
| **Layout cleanup** | Initial layout, some routing detours | Tighter routing, better copper pour clearance around shunt pads |

The core circuit — 3-in 6-out split, shunt current sensing, resistor voltage divider — is the same in both revisions. Rev 2 was mainly a layout and footprint cleanup based on what was observed after getting Rev 1 back from fab.

---

## Firmware Notes

- 6 ADC channels for current + 3 for voltage = 9 ADC inputs total to the MCU
- At 1mΩ shunt, 1A of current = 1mV across the shunt — need ADC with reasonable resolution (12-bit on STM32 gives ~0.8mV LSB at 3.3V ref, so ~0.8A resolution without amplification)
- For better resolution, consider adding an INA series op-amp in a future rev or using a dedicated current sense amp
- Voltage divider gives 24V → ~3V, calibrate in firmware with measured resistor values

---

## Project files

```
├── top schematic            # XT60 connectors, current sense instances, voltage dividers, ADC headers
├── current_sense.SchDoc     # Shunt + ADC tap sub-sheet (×6)
├── 24vto3v.SchDoc           # Voltage divider sub-sheet (×3)
├── PCB — PDB_splitter_rev_1
├── PCB — PDB_splitter_rev_2
└── README.md
```

---

*Thalamus Robotics Team — IRC26 | 2nd year ECE, VIT Vellore — Altium Designer*
