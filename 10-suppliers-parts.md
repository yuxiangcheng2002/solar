# Chapter 10: Suppliers & Parts Index

## 10.1 Solar Panels

### Small Panels for Embedded/IoT (< 5W)

| Manufacturer | Notable Products | Voltage | Power | Type | Where to Buy | Notes |
|-------------|-----------------|---------|-------|------|-------------|-------|
| **IXYS / Anysolar** | KXOB25-04X3F | 3.0V | 150mW | Mono-Si, rigid | DigiKey, Mouser | Tiny (25×25mm), ideal for energy harvesting ICs |
| | KXOB25-01X8F | 5.5V | 25mW | Mono-Si, rigid | DigiKey | 8 cells in series, good for BQ25570 |
| | KXOB25-02X8F | 4.7V | 44mW | Mono-Si, rigid | DigiKey, Mouser | Popular for IoT prototypes |
| **PowerFilm** | MP3-37 | 3.0V | 50mA | a-Si thin-film, flex | PowerFilm direct, DigiKey | Flexible, sewable, good for wearables |
| | Indoor Light Series (LL200-3-37) | 3.0V | ~50µA at 200 lux | a-Si thin-film | PowerFilm direct | Designed for indoor energy harvesting |
| | SP3-37 | 3.0V | 100mA | a-Si thin-film | PowerFilm direct | Classic Application series |
| **Voltaic Systems** | P126 (6V/1.0W) | 6.0V | 1.0W | Mono-Si, encapsulated | Voltaic direct, Amazon | ETFE-coated, waterproof, rugged |
| | P105 (6V/0.5W) | 6.0V | 0.5W | Mono-Si | Voltaic direct | Small (95×60mm) |
| | Custom panels | Various | 0.3–17W | Various | Voltaic direct (MOQ) | OEM/custom for production |
| **Seeed Studio** | 0.5W/1W/2W mini panels | 5–6V | 0.5–2W | Poly-Si | Seeed, Amazon | Budget option, good for prototyping |
| **DFRobot** | Solar Power Manager series panels | 5–6V | 1–6W | Mono-Si | DFRobot direct | Matched to their power manager boards |
| **Generic (AliExpress)** | 6V 1W–3W mini panels | 5.5–6V | 1–3W | Poly/Mono | AliExpress | Very cheap ($1–3), variable quality |

### Medium Panels for Raspberry Pi (5–50W)

| Manufacturer | Product | Power | Voltage | Type | Where to Buy | Notes |
|-------------|---------|-------|---------|------|-------------|-------|
| **Voltaic** | V25 (6V/4.5W) | 4.5W | 6V | Mono-Si | Voltaic direct | USB output available |
| **Renogy** | E.FLEX 21W / 30W foldable | 21–30W | 5V USB + 18V DC | Mono-Si | Amazon, Renogy direct | Foldable, portable, good for Pi 5 |
| **BigBlue** | 28W / 36W foldable | 28–36W | 5V USB-A/C | Mono-Si | Amazon | Multiple USB ports, affordable |
| **BougeRV** | Yuma 50W CIGS | 50W | 23V | CIGS thin-film | Amazon, BougeRV direct | Flexible, lightweight |
| **Generic (AliExpress)** | 12V 10–30W rigid | 10–30W | 12V | Poly/Mono | AliExpress | Cheap, rigid, suitable for fixed installations |

## 10.2 Energy Harvesting & MPPT ICs

| IC | Manufacturer | Package | Key Feature | DigiKey | Mouser | Approx. Price |
|----|-------------|---------|-------------|---------|--------|--------------|
| **BQ25570** | Texas Instruments | QFN-20 | Gold standard µW harvester, 100mV start | ✅ | ✅ | $4–5 |
| **BQ25504** | Texas Instruments | QFN-16 | Simpler BQ25570 without buck | ✅ | ✅ | $3–4 |
| **AEM10941** | e-peas | QFN-28 | 50mV cold start, dual LDO | ✅ | ✅ | $5–7 |
| **ADP5090** | Analog Devices | LFCSP-16 | 80mV start, tiny package | ✅ | ✅ | $4–5 |
| **SPV1050** | STMicroelectronics | QFN-20 | Up to 18V input, dual LDO | ✅ | ✅ | $3–4 |
| **LTC3105** | Analog Devices | DFN-12 | 225mV start, 400mA output | ✅ | ✅ | $5–6 |
| **CN3791** | Consonance | SOP-8 | Budget solar MPPT for 1S Li-ion | LCSC, AliExpress | — | $0.30–0.50 |
| **CN3065** | Consonance | SOP-8 | Linear solar charger, simpler CN3791 | LCSC, AliExpress | — | $0.20–0.30 |

