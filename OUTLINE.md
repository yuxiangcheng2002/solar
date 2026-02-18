# Solar Power Research — Outline

## Scope
Comprehensive textbook-style notes on solar power for:
- **Wearables** (small flex panels, energy harvesting ICs)
- **Embedded systems** (ESP32, nRF52/53, Arduino)
- **Raspberry Pi** (5V solar + battery management)
- **Ecological / permacomputing** (off-grid, minimal, resilient)
- **Ubiquitous computing / IoT** (deploy-and-forget nodes)

## Structure

### 1. Fundamentals
- Photovoltaic physics (brief, practical)
- Solar cell types: monocrystalline, polycrystalline, thin-film, organic, perovskite
- Key specs: Voc, Isc, Vmp, Imp, fill factor, efficiency
- I-V curves and how load affects output
- Real-world factors: irradiance, angle, temperature, shading

### 2. Solar Panels for Small Systems
- Panel form factors: rigid, semi-flex, flexible, thin-film
- Sizing: how to calculate panel size for a given load
- Recommended panels by application:
  - Wearable (< 1W): flex cells, amorphous silicon
  - IoT node (1-5W): small rigid/semi-flex
  - Raspberry Pi (10-25W): larger panels
- DIY vs commercial panels
- Industry parts: specific models, datasheets, suppliers

### 3. Energy Harvesting ICs & MPPT
- Maximum Power Point Tracking (MPPT) explained
- Key chips:
  - **TI BQ25570** — ultra-low power harvester (µW to mW)
  - **TI BQ25504** — boost charger from low voltage
  - **e-peas AEM10941** — ambient energy manager
  - **Analog Devices ADP5090** — ultralow power boost
  - **SPV1050** (ST) — ultra-low power energy harvester
  - **LTC3105** (Analog) — step-up with MPPT
  - **CN3791** — budget solar MPPT charge controller
- Evaluation boards and breakouts
- DIY MPPT circuits

### 4. Battery Management
- Battery chemistry: LiPo, LiFePO4, supercapacitors, NiMH
- Charge controllers and BMS ICs
- Battery sizing for solar applications
- Charge profiles and protection
- Cold weather considerations
- Supercapacitor vs battery trade-offs

### 5. Power Regulation & Distribution
- Buck/boost converters for solar systems
- Low quiescent current regulators
- Power path management (simultaneous charge + use)
- Voltage sequencing for multi-rail systems
- Load switches and power gating

### 6. System Design Patterns
- **Wearable solar:** flex panel → BQ25570 → supercap/LiPo → LDO → MCU
- **IoT sensor node:** small panel → CN3791/BQ25504 → LiFePO4 → buck → ESP32/nRF
- **Raspberry Pi solar:** 20W panel → MPPT controller → 12V battery → 5V/3A buck → Pi
- **Permacomputing node:** oversized panel + large battery + deep sleep + watchdog
- Energy budgets and duty cycling
- Sleep current optimization

### 7. Software & Firmware
- Solar-aware scheduling (harvest-aware duty cycling)
- Energy-neutral operation
- Battery state estimation (coulomb counting, voltage-based)
- Sleep modes: ESP32, nRF52, ATmega, Pi
- Watchdog and recovery strategies

### 8. Ecological & Permacomputing
- Philosophy: minimal hardware, maximal lifespan
- Low-tech solar computing projects
- Solar protocol / solar-powered servers
- E-waste considerations
- Repair and modularity
- Case studies: Solar Protocol, LOW←TECH MAGAZINE

### 9. Practical Builds & Reference Designs
- Solar-powered ESP32 weather station
- Solar LoRa sensor node (nRF52 + RFM95)
- Solar Pi Zero dashboard
- Solar Pi 5 portable (our project!)
- Wearable solar charging garment

### 10. Suppliers & Parts Index
- Panel suppliers (Voltaic, PowerFilm, IXYS/Anysolar, AliExpress)
- IC distributors (DigiKey, Mouser, LCSC)
- Breakout boards (Adafruit, SparkFun, DFRobot)
- Reference designs and app notes
- Open-source hardware projects

## Format
- Textbook-style markdown notes
- Diagrams where helpful (ASCII or mermaid)
- Datasheet references and links
- Practical calculations and examples
- Each chapter = one .md file in the repo
