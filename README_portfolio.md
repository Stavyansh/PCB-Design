# Hardware Portfolio — Thalamus Robotics | IRC26

2nd year ECE, VIT Vellore. All boards designed in Altium Designer, fabricated and delivered Jan–Mar 2026.

---

## Projects

### 1. STM32 + RFM96W Dev Board
![stm32_rfm96w_devboard](assets/stm32_rfm96w_devboard.png)

> A custom STM32F103 development board with onboard RFM96W LoRa, CAN bus, USB-C, dual LDO rails, and a TPS259621 eFuse — built as a standalone wireless + CAN embedded dev platform.

📁 `stm32_rfm96w_devboard` · [README](stm32_rfm96w_devboard/README.md)

---

### 2. STM32F103 CAN Node
![stm32f103_dev](assets/stm32f103_dev.png)

> A minimal STM32F103 board with TJA1050T CAN transceiver, TPS25944 eFuse protection, and SWD/UART/CAN headers — designed as a drop-in CAN bus node for robotics systems.

📁 `stm32f103_dev` · [README](stm32f103_dev/README.md)

---

### 3. CANstar — CAN Hub (laptop_module_v1)
![canstar](assets/canstar_IRC26_Rev1.png)

> A Blue Pill carrier board with 8 CAN output ports, shared TJA1050T transceiver, and eFuse protection — acts as a central CAN distribution hub for all nodes on the rover.

📁 `laptop_module_v1` · [README](laptop_module_v1/README.md)

---

### 4. Raven — LoRa + CAN Node (lora_module_v1)
![raven_lora](assets/raven_lora_IRC26_Rev1.png)

> Blue Pill carrier with RA-02 (SX1278) LoRa module and CAN passthrough — bridges the rover's CAN bus to a 433MHz LoRa wireless link for remote telemetry.

📁 `lora_module_v1` · [README](lora_module_v1/README.md)

---

### 5. Trident — PDB Splitter Rev 1 + Rev 2
![pdb_splitter](assets/pdb_splitter_rev2.png)

> A 3-input 6-output power distribution board with 1mΩ inline shunt resistors for per-channel current sensing and a 24V→3V resistor divider for bus voltage monitoring over ADC.

📁 `pdb_splitter_v1+v2` · [README](pdb_splitter_v1+v2/README.md)

---

### 6. PDB v1 + v2
> Main rover power distribution board — takes battery input and distributes protected power rails across the robot's subsystems.

📁 `pdb_v1+v2` · [README](pdb_v1+v2/README.md)

---

## Summary

| Board | MCU / Main IC | Key Feature | Status |
|-------|--------------|-------------|--------|
| STM32 + RFM96W Dev | STM32F103 + RFM96W | LoRa + CAN + USB-C on one board | Fabbed ✅ |
| STM32F103 CAN Node | STM32F103 | Minimal CAN node with eFuse | Fabbed ✅ |
| CANstar IRC26 | STM32F103 (Blue Pill) | 8-port CAN hub | Fabbed ✅ |
| Raven LoRa IRC26 | STM32F103 (Blue Pill) | LoRa ↔ CAN bridge | Fabbed ✅ |
| PDB Splitter (Trident) | Passive | 3-in 6-out + current sense | Fabbed ✅ (2 revs) |
| PDB | Passive | Main power distribution | Fabbed ✅ (2 revs) |

---

*All boards designed in Altium Designer — IRC26, Thalamus Robotics Team, VIT Vellore*