### Evaluation Boards

| Board | IC | Price | Source |
|-------|-----|-------|--------|
| **BQ25570EVM** | BQ25570 | ~$49 | TI direct, DigiKey, Mouser |
| **BQ25504EVM** | BQ25504 | ~$49 | TI direct |
| **AEM10941 eval** | AEM10941 | ~$80 | e-peas direct |
| **STEVAL-ISV006V2** | SPV1050 | ~$35 | ST direct, DigiKey |
| **DC1587A** | LTC3105 | ~$60 | Analog Devices |
| **CN3791 module** | CN3791 | ~$1–2 | AliExpress, eBay |
| **TP4056 module** | TP4056 | ~$0.50 | AliExpress (ubiquitous) |

## 10.3 Charge Controllers & BMS

### IC-Level

| IC | Type | Chemistry | Max Current | Features | Package | Price |
|----|------|-----------|------------|----------|---------|-------|
| **MCP73831** | Linear charger | 1S Li-ion | 500mA | Simplest possible, SOT-23-5 | SOT-23-5 | $0.50 |
| **TP4056** | Linear charger | 1S Li-ion | 1A | Ubiquitous breakout module | SOP-8 | $0.20 |
| **BQ24074** | Linear, power path | 1S Li-ion | 1.5A | USB+solar input, DPPM | QFN-24 | $3 |
| **BQ25895** | Buck-boost charger | 1S Li-ion | 3A | USB PD, I2C, narrow VDC | QFN-24 | $4 |
| **BQ25185** | Linear charger | 1S Li-ion | 1A | Ship mode, tiny (WCSP) | WCSP-9 | $1.50 |
| **DW01A + FS8205** | Protection only | 1S Li-ion | — | OVP/UVP/OCP | SOT-23-6 + SOT-23-6 | $0.10 |

### Board-Level (Solar Power Managers)

| Board | Input | Output | Battery | Features | Price | Source |
|-------|-------|--------|---------|----------|-------|--------|
| **Waveshare Solar PM** | 6V solar | 5V/1A | 1S LiPo | Basic, low power only | $8–12 | Waveshare, Amazon |
| **Waveshare Solar PM (D)** | 6–24V solar | 5V/3A | 1S LiPo | MPPT, powers Pi | $20–25 | Waveshare, Amazon |
| **Waveshare Solar PM (B)** | 6–24V solar | 5V/3A | 1S/2S LiPo | Larger, powers Pi/Jetson | $25–30 | Waveshare |
| **DFRobot Solar Manager 5V** | 6V solar | 5V/1A | 1S LiPo | Integrated, compact | $12–15 | DFRobot |
| **PiJuice HAT** | Solar via PiJuice panel | 5V (HAT) | 1S LiPo (onboard) | RTC, wake scheduler, I2C API | $50–60 | Pi Hut, Pimoroni |
| **Adafruit Universal USB-C Charger** | USB-C 5V | 5V | 1S LiPo | BQ24074, power path | $15 | Adafruit |
| **Adafruit Solar Charger BFF** | 6V solar | 3.3V | 1S LiPo | Tiny, for QT Py / Xiao | $8 | Adafruit |

### Standalone MPPT Controllers (for 12V+ systems)

| Controller | Input | Battery | Current | Features | Price |
|-----------|-------|---------|---------|----------|-------|
| **Victron SmartSolar 75/10** | 12–75V | 12V lead/LiFePO4 | 10A | Bluetooth, app, VE.Direct | $70–90 |
| **Genasun GV-5** | 12–25V | 12V LiFePO4/Li-ion | 5A | Tiny, efficient, set-and-forget | $80–120 |
| **EPEver Tracer 1210AN** | 12–100V | 12/24V | 10A | Budget, RS485, widely available | $25–40 |

## 10.4 Batteries & Energy Storage

### LiPo / Li-ion Cells

| Source | Types | Notes |
|--------|-------|-------|
| **Adafruit** | 150mAh–6600mAh pouch cells, JST-PH | UL-listed, safe, well-documented |
| **SparkFun** | 400mAh–2000mAh pouch cells | JST-PH connector standard |
| **18650BatteryStore.com** | Samsung, LG, Sony/Murata 18650 & 21700 cells | Authentic cells, good for DIY packs |
| **LCSC / AliExpress** | Budget pouch cells | Verify specs; capacity often overstated |
| **Salvaged laptop packs** | 18650 cells (2000–3500mAh) | Free, but test each cell individually |

