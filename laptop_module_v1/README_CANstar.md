# CANstar IRC26 Rev1 — CAN Hub Board

**Thalamus** | CANstar_IRC26_Rev1

A CAN bus distribution board built around a Blue Pill (STM32F103). The idea is simple — one MCU, one CAN transceiver, and 8 CAN ports fanned out so multiple nodes on the rover can all plug into a central hub. Designed for IRC26 , boards came back from fab done by lion circuits.

---

## What it does

It is the centralised node from where all data from all other nodes get passed on to the rovers on board laptop for further applications. Instead of daisy chaining connectors or hand wiring a bus, this board gives 8 keyed JST connectors all on the same CAN_H/CAN_L bus, with power (5V) on every port so nodes can draw power and comms from a single connector.

The Blue Pill plugs in via through-hole headers making it easy to replace and a sort of plug and play system.

---

## What's on the board

| Part | Why |
|------|-----|
| Blue Pill (STM32F103C8T6) | Main MCU — plugs into the 2× pin header socket on the left |
| ZC323000 | I/O expander — extends the Blue Pill's port A/B/C pins to the connectors |
| TJA1050T (Nexperia) | CAN transceiver, one shared bus for all 8 ports |
| TPS25944ARVCT | TI eFuse — OVP, current limit, UVLO, dV/dt, IMON/ILIM/PGOOD |
| AMS1117 | 5V → 3.3V LDO for MCU supply |
| SSA34HE3_A/I | Schottky diode on output rail |
| 150080GS75000 | Power indicator LED (D1) |
| Rbypass1 | 0Ω bypass resistor — jumper to route 5V directly if eFuse is not connected |
| B4B-XH-AM (×8) | CAN ports J_CAN_I/P1 through J_CAN_I/P8, each with 5V, GND, CAN_P, CAN_N |
| B4B-XH-AM (×1) | J_FTDI1 — UART header for Blue Pill programming/debug |

---

## Power

Input is 5V. Protection chain:

- **TPS25944 eFuse** — R1 (75kΩ) + R3 (8.2kΩ) + R4 (15kΩ) divider sets OVP threshold. Rlim_prot1/2 (both 100kΩ) set current limit. dVdT pin controls inrush slew rate.
- FLT and PGOOD outputs available on the schematic (can be read by MCU for fault detection)
- **SSA34HE3 Schottky** on the output, **D1 LED** (R2 = 220Ω) shows 5Vp rail is live
- **Rbypass1 (0Ω)** — short this to bypass the eFuse entirely if needed (useful for bring-up or if the eFuse isn't populated)
- **AMS1117 LDO** — 5Vp → 3V3 for the Blue Pill, 22µF input and output caps

Every CAN port also gets 5V directly from the protected rail — so plugged-in nodes draw power from here.

---

## CAN Bus

- Blue Pill bxCAN (MCU_CAN_RX / MCU_CAN_TX) → TJA1050T → single CAN_H / CAN_L bus
- All 8 J_CAN ports are connected to the same bus in parallel
- Rcan1 (120Ω) and Rcans1 on the schematic for line termination — check which end of the cable run this board sits on
- CAN_S on TJA1050T tied to GND (normal operating mode)
- Each port: **5V, GND, CAN_P (CAN_H), CAN_N (CAN_L)**

---

## UART / Programming

- **J_FTDI1** — 5V, GND, UART1_RX, UART1_TX — connect an FTDI or USB-UART adapter here
- Blue Pill's own BOOT0/RST buttons handle bootloader entry (on the Blue Pill module itself)
- Can also flash via SWD directly on the Blue Pill module pins

---

## PCB

- Rectangular form factor (~120×60mm approximately), 4× corner mounting holes
- Blue Pill socket on the left half
- All 8 CAN connectors lined up on the right edge — makes cable routing clean
- TJA1050T (CAN_MOD) sits center-right between the upper and lower port groups
- eFuse + LDO circuitry on the lower-left
- FTDI header bottom-left

---

## Pinout (Blue Pill signals used)

| Pin | Signal |
|-----|--------|
| PA9 / PA10 | UART1_TX / UART1_RX |
| PB8 / PB9 | MCU_CAN_RX / MCU_CAN_TX (bxCAN) |
| PA0–PA15, PB0–PB15 | Routed through ZC323000 I/O expander to connectors |

---

## Project files

```
├── top schematic        # CAN transceiver, eFuse, LDO, IO connectors
├── MCU.SchDoc           # Blue Pill / ZC323000 sub-sheet
├── PCB files            # CANstar_IRC26_Rev1
└── README.md
```

---

*Team Vyadh — IRC26 | 2nd year ECE, VIT Vellore — Altium Designer*
