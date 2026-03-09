# STM32F103 CAN Node Board

A minimal STM32F103-based board built around CAN bus communication, with a TPS25944 eFuse for power protection and clean 3.3V regulation. Designed as a CAN node for robotics — think motor controller interface, sensor aggregator, or general embedded node on a CAN network.

Boards came back from fab (you can see them in the delivery photo — both front and back look clean).

---

## What's on the board

| Part | Why |
|------|-----|
| STM32F103C8T6 | Cortex-M3 @ 72MHz, 64KB flash — same chip as the Blue Pill, solid for CAN-heavy embedded work |
| TPS25944ARVCT | TI eFuse — OVP, current limiting, UVLO, dV/dt slew control, IMON/ILIM monitoring |
| TJA1050T (Nexperia) | CAN transceiver, connects STM32 bxCAN to the physical CAN_H/CAN_L bus |
| AMS1117 | 5Vp → 3.3V LDO for MCU supply |
| SSA34HE3_A/I | Schottky diode on the output rail |
| 150080GS75000 | Status LED |
| 8MHz Crystal | External oscillator for STM32 PLL (20pF load caps) |
| B3F-1000 (×2) | BOOT0 and RST tactile buttons |
| B4E-XH-AM (×3) | SWD, UART, and CAN connectors |

---

## Power

Input is 5V. The protection chain before the MCU:

- **TPS25944 eFuse** — handles OVP (R1_prot 75kΩ, R2_prot 8.2kΩ divider sets threshold), current limit via Rlim_prot (100kΩ), IMON output monitored externally. dV/dt pin slows inrush. FLT and PGOOD outputs available.
- **SSA34HE3 Schottky** on the output for reverse current blocking
- **D5vp indicator LED** (150080GS75000 + R5vp 220Ω) shows the 5Vp rail is live
- **AMS1117 LDO** takes 5Vp → 3V3 for STM32, with 22µF caps on both input and output

STM32 power pins (VDD_1/2/3, VDDA, VBAT) all decoupled — 2.2µF + multiple 100nF caps close to the chip, GRM148R71A225KE15J bulk cap on the 3V3 rail.

---

## CAN Bus

- STM32 bxCAN (PA11/PA12 mapped as MCU_CAN_RX / MCU_CAN_TX) → TJA1050T → CAN_H / CAN_L
- CAN_S pin on TJA1050T pulled to GND (normal mode, not silent)
- Rcans termination resistor on the bus — add/remove depending on whether this node is at a line end
- J_CAN connector (B4E-XH-AM): 5V, GND, CAN_L (CL), CAN_H (CH)

---

## Debug / IO Connectors

| Header | Connector | Pins |
|--------|-----------|------|
| J_SWD | B4E-XH-AM | dio, CLK, GND, 5V |
| J_UART | B4E-XH-AM | TX, RX, GND, 5V |
| J_CAN | B4E-XH-AM | CL, CH, GND, 5V |

All three headers are on the top edge of the board — easy access while the board is mounted.

---

## Buttons

- **BOOT0** — pulls BOOT0 high, forces STM32 into system bootloader. Use with J_UART for UART flashing or J_SWD for DFU
- **RST** — hardware reset via MCU_NRST, 10kΩ pull-up (Rrst) + 100nF debounce (Crst)

---

## STM32 Pinout (key signals)

| Pin | Signal |
|-----|--------|
| PA9 / PA10 | MCU_TX1 / MCU_RX1 (UART1) |
| PB8 / PB9 | MCU_CAN_RX / MCU_CAN_TX (bxCAN) |
| PA13 / PA14 | MCU_SWDIO / MCU_SWCLK |
| PC13 | User LED / TAMPER |
| PD0 / PD1 | 8MHz crystal OSC_IN / OSC_OUT |

---

## PCB

- ~60×60mm square, 4× M5 corner mounting holes
- 2-layer board — red (top), blue (bottom)
- STM32 center, crystal above it, eFuse circuitry left side, CAN transceiver bottom-right
- All three connectors on the top edge, BOOT0 + RST on the bottom edge

---

## Flashing

Two ways:
1. **SWD** — J_SWD + ST-Link, works with STM32CubeIDE or OpenOCD
2. **UART bootloader** — hold BOOT0, press RST, connect J_UART to a USB-UART adapter, use STM32CubeProgrammer

Toolchain: STM32CubeIDE or PlatformIO. CAN driver: STM32 HAL bxCAN.

---

## Project files

```
├── MCU.SchDoc           # STM32F103 main + power
├── top.SchDoc           # Top-level schematic
├── PCB files
└── README.md
```

---

*2nd year ECE, VIT Vellore — Altium Designer*