### LiFePO4 Cells & Packs

| Source | Products | Notes |
|--------|----------|-------|
| **TalentCell** | 12V 3Ah–12Ah packs | USB + barrel jack output, integrated BMS |
| **Headway / EVE** | 38120S (3.2V, 10Ah) cylindrical | For larger builds, screw terminal |
| **AliExpress** | IFR14500 (3.2V, 600mAh) — AA-size LiFePO4 | Drop-in for NiMH AA in solar garden lights |
| **BatteryHookup.com** | Surplus LiFePO4 cells & packs | Good deals on pull cells |

### Supercapacitors

| Part | Capacitance | Voltage | Size | Source | Use Case |
|------|------------|---------|------|--------|----------|
| **Seiko CPH3225A** | 11mF | 3.3V | Coin (3.2mm) | DigiKey, Mouser | Wearable burst power |
| **Eaton HV series** (HV0810-2R7105-R) | 1F | 2.7V | 8×10mm | DigiKey, Mouser | Small IoT backup |
| **Eaton KR series** | 0.1–1.5F | 5.5V (2S) | Various | DigiKey | General-purpose |
| **Maxwell/UCAP** | 1–3000F | 2.7V/cell | Large | Mouser, specialty | High-energy applications |

## 10.5 Microcontrollers & SBCs

### MCU Boards for Solar IoT

| Board | MCU | Deep Sleep | Connectivity | Solar-Friendly Features | Price | Source |
|-------|-----|-----------|-------------|------------------------|-------|--------|
| **DFRobot FireBeetle ESP32-E** | ESP32-E | ~10µA | Wi-Fi, BLE | Built-in charge IC, low sleep current | $7 | DFRobot, DigiKey |
| **Seeed XIAO ESP32-S3** | ESP32-S3 | ~14µA | Wi-Fi, BLE | Tiny (21×17mm), battery pad | $5 | Seeed |
| **Seeed XIAO nRF52840** | nRF52840 | ~5µA | BLE | Ultra-low power, tiny | $5 | Seeed |
| **RAK4631 (WisBlock Core)** | nRF52840 + SX1262 | ~2µA | BLE + LoRa | Modular system, best for LoRa solar | $20 | RAKwireless |
| **Heltec WiFi LoRa 32 V3** | ESP32-S3 + SX1262 | ~20µA | Wi-Fi, BLE, LoRa | OLED, battery connector, JST-PH | $16 | Heltec, AliExpress |
| **LILYGO T-Beam** | ESP32 + SX1262 + GPS | ~20µA | Wi-Fi, BLE, LoRa, GPS | Popular for Meshtastic | $25–35 | LILYGO, AliExpress |
| **Adafruit Feather nRF52840** | nRF52840 | ~5µA | BLE | LiPo charger built-in, Feather ecosystem | $25 | Adafruit |
| **Arduino Nano 33 BLE Sense** | nRF52840 | ~5µA | BLE | IMU, mic, environmental sensors onboard | $30 | Arduino, DigiKey |

### Single-Board Computers

| Board | Idle Power | Use Case | Price |
|-------|-----------|----------|-------|
| **Raspberry Pi Zero 2 W** | 0.5W | Solar mini-server, data logger | $15 |
| **Raspberry Pi 4B** | 3–4W | Gateway, heavier compute | $35–55 |
| **Raspberry Pi 5** | 3–5W idle, 25W peak | Solar workstation (needs big panel) | $60–80 |
| **Olimex A20-OLinuXino** | 1–2W | Used by LOW←TECH MAGAZINE | $50 |
| **Orange Pi Zero 2W** | 0.5–1W | Budget Pi Zero alternative | $15–20 |

## 10.6 Sensors (Low-Power, Solar-Friendly)

| Sensor | Measures | Interface | Active Current | Sleep Current | Price | Notes |
|--------|----------|-----------|---------------|---------------|-------|-------|
| **BME280** (Bosch) | Temp, humidity, pressure | I2C/SPI | 340µA | 0.1µA | $3–8 | The default choice for weather |
| **SHT40** (Sensirion) | Temp, humidity | I2C | 340µA | 0.08µA | $3–5 | More accurate humidity than BME280 |
| **BMP390** (Bosch) | Pressure, temp (altimeter) | I2C/SPI | 700µA | 0.3µA | $4–6 | ±3Pa accuracy |
| **TSL2591** (AMS) | Ambient light | I2C | 230µA | 0.5µA | $5–7 | Wide 600M:1 dynamic range |
| **VEML7700** (Vishay) | Ambient light (lux) | I2C | 45µA | 0.5µA | $2–4 | Simpler than TSL2591 |
| **INA219** (TI) | Current, voltage, power | I2C | 1mA | 6µA | $1–3 | For monitoring solar panel/battery |
| **MAX17048** (Maxim) | LiPo fuel gauge | I2C | 23µA | 2µA | $2–4 | SOC without coulomb counting |
| **SEN-15901** (SparkFun) | Weather meters (wind, rain) | Pulse/analog | — | — | $70–80 | Anemometer + wind vane + rain gauge |

