# LoRa + STM32F103 Dev Board

A personal hardware project — STM32F103 + RFM95W on a 4-layer PCB with CAN bus, USB-C, and a proper power protection frontend. Built to learn mixed-signal PCB design and have something useful for robotics/IoT work.

---

## What's on the board

| Part | Why |
|------|-----|
| STM32F103C8T6 | Cortex-M3 @ 72MHz, 64KB flash — workhorse MCU, cheap, well documented |
| RFM95W-868S2 | 868MHz LoRa module, talks to STM32 over SPI |
| TJA1050T | CAN transceiver — needed for talking to motor controllers and other CAN nodes in robots |
| TPS259621DDAR | TI eFuse — handles overcurrent, UVLO, and inrush limiting on the 5V rail |
| AMS1117 (×2) | One LDO for the STM32 (3V3-STM), separate one for the LoRa module (3V3-LoRa) |
| ECS-80-20-4X | 8MHz crystal for the STM32 PLL |
| JAE USB-C | Power input + USB 2.0 FS to STM32 |
| Rosenberger SMA | Antenna connector for LoRa, matched with a Pi network |

---

## Power

Input is USB-C (5V) or an external 5V supply. Before anything gets power, it goes through:

- PMOS load switch + SMAJ5.0A DTVS + BZT52C5V6T-7 Zener for basic spike/reverse protection
- TPS259621 eFuse — current limit set with a 4× 100kΩ resistor divider on OVC, ILM monitored via RILM1/2

After the eFuse, the protected 5Vp rail splits into two AMS1117 LDOs:
- **3V3-STM** — powers the MCU, with 22µF + 100nF decoupling on both sides
- **3V3-LoRa** — dedicated rail for the LoRa module so RF switching noise doesn't get into the MCU supply. MCU can also cut this rail via `LoRa_en` to power-gate the module.

---

## LoRa RF

RFM95W → Pi matching network (AISC-0603 + 2× 1µF caps) → Rosenberger SMA. The matching network keeps the antenna path at 50Ω for 868MHz. SPI lines (MISO, MOSI, SCK, NSS) plus DIO0–DIO5 and RESET all go to the STM32.

---

## Connectors / Debug

| Header | Connector | Pins |
|--------|-----------|------|
| J_SWD | HTSW-104-07-G-S | SWDIO, SWCLK, 5V, GND |
| J_UART | HTSW-104-07-G-S | TX, RX, 5V, GND |
| J_JTAG | HTSW-104-07-G-S | JTDI, JTDO, 5V, GND |
| J_CAN | S4B-XH-SM4-TB | CAN_P, CAN_N, 5V, GND |
| USB-C | JAE | VBUS, D+, D−, CC1/CC2 |

CAN line termination: 120Ω split between Rcans and RCAN on the board. USB D+/D− route to PA11/PA12 on the STM32.

---

## Buttons

- **RST** — hardware reset, 10kΩ pull-up + 100nF debounce cap
- **BOOT0** — hold this and tap RST to enter USB DFU bootloader, 10kΩ pull-down

---

## PCB

- 4-layer, ~75×75mm, 4× M5 mounting holes at corners
- LoRa module sits top-left, STM32 center, CAN stuff top-right
- eFuse/protection circuit bottom-center, USB-C on the bottom edge
- Debug headers along the right edge, SMA on the top edge

---

## STM32 Pinout (main signals)

| Pin | Signal |
|-----|--------|
| PA4–PA7 | SPI1 — NSS/SCK/MISO/MOSI → LoRa |
| PA9/PA10 | UART1 TX/RX |
| PA11/PA12 | USB D−/D+ |
| PB8/PB9 | bxCAN RX/TX |
| PD0/PD1 | 8MHz crystal in/out |
| PA0–PA15 | GPIO/ADC, rest of peripherals |

---

## Flashing

Three ways to get firmware on:
1. **SWD** — J_SWD header + any ST-Link
2. **USB DFU** — hold BOOT0, press RST, plug in USB-C → shows up as DFU device
3. **UART bootloader** — J_UART header with a USB-UART adapter

Recommended toolchain: STM32CubeIDE or PlatformIO. For LoRa, [RadioLib](https://github.com/jgromes/RadioLib) works well with STM32 HAL. CAN uses the standard HAL bxCAN driver.

---

## Project files

```
├── STM32.SchDoc         # top-level schematic
├── MCU.SchDoc           # STM32 main circuitry + power
├── protection.SchDoc    # eFuse + input protection
├── PCB files
└── README.md
```

---

*2nd year ECE — Altium Designer*
