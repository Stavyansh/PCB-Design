# Raven — LoRa_IRC26_Rev1

**Thalamus** | LoRa_IRC26_Rev1

A Blue Pill carrier board that combines LoRa (RA-02/SX1278) with CAN bus passthrough and protected 5V regulation. Built for IRC26 (Indian Rover Challenge) — the idea being a wireless telemetry node that also sits on the rover's CAN network. Boards came back from fab via Blue Dart, Jan 2026.

---

## What it does

The board lets a Blue Pill talk LoRa over SPI while also being connected to the CAN bus. Useful as a remote telemetry node — receive/transmit LoRa packets and simultaneously read/write CAN frames. Power comes in from 5V, goes through an eFuse, then an LDO drops it to 3.3V for the MCU and LoRa module.

CAN passthrough ports (input + output) mean this board can sit inline on a CAN cable run, not just at a terminal node.

---

## What's on the board

| Part | Why |
|------|-----|
| Blue Pill (STM32F103C8T6) | Main MCU — plugs into the through-hole socket, swappable |
| RA-02 (SX1278) | LoRa module, 433MHz, connects via SPI1 — labeled `LoRa_SX1278` on silkscreen |
| ZC323000 | I/O expander — routes Blue Pill GPIO (Port A/B/C) to the LoRa and CAN signals |
| TJA1050T (Nexperia) | CAN transceiver — labeled `CAN_CNT`, MCU_CAN_RX/TX → CAN_H/CAN_L |
| TPS25944ARVCT | TI eFuse — OVP, current limit, UVLO, dVdT slew, IMON/ILIM/PGOOD/FLT |
| AMS1117 | 5Vp → 3V3 LDO for the MCU and LoRa module |
| SSA34HE3_A/I | Schottky diode on the eFuse output |
| 150080GS75000 | Power indicator LED (D1, R2 = 220Ω) |
| Rbypass1 (0Ω) | Shorts the eFuse — populate to bypass protection if needed |
| B4B-XH-AM (×3) | J_CAN_I/P (CAN in), J_CAN_O/P (CAN out), J_FTDI (UART) |

---

## Power

5V input → TPS25944 eFuse → 5Vp protected rail → AMS1117 → 3V3

- **eFuse config:** R1 (75kΩ) + R3 (8.2kΩ) + R4 (15kΩ) resistor divider sets OVP threshold. Rlim_prot1 + Rlim_prot2 (both 100kΩ) set current limit. dVdT pin slows inrush.
- FLT and PGOOD outputs available — can be polled by MCU to detect power faults
- **Rbypass1 (0Ω)** — bridge this to skip the eFuse entirely (useful for bring-up before eFuse is populated)
- **AMS1117** — 22µF caps in and out, 3V3 feeds both Blue Pill VCC and LoRa module VCC

---

## LoRa

- **RA-02 (SX1278)** module footprint on the right side of the board (`LoRa_SX1278` in silkscreen)
- Connected to Blue Pill via **SPI1**: SPI1_SCK, SPI1_MOSI, SPI1_MISO, NSS
- Additional pins: RST, DIO0–DIO5 (interrupt lines for TX done, RX done, etc.)
- Operates at 3.3V — powered from LDO output directly
- No onboard antenna connector shown in schematic — RA-02 module has its own U.FL/wire antenna pad

---

## CAN Bus

- TJA1050T (MCU_CAN_RX / MCU_CAN_TX from Blue Pill bxCAN) → CAN_H / CAN_L
- **J_CAN_I/P** — CAN input port (5V, GND, CAN_P, CAN_N)
- **J_CAN_O/P** — CAN output port (5V, GND, CAN_P, CAN_N)
- Having both in and out means the board can sit mid-bus, not just at a line end
- Rcan1 (120Ω) and Rcans1 (0Ω, unpopulated by default) for termination — populate Rcans1 only if this is a bus endpoint
- CAN_S tied to GND on TJA1050T (normal mode)

---

## UART / Programming

- **J_FTDI** — 5V, GND, UART1_RX, UART1_TX — standard FTDI/USB-UART header
- Flash via UART bootloader: hold BOOT0 on Blue Pill, press RST, connect FTDI adapter
- Or SWD directly on Blue Pill module pins

---

## PCB

- Rectangular, ~120×65mm, 4× M5 corner mounting holes (labeled GND)
- Blue Pill socket takes up the left half of the board
- LoRa module footprint on the right, upper half
- LDO + eFuse + bypass in the upper center
- CAN transceiver bottom-left
- FTDI + CAN I/P + CAN O/P connectors all on the bottom edge

---

## Blue Pill Pin Usage

| Pin | Signal |
|-----|--------|
| PA4 | SPI1 NSS → LoRa |
| PA5 | SPI1 SCK → LoRa |
| PA6 | SPI1 MISO → LoRa |
| PA7 | SPI1 MOSI → LoRa |
| PB0–PB5 | DIO0–DIO5 → LoRa interrupt lines |
| PA9 / PA10 | UART1_TX / UART1_RX |
| PB8 / PB9 | MCU_CAN_RX / MCU_CAN_TX |

---

## Firmware

- Flash via UART (J_FTDI) or SWD on Blue Pill pins
- Toolchain: STM32CubeIDE or PlatformIO
- LoRa: [RadioLib](https://github.com/jgromes/RadioLib) has SX1278 support, port to STM32 HAL SPI
- CAN: STM32 HAL bxCAN driver

Typical use case: poll CAN bus, package frames into a LoRa payload, transmit to a base station. Or receive LoRa commands and put them onto the CAN network.

---

## Project files

```
├── LoRa_Laptop_Mod(V1)  # top-level schematic title
├── PCB files            # LoRa_IRC26_Rev1
└── README.md
```

---

*Thalamus Robotics Team — IRC26 | 2nd year ECE, VIT Vellore — Altium Designer*