## 10.7 Distributors & Marketplaces

| Distributor | Strengths | Min Order | Ship From | Notes |
|-------------|-----------|-----------|-----------|-------|
| **DigiKey** | Massive catalog, fast ship, datasheets | No min | USA | Best for ICs and passives |
| **Mouser** | Similar to DigiKey, good for TI/ADI/ST parts | No min | USA | Often has better stock of niche ICs |
| **LCSC** | Chinese components, very cheap | $2 min | China | Great for CN3791, TP4056, budget parts |
| **Adafruit** | Curated breakout boards, excellent docs | No min | USA | Premium but well-documented |
| **SparkFun** | Similar to Adafruit, good tutorials | No min | USA | Qwiic I2C ecosystem |
| **DFRobot** | Maker boards, sensors, solar managers | No min | China (or local) | FireBeetle series, Gravity sensors |
| **Seeed Studio** | XIAO boards, Grove ecosystem, Fusion PCB | No min | China | XIAO is great for solar IoT |
| **RAKwireless** | WisBlock LoRa ecosystem | No min | China | Best modular LoRa platform |
| **Pimoroni** | Pi accessories (UK) | No min | UK | PiJuice, Enviro boards |
| **Voltaic Systems** | Rugged small solar panels, battery packs | No min (1pc) | USA | Best quality small panels for IoT |
| **PowerFilm Solar** | Flexible/indoor solar cells | Contact for some | USA | Indoor Light Series, flex cells |
| **AliExpress** | Everything, rock-bottom prices | ~$1 min | China | Variable quality, verify specs |
| **Amazon** | Convenience, fast ship, panels & batteries | No min | Varies | Good for Renogy, BigBlue, battery packs |

## 10.8 Key Datasheets & Application Notes

| Document | Description | Source |
|----------|-------------|--------|
| **BQ25570 datasheet** (SLUSBH2) | Energy harvesting IC reference | ti.com/lit/ds/symlink/bq25570.pdf |
| **BQ25570 reference design** (SLUC484) | Complete harvesting circuit | ti.com |
| **AEM10941 datasheet** | e-peas ambient energy manager | e-peas.com |
| **CN3791 datasheet** | Budget solar MPPT charger | consonance-elec.com |
| **AN-1271** (TI) | Selecting solar cells for energy harvesting | ti.com |
| **DN-522** (Analog) | LTC3105 solar application | analog.com |
| **ESP32 power consumption** | Sleep mode reference | espressif.com |
| **nRF52840 power profiling** | Nordic power analysis | nordicsemi.com |

## 10.9 Open-Source Hardware & Reference Designs

| Project | Description | License | Link |
|---------|-------------|---------|------|
| **Solar Protocol** | Distributed solar server network | Open source | github.com/alexnathanson/solar-protocol |
| **SoLoRa** | Solar LoRa IoT node | Open hardware | hackaday.io/project/159658 |
| **OpenEnergyMonitor** | Energy monitoring platform | GPL | openenergymonitor.org |
| **MPPT Solar Charger** (Libre Solar) | Open-source MPPT charge controller | CC BY-SA | libre.solar |
| **RAK WisBlock** | Modular IoT platform (schematics available) | Open hardware | docs.rakwireless.com |
| **Meshtastic** | LoRa mesh firmware | GPL-3.0 | meshtastic.org |
| **ESP-IDF examples** | Espressif IoT framework examples | Apache-2.0 | github.com/espressif/esp-idf |

---

**Key takeaway:** The solar embedded ecosystem is mature and accessible. Between Voltaic/PowerFilm panels, TI/e-peas harvesting ICs, and ESP32/nRF52 platforms, you can source every component for a solar IoT project from reputable distributors with datasheets. For prototyping, start with breakout boards (Adafruit, SparkFun, DFRobot). For production, move to LCSC/DigiKey for component-level sourcing at volume pricing.
