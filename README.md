# InkTime-Smartwatch

An open-source smartwatch powered by the nRF52840 with an e-paper display, designed for low power consumption and BLE connectivity.

<img width="835" height="587" alt="image" src="https://github.com/user-attachments/assets/1c75478e-1384-4b33-8458-29849f2b9f8a" />


## BOM (Bill Of Materials) 
| Qty | Component | Value / Part | Package | Notes |
|-----|----------|-------------|--------|------|
| 3 | Buttons | EVP-AKE31A | SMD | SW_DN, SW_ENT, SW_UP |
| 1 | Solder Jumper | SJ1 | SMD | Configuration jumper |
| 3 | Resistor | 7.68kΩ | 0201 | R2, R3, R4 |
| 4 | Capacitor | 0.1µF | 0201 | Decoupling |
| 1 | Capacitor | 0.1µF / 50V | 0402 | EPD |
| 1 | Resistor | 0Ω / 0.47Ω | 0201 | EPD driver |
| 1 | Capacitor | 1µF | 0402 | General |
| 5 | Capacitor | 100nF | 0402 | Decoupling |
| 1 | Capacitor | 100pF | 0402 | Filtering |
| 6 | Resistor | 10kΩ | 0201 | Pull-ups / general |
| 1 | Capacitor | 10µF | 0201 | Bulk |
| 2 | Capacitor | 10µF | 0402 | Power |
| 1 | Inductor | 10µH | 0402 | DC/DC |
| 4 | Capacitor | 12pF | 0402 | Crystal load |
| 1 | Inductor | 15nH | 0402 | RF matching |
| 2 | Capacitor | 1pF | 0402 | RF |
| 6 | Capacitor | 1µF | 0201 | Decoupling |
| 9 | Capacitor | 1µF / 50V | 0402 | EPD |
| 1 | Resistor | 2.2kΩ | 0201 | Config |
| 1 | PMOS | DMG2305UX | SOT-23 | Power switching |
| 2 | Capacitor | 22µF | 0402 | Power rail |
| 1 | Antenna | 2450AT18B100E | 1206 | 2.4GHz |
| 1 | Inductor | 3.9nH | 0402 | RF matching |
| 1 | Crystal | 32.768kHz | 3215 | RTC |
| 1 | Crystal | 32MHz | 2016 | MCU clock |
| 2 | Resistor | 3.3kΩ | 0201 | General |
| 1 | Capacitor | 4.7µF | 0402 | Bulk |
| 5 | Capacitor | 4.7µF | 0402 | Decoupling |
| 1 | Capacitor | 47nF | 0402 | Filtering |
| 1 | FPC Connector | 503480-2400 | SMD | Display |
| 2 | Resistor | 5.1kΩ | 0201 | USB-C CC |
| 1 | Inductor | 68µH | SMD | Power |
| 1 | Capacitor | 820pF | 0402 | Filtering |
| 1 | Accelerometer | BMA421 | LGA | Sensor |
| 1 | Charger IC | BQ25180YBGR | BGA | LiPo charger |
| 1 | Haptic Driver | DRV2605YZFR | BGA | Motor driver |
| 1 | Inductor | 0.47µH | 2012 | Power stage |
| 14 | Test Points | TP pads | PCB | Debug |
| 1 | USB-C | KH-TYPE-C-16P | SMD | Power |
| 1 | Fuel Gauge | MAX17048G+T10 | TDFN-8 | Battery monitor |
| 3 | Diode | MBR0530 | SOD-123 | Schottky |
| 3 | Capacitor | N.C. | 0402 | Not populated |
| 1 | MCU | nRF52840 | QFN | Main MCU |
| 1 | Buck-Boost | RT6160AWSC | WLCSP | 3.3V rail |
| 1 | NMOS | SI1308EDL | SOT-323 | Switching |
| 1 | Debug Connector | TC2030-IDC | PCB | SWD |
| 1 | ESD Protection | USBLC6-2SC6Y | SOT-23-6 | USB protection |


## Hardware Description

### MCU — nRF52840

The nRF52840 is the central processing unit of the system.

**Key features:**
- ARM Cortex-M4F @ 64 MHz (with FPU)
- Bluetooth Low Energy (BLE 5.0)
- USB 2.0 Full Speed
- 1 MB Flash / 256 KB RAM

All peripherals are connected via:
- I2C
- SPI
- GPIO

---

### Power Architecture


USB-C → BQ25180 (charger) → LiPo battery (AKY0106, 400 mAh)
→ 3.3V rail (VREG, for MCU and all peripherals)

Battery → RT6160AWSC (boost) → EPD high-voltage supply (VCOM, gate voltages)


#### BQ25180 (Charger IC)

- Charges the LiPo battery
- Provides system rail (VSYS)
- I2C communication with MCU
- Interrupt output for status/fault

#### RT6160 (Buck-Boost Regulator)

- Converts VSYS to stable 3.3V
- Powers:
  - MCU
  - sensors
  - display
  - haptic driver

#### MAX17048 (Fuel Gauge)

- Monitors battery voltage and state of charge
- I2C interface
- ALERT signal for battery events

---

### E-Paper Display

Controlled via SPI and GPIO.

#### Interface

| Signal | Direction | Description |
|--------|----------|------------|
| SCK | MCU → Display | SPI clock |
| MOSI | MCU → Display | SPI data |
| EPD_CS | MCU → Display | Chip select |
| EPD_DC | MCU → Display | Data/Command |
| EPD_RST | MCU → Display | Reset |
| EPD_BUSY | Display → MCU | Status |

#### Power Stage

Includes:
- inductors
- Schottky diodes
- MOSFETs

Generates:
- PREVGH
- PREVGL

---

### IMU — BMA421

- Interface: I2C
- Features:
  - acceleration sensing
  - motion detection
  - step counting

**Interrupts:**
- INT1
- INT2

---

### Haptic Driver — DRV2605

- Interface: I2C
- Enable pin: `HAPTIC_EN`
- Function: vibration motor control

---

### USB Interface

- USB Type-C connector
- ESD protection: USBLC6-2SC6Y

**Signals:**
- VBUS
- D+
- D-

**Usage:**
- charging
- USB detection

---

### RF & Antenna

- 2.4 GHz chip antenna (2450AT18B100E)

**Design includes:**
- matching network
- antenna keepout
- no copper under antenna

---

### User Interface

3 buttons:
- Up
- Down
- Enter / Esc

- Connected to GPIO
- Active-low

---

### Debug Interface

SWD via Tag-Connect:

- SWDIO
- SWDCLK
- RESET
- SWO
- 3.3V
- GND

---

## Pin Mapping (nRF52840)

### I2C (Shared Bus)

| Pin | Function |
|-----|---------|
| P0.06 | SDA |
| P0.07 | SCL |

Devices:
- BMA421
- MAX17048
- BQ25180
- RT6160
- DRV2605

---

### SPI (E-Paper Display)

| Pin | Function |
|-----|---------|
| P0.02 | SCK |
| P0.03 | MOSI |
| P0.05 | CS |
| P0.15 | DC |
| P0.16 | RST |
| P0.17 | BUSY |

---

### Control & Interrupts

| Pin | Function |
|-----|---------|
| P1.01 | Display power (PFET) |
| P0.08 | BMA421 INT1 |
| P1.08 | BMA421 INT2 |
| P0.10 | MAX17048 ALERT |
| P0.11 | Charger interrupt |
| P0.12 | Haptic enable |

---

### Buttons

| Pin | Function |
|-----|---------|
| P0.13 | Up |
| P0.14 | Down |
| P1.00 | Enter / Esc |

---

### USB

| Signal | Function |
|--------|---------|
| D+ | Data |
| D- | Data |
| VBUS | Power detect |

---

### Clocks & Debug

- SWDIO / SWDCLK / RESET / SWO
- 32 MHz crystal (HFXO)
- 32.768 kHz crystal (LFXO)

---

## Design Considerations

- Use of dedicated hardware interfaces (I2C, SPI)
- Logical grouping of signals
- Simplified PCB routing
- Interrupt-driven architecture
- Clean debug interface

---

## Power Optimization

- MCU supports low-power modes
- E-paper consumes power only during refresh
- IMU is ultra low-power
- Haptic driver active only briefly

**Efficiency strategies:**
- enable peripherals only when needed
- regulated 3.3V rail
- independent control of display power
