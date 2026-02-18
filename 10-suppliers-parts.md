# Chapter 10: Suppliers & Parts Index

> **A curated, buyable-today shopping reference for solar-powered embedded systems.**
> All prices are approximate (USD, qty 1) as of early 2026. Check linked distributors for current stock.

---

## 10.1 Solar Panels

### 10.1.1 Wearable / Tiny (< 1 W)

| Part Number | Manufacturer | Voc / Vmp | Power | Type | Size | Price | Source |
|-------------|-------------|-----------|-------|------|------|-------|--------|
| **KXOB25-04X3F** | Anysolar (IXYS) | 3.0V / 2.07V | 22mW | Mono-Si, rigid | 25×19mm | ~$3.50 | DigiKey, Mouser |
| **KXOB25-05X3F** | Anysolar | 3.0V / 2.07V | 44mW | Mono-Si, rigid | 25×19mm | ~$3.50 | DigiKey, Mouser |
| **KXOB25-01X8F** | Anysolar | 5.5V / 4.0V | 25mW | Mono-Si, rigid | 22×7mm | ~$3.00 | DigiKey |
| **KXOB25-02X8F** | Anysolar | 5.6V / 4.7V | 44mW | Mono-Si, rigid | 22×13mm | ~$3.50 | DigiKey, Mouser |
| **KXOB25-03X4F** | Anysolar | 2.76V | 30mW | Mono-Si Gen3 | 25×19mm | ~$3.20 | DigiKey |
| **SM141K04LV** | Anysolar | 2.76V | 123mW | Mono-Si, large | 44×25mm | ~$5.30 | DigiKey |
| **KXOB25-14X1F** | Anysolar | 0.69V | 31mW | Mono-Si, single cell | 25×19mm | ~$3.05 | DigiKey |
| **MP3-37** | PowerFilm | 3.0V | 50mA (150mW) | a-Si thin-film, flex | 64×37mm | ~$6.00 | PowerFilm, DigiKey |
| **SP3-37** | PowerFilm | 3.0V | 100mA (300mW) | a-Si thin-film, flex | 114×37mm | ~$8.00 | PowerFilm |
| **MP7.2-150** | PowerFilm | 7.2V | 150mA | a-Si, flex | Various | ~$15.00 | PowerFilm |
| **OPT-SOLAR-01** | Seeed Studio | 5.5V | 0.5W | Poly-Si | 60×60mm | ~$2.50 | Seeed |

**Notes:**
- Anysolar KXOB25 series are the gold standard for energy harvesting IC inputs. 25% efficiency mono-Si in tiny packages.
- PowerFilm cells are flexible and sewable — ideal for wearables and curved surfaces.
- The "Gen3" Anysolar cells have improved efficiency over the original IXYS-branded versions.

### 10.1.2 IoT (1–5 W)

| Part Number | Manufacturer | Voltage | Power | Type | Size | Price | Source |
|-------------|-------------|---------|-------|------|------|-------|--------|
| **P126** | Voltaic Systems | 6V | 2W | Mono-Si, ETFE | 142×110×3mm | ~$17 | Voltaic, Adafruit (#5366) |
| **P105** | Voltaic Systems | 6V | 5.5W | Mono-Si, ETFE | 260×175×3mm | ~$29 | Voltaic, Adafruit |
| **P124** | Voltaic Systems | 6V | 3.5W | Mono-Si, ETFE | 195×140×3mm | ~$23 | Voltaic |
| **6V 1W Mini** | Seeed Studio | 5.5V | 1W | Poly-Si, epoxy | 110×60mm | ~$3.50 | Seeed, Amazon |
| **6V 2W Mini** | Seeed Studio | 5.5V | 2W | Poly-Si, epoxy | 136×110mm | ~$5.00 | Seeed, Amazon |
| **FIT0601** | DFRobot | 6V | 1W | Mono-Si | 110×60mm | ~$5.00 | DFRobot |
| **FIT0330** | DFRobot | 5.5V | 1.5W | Poly-Si | 81×137mm | ~$6.50 | DFRobot |
| **5V 3W USB Panel** | Various (generic) | 5V USB | 3W | Mono-Si | ~160×120mm | ~$5–8 | AliExpress, Amazon |

**Notes:**
- Voltaic panels are the quality benchmark: ETFE-coated, waterproof, UV-resistant, backed by real outdoor testing. Worth the premium for permanent installations.
- Generic AliExpress panels are fine for prototyping but expect ±30% of rated power and poor longevity outdoors.

### 10.1.3 Pi-Class (10–25 W)

| Product | Manufacturer | Power | Voltage | Output | Price | Source |
|---------|-------------|-------|---------|--------|-------|--------|
| **P110** | Voltaic Systems | 9W | 6V | Bare wire | ~$49 | Voltaic |
| **P117** | Voltaic Systems | 17W | 6V | Bare wire | ~$79 | Voltaic |
| **E.FLEX 21** | Renogy | 21W | 5V USB + 18V DC | Foldable, USB-A/C | ~$50 | Amazon, Renogy |
| **E.FLEX 30** | Renogy | 30W | 5V USB + 18V DC | Foldable | ~$65 | Amazon, Renogy |
| **28W Foldable** | BigBlue | 28W | 5V USB ×3 | Foldable, multiple USB | ~$50 | Amazon |
| **36W Foldable** | BigBlue | 36W | 5V USB + USB-C | Foldable | ~$60 | Amazon |
| **Yuma 50W CIGS** | BougeRV | 50W | 23V Vmp | Flexible, lightweight | ~$100 | Amazon, BougeRV |
| **10W–30W rigid** | Generic | 10–30W | 12V or 18V | MC4 / bare wire | ~$10–25 | AliExpress, Amazon |

**Notes:**
- For Raspberry Pi 5 (needs 5V/5A = 25W peak), plan for at least a 30W panel with battery buffer.
- Voltaic's 6V panels can charge LiPo directly; 12V/18V panels need a charge controller/buck converter.

### 10.1.4 Indoor Light Harvesting

| Part Number | Manufacturer | Voc | Power @ 200 lux | Type | Size | Price | Source |
|-------------|-------------|-----|-----------------|------|------|-------|--------|
| **AM-1815CA** | Panasonic (Amorton) | 4.9V | 126µW | Amorphous Si, glass | 53×25mm | ~$5–8 | DigiKey, Mouser |
| **AM-1816CA** | Panasonic (Amorton) | 6.3V | 100µW | Amorphous Si, glass | 53×25mm | ~$6–9 | DigiKey, Mouser |
| **AM-1801CA** | Panasonic (Amorton) | 2.4V | 52µW | Amorphous Si, glass | 38×19mm | ~$4 | DigiKey, Mouser |
| **LL200-3-37** | PowerFilm | 3.0V | ~50µA | a-Si thin-film | 64×37mm | ~$7 | PowerFilm, DigiKey |
| **LL200-2.4-75** | PowerFilm | 2.4V | ~50µA | a-Si thin-film | 37×22mm | ~$5 | PowerFilm, DigiKey |
| **LEH3_50X50_6_10** | Epishine | 3.26V | 137µA (447µW) | Organic PV, printed | 50×50mm | ~$8–12 | Farnell/Newark |
| **LEH3_100X50_6_10** | Epishine | 3.26V | 280µA (~900µW) | Organic PV, printed | 100×50mm | ~$12–16 | Farnell/Newark |

**Notes:**
- Panasonic Amorton cells are the gold standard for indoor harvesting. Glass substrate, extremely long life.
- Epishine LEH3 organic cells are a newer entrant (Swedish company) — flexible, printed, optimized for artificial light. Now available via Farnell.
- PowerFilm Indoor Light Series are optimized for fluorescent/LED lighting, not sunlight.
- Indoor harvesting yields µW–low mW. Plan your power budget accordingly (e.g., BLE beacon once per minute).

---

## 10.2 Energy Harvesting ICs

### 10.2.1 Premium Energy Harvesting PMICs

| IC | Manufacturer | Cold Start | Input Range | Output | Iq | Package | Price | Source |
|----|-------------|-----------|-------------|--------|-----|---------|-------|--------|
| **BQ25570** | Texas Instruments | 330mV (100mV warm) | 0.1–5.1V | Boost + Buck (1.8–3.6V) | 488nA | QFN-20 | ~$4.50 | DigiKey, Mouser |
| **BQ25504** | Texas Instruments | 330mV (80mV warm) | 0.08–3V | Boost only | 330nA | QFN-16 | ~$3.50 | DigiKey, Mouser |
| **AEM10941** | e-peas | 380mV (50mV warm) | 50mV–5V | Boost + dual LDO | 400nA | QFN-28 | ~$5.50 | DigiKey, Mouser |
| **AEM30940** | e-peas | 380mV | DC/RF/AC sources | Boost + dual LDO | 400nA | QFN-28 | ~$6.00 | DigiKey, Mouser |
| **ADP5090** | Analog Devices | 380mV (80mV warm) | 0.08–3.3V | Boost | 250nA | LFCSP-16 | ~$4.50 | DigiKey, Mouser |
| **LTC3105** | Analog Devices | 250mV | 0.225–5V | Step-up, 400mA | 24µA | DFN-12 | ~$5.50 | DigiKey, Mouser |
| **LTC3108** | Analog Devices | 20mV (TEG) | 20–500mV | Step-up + LDO | 6µA | DFN-12 | ~$5.50 | DigiKey, Mouser |
| **SPV1050** | STMicroelectronics | 150mV | 0.15–18V | Boost/buck + dual LDO | 800nA | QFN-20 | ~$3.50 | DigiKey, Mouser |
| **NEH7100** | Nexperia | Yes (spec TBD) | Solar/thermo | Boost + LDO + USB charge | Low | QFN | ~$3–4 | DigiKey, Mouser |
| **NEH7110** | Nexperia | Yes | Solar/thermo | Similar to NEH7100, I2C | Low | QFN | ~$3–4 | DigiKey, Mouser |

**Notes:**
- **BQ25570** remains the gold standard for µW harvesting. Massive ecosystem, tons of reference designs.
- **AEM10941** from e-peas has the lowest cold-start voltage (50mV warm start) — best for indoor PV.
- **Nexperia NEH71x0** is the newest entrant (2024–2025). Inductorless design reduces BOM cost. Watch this family.
- **LTC3108** is unique: works from 20mV, designed for thermoelectric generators (TEGs). Use the transformer ratio to set voltage.

### 10.2.2 Budget Solar Charger ICs

| IC | Manufacturer | Type | Chemistry | Max Current | Features | Package | Price | Source |
|----|-------------|------|-----------|------------|----------|---------|-------|--------|
| **CN3791** | Consonance | MPPT switching | 1S Li-ion | 4A | Adjustable MPPT via resistor, 4.5–6V solar | SOP-8 | ~$0.25 | LCSC |
| **CN3065** | Consonance | Linear solar charger | 1S Li-ion | 500mA | Simpler than CN3791, 4.4–6V | SOP-8 | ~$0.20 | LCSC |
| **CN3058E** | Consonance | Linear charger | 1S LiFePO4 | 800mA | LiFePO4 specific, 3.6V cutoff | SOP-8 | ~$0.25 | LCSC |
| **CN3058A** | Consonance | Linear charger | 1S LiFePO4 | 500mA | Budget LiFePO4 | SOP-8 | ~$0.20 | LCSC |
| **TP4056** | Various | Linear charger | 1S Li-ion | 1A | Ubiquitous, not solar-optimized | SOP-8 | ~$0.10 | LCSC, AliExpress |
| **TP5100** | Various | CC/CV charger | 1S/2S Li-ion | 2A | Dual-cell capable | SOP-8 | ~$0.30 | LCSC |

**Notes:**
- **CN3791** is the best budget solar charger IC. Real MPPT (not fake), dirt cheap. Pair with a 6V panel.
- **TP4056** modules ($0.50 on AliExpress) work for solar but have no MPPT — you're leaving power on the table.
- For LiFePO4, use CN3058E/A — the voltage profile is different from Li-ion.

---

## 10.3 Battery Charger ICs

| IC | Manufacturer | Type | Chemistry | Max I | Key Feature | Package | Price | Source |
|----|-------------|------|-----------|-------|-------------|---------|-------|--------|
| **MCP73831** | Microchip | Linear | 1S Li-ion | 500mA | Simplest possible, 5-pin | SOT-23-5 | ~$0.50 | DigiKey, Mouser |
| **MCP73833** | Microchip | Linear | 1S Li-ion | 1A | Status LEDs, DFN-10 | DFN-10 | ~$0.80 | DigiKey, Mouser |
| **BQ24074** | Texas Instruments | Linear, power path | 1S Li-ion | 1.5A | USB+solar input, DPPM | QFN-24 | ~$3.00 | DigiKey, Mouser |
| **BQ25185** | Texas Instruments | Linear, power path | Li-ion/LiFePO4 | 1A | **NEW 2024**. Ship mode, solar-friendly, tiny WCSP | WCSP-9 | ~$1.50 | DigiKey, Mouser |
| **BQ25895** | Texas Instruments | Buck-boost | 1S Li-ion | 3A | USB PD, I2C, narrow VDC | QFN-24 | ~$4.00 | DigiKey, Mouser |
| **BQ25180** | Texas Instruments | Linear | 1S Li-ion | 1A | Ultra-low Iq (5.2µA), I2C | DSBGA-9 | ~$1.30 | DigiKey, Mouser |
| **LTC4162-L** | Analog Devices | Buck (switching) | 1–4S Li-ion | 3.2A | MPPT, I2C, multi-cell | QFN-28 | ~$8.00 | DigiKey, Mouser |
| **LTC4162-F** | Analog Devices | Buck (switching) | 1–4S LiFePO4 | 3.2A | Same as above, LiFePO4 profile | QFN-28 | ~$8.00 | DigiKey, Mouser |
| **DW01A + FS8205** | Various | Protection only | 1S Li-ion | — | OVP/UVP/OCP combo | SOT-23-6 each | ~$0.10 total | LCSC |

**Notes:**
- **BQ25185** is the hot new chip for 2024–2025. Adafruit already has boards for it. Supports Li-ion AND LiFePO4 via pin selection. Near-MPPT solar input without external MPPT circuitry.
- **BQ25180** is great for ultra-low-power: 5.2µA Iq vs ~100µA for typical chargers.
- **LTC4162** is the serious multi-cell solar charger — real MPPT, I2C telemetry, up to 4S packs.

---

## 10.4 LDOs & Regulators (Ultra-Low Iq)

| IC | Manufacturer | Iq | Iout Max | Vin Range | Vout | Dropout | Package | Price | Source |
|----|-------------|-----|---------|-----------|------|---------|---------|-------|--------|
| **TPS7A02** | Texas Instruments | **25nA** | 200mA | 1.1–6.5V | Fixed (many options) | 300mV | SOT-23-5, DSBGA | ~$0.80 | DigiKey, Mouser |
| **TPS7A05** | Texas Instruments | **100nA** | 200mA | 1.1–6.5V | Adj (0.8–5.5V) | 300mV | SOT-23-5 | ~$0.75 | DigiKey, Mouser |
| **AP2112** | Diodes Inc. | 55µA | 600mA | 2.5–6V | 3.3V (or adj) | 250mV | SOT-23-5 | ~$0.25 | DigiKey, Mouser, LCSC |
| **MCP1700** | Microchip | 1.6µA | 250mA | 2.3–6V | 3.3V / 1.8V | 178mV | SOT-23-3 | ~$0.35 | DigiKey, Mouser |
| **MCP1702** | Microchip | 2µA | 250mA | 2.7–13.2V | 3.3V / 5V | 625mV | SOT-23-3, SOT-89 | ~$0.40 | DigiKey, Mouser |
| **RT9080** | Richtek | 2µA | 600mA | 1.2–5.5V | Adj/fixed | 300mV | SOT-23-5, DFN | ~$0.30 | DigiKey, LCSC |
| **HT7333** | Holtek | 4µA | 250mA | 3.6–12V | 3.3V | 350mV | SOT-89, TO-92 | ~$0.15 | LCSC, AliExpress |
| **XC6220** | Torex | 8µA | 700mA | 1.0–6V | Fixed (many) | 200mV | SOT-25, USP | ~$0.50 | DigiKey, Mouser |
| **TLV70233** | Texas Instruments | 28µA | 300mA | 2.5–24V | 3.3V | 300mV | SOT-23-5 | ~$0.45 | DigiKey, Mouser |
| **ME6211** | Micro One | 40µA | 500mA | 2–6V | 3.3V | 100mV | SOT-23-5 | ~$0.08 | LCSC |

**Notes:**
- **TPS7A02** at 25nA is the king of low quiescent current. Perfect for always-on solar systems.
- **AP2112** is the budget workhorse — used on countless dev boards (ESP32, etc.).
- **HT7333** and **ME6211** are the go-to ultra-budget options from LCSC.

---

## 10.5 Load Switches

| IC | Manufacturer | Ron | Vin | Iq (off) | Imax | Key Feature | Package | Price | Source |
|----|-------------|-----|-----|----------|------|-------------|---------|-------|--------|
| **TPS22860** | Texas Instruments | 0.73Ω | 1.05–5.5V | **2pA off** | 200mA | Ultra-low leakage, active-low | 6-DSBGA | ~$1.00 | DigiKey, Mouser |
| **TPS22918** | Texas Instruments | 52mΩ | 1.62–5.5V | 1µA off | 2A | General purpose, fast, active-high | SOT-23-5 | ~$0.40 | DigiKey, Mouser |
| **SiP32431** | Vishay | 100mΩ | 1.5–5.5V | **10nA off** | 1.4A | Ultra-low leakage, slew control | SC70-5 | ~$0.50 | DigiKey, Mouser |
| **SiP32432** | Vishay | 100mΩ | 1.5–5.5V | 10nA off | 1.4A | Same, active-low EN | SC70-5 | ~$0.50 | DigiKey, Mouser |
| **MAX16150** | Analog Devices | — | 1.7–5.5V | 300nA | — | Pushbutton on/off controller + debounce | WLP-6 | ~$1.20 | DigiKey, Mouser |
| **NCP45560** | onsemi | 6.5mΩ | 0.8–5.5V | 1µA off | 6A | High current, adjustable slew | WDFN-6 | ~$0.35 | DigiKey, Mouser |

**Notes:**
- **TPS22860** has 2 picoamps of off-state leakage. Use it to completely disconnect power-hungry peripherals.
- **SiP32431** is popular in maker designs for its simplicity and low leakage.
- **MAX16150** is a pushbutton controller, not a pure switch — great for on/off button with clean debounce.

---

## 10.6 Supercapacitors

| Part Number | Manufacturer | Capacitance | Voltage | ESR | Size | Price | Source |
|-------------|-------------|------------|---------|-----|------|-------|--------|
| **CPH3225A** | Seiko | 11mF | 3.3V | ~70Ω | Coin 3.2×2.5mm | ~$2.50 | DigiKey, Mouser |
| **DMF3Z5R5H474M3DTA0** | Murata/CAP-XX | 470mF | 5.5V | 45mΩ | 21×14×3.2mm SMD | ~$5.00 | DigiKey, Mouser |
| **DMH3Z5R5H105M3RTA0** | Murata | 1F | 5.5V | 30mΩ | 21×14×5mm | ~$7.00 | DigiKey |
| **HV0810-2R7105-R** | Eaton | 1F | 2.7V | 200mΩ | 8×10mm | ~$2.00 | DigiKey, Mouser |
| **HV1030-2R7105-R** | Eaton | 1F | 2.7V | 120mΩ | 10×30mm cyl. | ~$2.50 | DigiKey |
| **HS0830-2R7104-R** | Eaton | 100mF | 2.7V | 2Ω | 8×9mm | ~$1.30 | DigiKey |
| **SCCR12B105PRB** | Kyocera AVX | 1F | 2.7V | 150mΩ | 12mm radial | ~$1.80 | DigiKey, Mouser |
| **SCCR20B505PRB** | Kyocera AVX | 5F | 2.7V | 80mΩ | 20mm radial | ~$3.00 | DigiKey |
| **SCCS20E505PRBLE** | Kyocera AVX | 5F | 2.7V | 100mΩ | 20mm SMD | ~$4.00 | DigiKey |
| **SCCS30E106PRB** | Kyocera AVX | 10F | 2.7V | 50mΩ | 30mm SMD | ~$6.00 | DigiKey |
| **KR-5R5V105-R** | Eaton | 1F | 5.5V (2S) | 300mΩ | 19×18mm | ~$2.50 | DigiKey, Mouser |
| **KR-5R5V155-R** | Eaton | 1.5F | 5.5V (2S) | 250mΩ | 19×21mm | ~$3.00 | DigiKey |

**Notes:**
- **5.5V-rated** caps are internally two 2.7V cells in series — convenient for 3.3V/5V rails but lower effective capacitance.
- Murata DMF/DMH series are premium thin-profile (3–5mm) SMD supercaps — ideal for PCB-mount energy harvesting.
- For energy math: E = ½CV². A 1F cap at 2.7V stores ~3.6J ≈ 1mWh — enough for a few BLE transmissions.

---

## 10.7 MPPT Controllers & Solar Charger Boards

### 10.7.1 Tiny / IoT Boards

| Board | IC | Input | Output | Battery | Features | Price | Source |
|-------|-----|-------|--------|---------|----------|-------|--------|
| **Adafruit BQ25185 Solar Charger** | BQ25185 | 5–18V DC/solar, USB-C | Power path to load | 1S Li-ion/LiFePO4 | **NEW 2024**. Near-MPPT solar, USB-C, I2C | ~$8 | Adafruit (#6091) |
| **Adafruit BQ25185 + 3.3V Buck** | BQ25185 + TPS62840 | Same | 3.3V regulated | 1S Li-ion/LiFePO4 | Same + 3.3V buck | ~$10 | Adafruit |
| **Adafruit BQ25185 + 5V Boost** | BQ25185 + TPS61023 | Same | 5V regulated | 1S Li-ion | Same + 5V boost | ~$10 | Adafruit |
| **Adafruit Solar Charger BFF** | BQ25185 | 6V solar | 3.3V | 1S LiPo | Tiny, for QT Py / XIAO | ~$8 | Adafruit |
| **Adafruit Universal USB-C Charger** | BQ24074 | USB-C 5V | 5V power path | 1S LiPo | Power path, 1.5A charge | ~$15 | Adafruit (#4410) |
| **SparkFun Sunny Buddy** | LT3652 | 6–20V solar | Charge only | 1S LiPo | Real MPPT, 450mA | ~$25 | SparkFun (PRT-12885) |
| **SparkFun LiPo Charger Plus** | MCP73831 | USB-C or solar | Charge only | 1S LiPo | Simple, LED status | ~$8 | SparkFun |
| **DFRobot Solar PM 5V** | CN3065 | 4.4–6V solar | 5V/1A | 1S LiPo | Compact, basic | ~$9 | DFRobot (#DFR0559) |
| **DFRobot Micro Solar PM** | ADP5090 | 1–3V solar | 3.3V/5V | 1S LiPo | µW harvesting, incl. 2V panel | ~$12 | DFRobot (#DFR0579) |
| **DFRobot Solar PM (9V/12V/18V)** | CN3767 | 9–18V solar | 5V/1A | 1S LiPo | Larger panels | ~$10 | DFRobot (#DFR0580) |
| **Soldered CN3791 Board** | CN3791 | 6V solar | Charge only | 1S Li-ion | Open-source, MPPT, EU-made | ~$8 | Soldered.com |
| **CN3791 module (generic)** | CN3791 | 6V solar | Charge only | 1S Li-ion | Budget MPPT | ~$1–2 | AliExpress |
| **TP4056 module (generic)** | TP4056 | 5V USB/solar | Charge only | 1S Li-ion | Ubiquitous, no MPPT | ~$0.50 | AliExpress |

### 10.7.2 Pi-Class Boards

| Board | Input | Output | Battery | Features | Price | Source |
|-------|-------|--------|---------|----------|-------|--------|
| **Waveshare Solar PM** | 6V solar | 5V/1A | 1S LiPo | Basic, passthrough | ~$10 | Waveshare, Amazon |
| **Waveshare Solar PM (D)** | 6–24V solar | 5V/3A, PD/QC | 1S LiPo | MPPT, powers Pi, USB-C | ~$22 | Waveshare, Amazon |
| **Waveshare Solar PM (B)** | 6–24V solar | 5V/3A | 1S/2S LiPo, built-in 10Ah | MPPT, powers Pi/Jetson | ~$28 | Waveshare |
| **Waveshare Solar PM (C)** | 6–24V solar | 5V/3A | 1S LiPo (external) | USB-C out, PD | ~$20 | Waveshare |
| **PiJuice HAT** | Solar via PiJuice panel | 5V (HAT) | 1S LiPo (onboard) | RTC, wake scheduler, I2C API | ~$55 | Pi Hut, Pimoroni |
| **PiSugar 3 Plus** | 5V USB-C | 5V (pogo pins) | 1S LiPo (5000mAh) | RTC, web UI, fits Pi form | ~$40 | PiSugar, Amazon |

### 10.7.3 Standalone MPPT Charge Controllers (12V+ Systems)

| Controller | Input | Battery | Current | Features | Price | Source |
|-----------|-------|---------|---------|----------|-------|--------|
| **Victron SmartSolar 75/10** | 12–75V | 12V lead/LiFePO4 | 10A | Bluetooth, app, VE.Direct | ~$80 | Amazon, Victron dealers |
| **Genasun GV-5** | 12–25V | 12V LiFePO4/Li-ion | 5A | Tiny, efficient, set-and-forget | ~$100 | Genasun |
| **EPEver Tracer 1210AN** | 12–100V | 12/24V | 10A | Budget, RS485 | ~$30 | Amazon, AliExpress |
| **EPEver Tracer 2210AN** | 12–100V | 12/24V | 20A | Popular mid-range | ~$50 | Amazon, AliExpress |

---

## 10.8 Evaluation Boards & Dev Kits

| Board | IC | Included | Price | Source |
|-------|-----|---------|-------|--------|
| **BQ25570EVM-206** | BQ25570 | Full eval board with headers | ~$49 | TI, DigiKey, Mouser |
| **BQ25504EVM** | BQ25504 | Full eval board | ~$49 | TI, DigiKey |
| **BQ25185EVM** | BQ25185 | Charger eval board | ~$29 | TI, DigiKey |
| **AEM10941 eval (Tindie)** | AEM10941 | Breakout by Jasper Sikken | ~$25 | Tindie |
| **AEM10941 eval (e-peas)** | AEM10941 | Official eval with antennas | ~$80 | e-peas |
| **AEM30940 eval** | AEM30940 | Multi-source eval | ~$80 | e-peas |
| **STEVAL-ISV006V2** | SPV1050 | Solar eval with supercap | ~$35 | ST, DigiKey |
| **DC1587A** | LTC3105 | Solar step-up demo | ~$60 | Analog Devices |
| **DC2042A** | LTC3108 + others | Multi-harvester demo | ~$100 | Analog Devices |
| **ADP5090-2-EVALZ** | ADP5090 | Solar boost eval | ~$50 | DigiKey, Mouser |
| **NEVB-NEH7100** | NEH7100 | Full eval, Nexperia | ~$30 | Nexperia, DigiKey |
| **DEV-BLE-TI** | BQ25570 + CC2650 | PowerFilm indoor solar + BLE | ~$99 | DigiKey |

---

## 10.9 Batteries

### 10.9.1 Small LiPo Cells (for Solar IoT)

| Capacity | Dimensions | Connector | Price | Source | Notes |
|----------|-----------|-----------|-------|--------|-------|
| **100mAh** | 28×18×4mm | JST-PH 2-pin | ~$6 | Adafruit (#1570) | Tiny, good for BLE beacons |
| **150mAh** | 24×20×5mm | JST-PH 2-pin | ~$6 | Adafruit (#1317) | Common in wearable builds |
| **350mAh** | 36×24×5mm | JST-PH 2-pin | ~$7 | Adafruit (#2750) | Sweet spot for Feather boards |
| **400mAh** (Feather) | 35×25×5mm | JST-PH 2-pin | ~$7 | Adafruit (#3898) | Designed for Feather form factor |
| **500mAh** | 36×28×6mm | JST-PH 2-pin | ~$8 | Adafruit (#1578), SparkFun | Good all-around |
| **1200mAh** | 51×34×6mm | JST-PH 2-pin | ~$10 | Adafruit (#258) | Popular for Pi Zero projects |
| **2000mAh** | 60×36×7mm | JST-PH 2-pin | ~$13 | Adafruit (#2011), SparkFun | Full-day Pi Zero buffer |
| **2500mAh** | 55×37×8mm | JST-PH 2-pin | ~$15 | Adafruit (#328) | |
| **4400mAh** | 60×50×8mm | JST-PH 2-pin | ~$20 | Adafruit (#354) | Multi-day buffer |
| **6600mAh** | 62×50×12mm | JST-PH 2-pin | ~$25 | Adafruit (#353) | Maximum single-cell |

**Notes:**
- Adafruit and SparkFun LiPos use standard JST-PH connectors and include protection circuits. Worth the premium over bare AliExpress cells.
- Always match the battery capacity to your panel: a 1W panel generates ~170mAh/day (peak sun hours vary). Don't over-size.

### 10.9.2 LiFePO4 Cells

| Type | Voltage | Capacity | Size | Price | Source | Notes |
|------|---------|----------|------|-------|--------|-------|
| **IFR14500** (AA-size) | 3.2V | 400–600mAh | AA | ~$2–4/ea | eBay, Amazon, ExellBattery | Drop-in for solar garden lights |
| **IFR18650** | 3.2V | 1500mAh | 18650 | ~$5–8/ea | Amazon, AliExpress | Cycle life >2000 |
| **IFR26650** | 3.2V | 3000–3300mAh | 26650 | ~$8–12/ea | Amazon | Good for permanent solar |
| **TalentCell 12V 3Ah** | 12.8V (4S) | 3Ah | Pack with BMS | ~$30 | Amazon | USB + barrel, integrated BMS |
| **TalentCell 12V 6Ah** | 12.8V (4S) | 6Ah | Pack with BMS | ~$45 | Amazon | Great for Pi + panel setups |

**Notes:**
- LiFePO4 is superior to Li-ion for permanent solar: wider temperature range (−20°C to +60°C), 2000–5000 cycle life, safer chemistry.
- IFR14500 AA cells are perfect for solar garden light hacks and simple outdoor sensors.

### 10.9.3 Coin Cells & Holders

| Cell | Voltage | Capacity | Price | Notes |
|------|---------|----------|-------|-------|
| **CR2032** | 3V | 220mAh | ~$0.30/ea | Standard, not rechargeable |
| **LIR2032** | 3.6V | 40mAh | ~$1.50/ea | Rechargeable Li-ion coin cell |
| **ML2032** | 3V | 65mAh | ~$3/ea | Rechargeable LiMnO2 (Panasonic) |
| **CR2032 holder (PTH)** | — | — | ~$0.30 | Keystone #3034 or similar |
| **CR2032 holder (SMD)** | — | — | ~$0.40 | Keystone #3002 or Linx BAT-HLD-012 |

---

## 10.10 Microcontrollers & SBCs (Solar-Optimized)

### 10.10.1 MCU Boards for Solar IoT

| Board | MCU | Deep Sleep | Connectivity | Solar Features | Price | Source |
|-------|-----|-----------|-------------|----------------|-------|--------|
| **DFRobot FireBeetle ESP32-E** | ESP32-E | ~10µA | Wi-Fi, BLE | Built-in charge IC, low sleep | ~$7 | DFRobot, DigiKey |
| **Seeed XIAO ESP32-S3** | ESP32-S3 | ~14µA | Wi-Fi, BLE | Tiny (21×17mm), battery pad | ~$5 | Seeed |
| **Seeed XIAO ESP32-C6** | ESP32-C6 | ~7µA | Wi-Fi 6, BLE, Thread/Zigbee | **NEW**. Thread support, tiny | ~$5 | Seeed |
| **Seeed XIAO nRF52840** | nRF52840 | ~5µA | BLE 5.0 | Ultra-low power, tiny | ~$5 | Seeed |
| **RAK4631 (WisBlock Core)** | nRF52840 + SX1262 | ~2µA | BLE + LoRa | Modular system, best LoRa solar | ~$20 | RAKwireless |
| **Heltec WiFi LoRa 32 V3** | ESP32-S3 + SX1262 | ~20µA | Wi-Fi, BLE, LoRa | OLED, battery connector | ~$16 | Heltec, AliExpress |
| **LILYGO T-Beam** | ESP32 + SX1262 + GPS | ~20µA | Wi-Fi, BLE, LoRa, GPS | Meshtastic popular | ~$28 | LILYGO, AliExpress |
| **Adafruit Feather nRF52840** | nRF52840 | ~5µA | BLE 5.0 | LiPo charger built-in | ~$25 | Adafruit |
| **Adafruit Feather ESP32-S3** | ESP32-S3 | ~10µA | Wi-Fi, BLE | LiPo charger, STEMMA QT | ~$18 | Adafruit |
| **Arduino Nano ESP32** | ESP32-S3 | ~10µA | Wi-Fi, BLE | Arduino ecosystem | ~$20 | Arduino, DigiKey |
| **Blues Swan** | STM32L4R5 | ~2µA | None (add Notecard) | Ultra-low-power STM32, Feather form | ~$30 | Blues.com |

### 10.10.2 Single-Board Computers

| Board | Idle Power | Solar Feasibility | Price | Notes |
|-------|-----------|-------------------|-------|-------|
| **Raspberry Pi Zero 2 W** | 0.4–0.5W | ✅ 2W panel + battery | ~$15 | Best Pi for solar |
| **Raspberry Pi 4B (2GB)** | 3–4W | ⚠️ 20W panel + big battery | ~$35 | Gateway/heavier compute |
| **Raspberry Pi 5** | 3–5W idle, 25W peak | ❌ Needs 30W+ panel | ~$60–80 | Not great for solar |
| **Olimex A20-OLinuXino** | 1–2W | ✅ Used by LOW←TECH MAGAZINE | ~$50 | Proven solar server |
| **Orange Pi Zero 2W** | 0.5–1W | ✅ Budget Pi Zero alt | ~$18 | H618, decent Linux support |
| **Milk-V Duo (RISC-V)** | 0.3W | ✅ Tiny Linux, ultra low power | ~$5 | Experimental, Linux + RTOS |

---

## 10.11 Sensors (Low-Power, Solar-Friendly)

| Sensor | Measures | I2C/SPI | Active I | Sleep I | Price | Notes |
|--------|----------|---------|----------|---------|-------|-------|
| **BME280** (Bosch) | Temp, humidity, pressure | I2C/SPI | 340µA | 0.1µA | ~$4 | Default weather sensor |
| **BME680** (Bosch) | Temp, humid, press, VOC | I2C/SPI | 12mA (gas) | 0.15µA | ~$10 | Air quality index |
| **SHT40** (Sensirion) | Temp, humidity | I2C | 340µA | 0.08µA | ~$3 | More accurate than BME280 humidity |
| **SHT45** (Sensirion) | Temp, humidity | I2C | 340µA | 0.04µA | ~$5 | Newest, best accuracy |
| **BMP390** (Bosch) | Pressure, temp | I2C/SPI | 700µA | 0.3µA | ~$5 | ±3Pa altitude precision |
| **TSL2591** (AMS) | Ambient light | I2C | 230µA | 0.5µA | ~$6 | 600M:1 dynamic range |
| **VEML7700** (Vishay) | Ambient light (lux) | I2C | 45µA | 0.5µA | ~$3 | Simpler, lower power |
| **INA219** (TI) | Current, voltage, power | I2C | 1mA | 6µA | ~$2 | Solar panel monitoring |
| **INA226** (TI) | Current, voltage, power | I2C | 330µA | 2.5µA | ~$3 | Higher precision, 16-bit |
| **MAX17048** (Analog) | LiPo fuel gauge | I2C | 23µA | 2µA | ~$3 | SOC without coulomb counting |
| **DS18B20** (Maxim) | Temperature | 1-Wire | 1mA | 750nA | ~$2 | Waterproof probe versions |
| **SEN-15901** (SparkFun) | Weather station kit | Pulse/analog | — | — | ~$75 | Anemometer + vane + rain gauge |

---

## 10.12 Distributors & Marketplaces

| Distributor | Strengths | Min Order | Ship From | Notes |
|-------------|-----------|-----------|-----------|-------|
| **DigiKey** | Massive catalog, fast ship, datasheets | No min | USA (Thief River Falls, MN) | Best for ICs and passives |
| **Mouser** | Similar to DigiKey, strong TI/ADI/ST stock | No min | USA (Mansfield, TX) | Often better prices on niche ICs |
| **LCSC** | Chinese components, incredibly cheap | $2 min | China (Shenzhen) | Best for CN3791, TP4056, budget parts |
| **Adafruit** | Curated breakout boards, excellent docs | No min | USA (NYC) | Premium but well-documented |
| **SparkFun** | Similar to Adafruit, Qwiic ecosystem | No min | USA (Boulder, CO) | Good tutorials |
| **DFRobot** | Maker boards, sensors, solar managers | No min | China (or local) | FireBeetle, Gravity sensors |
| **Seeed Studio** | XIAO boards, Grove ecosystem, Fusion PCB | No min | China (Shenzhen) | XIAO is the best tiny MCU |
| **RAKwireless** | WisBlock LoRa ecosystem | No min | China (Shenzhen) | Best modular LoRa platform |
| **Pimoroni** | Pi accessories, Enviro boards | No min | UK (Sheffield) | PiJuice, quality Pi HATs |
| **Voltaic Systems** | Rugged small solar panels, battery packs | No min (1pc) | USA (Brooklyn, NY) | Best quality small panels |
| **PowerFilm Solar** | Flexible/indoor solar cells | Contact for some | USA (Ames, IA) | Indoor Light Series, flex cells |
| **Farnell/Newark** | Similar to DigiKey, Epishine stocked | No min | USA/UK/EU | Good for EU sourcing |
| **AliExpress** | Everything, rock-bottom prices | ~$1 min | China | Variable quality, verify specs |
| **Amazon** | Convenience, fast ship | No min | Varies | Good for Renogy, BigBlue, batteries |
| **Tindie** | Community maker boards & breakouts | No min | Various | e-peas breakouts, niche boards |

---

## 10.13 Key Datasheets & Application Notes

| Document | Description | Source |
|----------|-------------|--------|
| **BQ25570 datasheet** (SLUSBH2) | Energy harvesting IC reference | ti.com/lit/ds/symlink/bq25570.pdf |
| **BQ25570 reference design** (SLUC484) | Complete harvesting circuit | ti.com |
| **BQ25185 datasheet** (SLUSE35) | New solar-friendly charger IC | ti.com/product/BQ25185 |
| **AEM10941 datasheet** | e-peas ambient energy manager | e-peas.com |
| **AEM30940 datasheet** | Multi-source harvester | e-peas.com |
| **NEH7100 datasheet** | Nexperia inductorless PMIC | nexperia.com/product/NEH7100BU |
| **CN3791 datasheet** | Budget solar MPPT charger | consonance-elec.com |
| **LTC4162 datasheet** | Multi-cell solar charger | analog.com/en/products/ltc4162-l.html |
| **TPS7A02 datasheet** | 25nA Iq LDO reference | ti.com/product/TPS7A02 |
| **AN-1271** (TI) | Selecting solar cells for energy harvesting | ti.com |
| **DN-522** (Analog) | LTC3105 solar application note | analog.com |
| **ESP32 power consumption** | Sleep mode reference | espressif.com |
| **nRF52840 power profiling** | Nordic power analysis | nordicsemi.com |

---

## 10.14 Open-Source Hardware & Reference Designs

| Project | Description | License | Link |
|---------|-------------|---------|------|
| **Solar Protocol** | Distributed solar server network | Open source | github.com/alexnathanson/solar-protocol |
| **SoLoRa** | Solar LoRa IoT node | Open hardware | hackaday.io/project/159658 |
| **OpenEnergyMonitor** | Energy monitoring platform | GPL | openenergymonitor.org |
| **MPPT Solar Charger** (Libre Solar) | Open-source MPPT charge controller | CC BY-SA | libre.solar |
| **RAK WisBlock** | Modular IoT platform (schematics available) | Open hardware | docs.rakwireless.com |
| **Meshtastic** | LoRa mesh firmware | GPL-3.0 | meshtastic.org |
| **ESP-IDF examples** | Espressif IoT framework examples | Apache-2.0 | github.com/espressif/esp-idf |
| **Soldered CN3791 board** | Open-source MPPT breakout | Open hardware | soldered.com |

---

## 10.15 Quick-Reference: What to Buy for Each Tier

### Wearable / µW Tier
- **Panel:** Anysolar KXOB25-02X8F (~$3.50) or Panasonic AM-1815CA for indoor (~$6)
- **PMIC:** BQ25570 (~$4.50) or AEM10941 (~$5.50)
- **Storage:** Seiko CPH3225A supercap (~$2.50) or 100mAh LiPo (~$6)
- **MCU:** Seeed XIAO nRF52840 (~$5)
- **Regulator:** TPS7A02 3.3V (~$0.80)
- **Total BOM:** ~$15–25

### IoT / mW–W Tier
- **Panel:** Voltaic P126 2W (~$17) or Seeed 6V 2W (~$5)
- **Charger:** CN3791 module (~$2) or Adafruit BQ25185 board (~$8)
- **Battery:** 500mAh LiPo (~$8) or 1200mAh (~$10)
- **MCU:** Seeed XIAO ESP32-S3 (~$5) or RAK4631 for LoRa (~$20)
- **Regulator:** AP2112 3.3V (~$0.25)
- **Total BOM:** ~$25–50

### Pi / Multi-W Tier
- **Panel:** Voltaic P105 5.5W (~$29) or Renogy 21W foldable (~$50)
- **Controller:** Waveshare Solar PM (D) (~$22) or Victron 75/10 (~$80)
- **Battery:** TalentCell 12V 6Ah LiFePO4 (~$45) or 2000mAh LiPo (~$13)
- **SBC:** Raspberry Pi Zero 2 W (~$15) or Orange Pi Zero 2W (~$18)
- **Total BOM:** ~$75–170

### Permacomputing / Always-On Server
- **Panel:** 30–50W rigid or foldable (~$40–100)
- **Controller:** Victron SmartSolar 75/10 (~$80) or EPEver Tracer 1210AN (~$30)
- **Battery:** 12V 20Ah+ LiFePO4 pack (~$80–150)
- **SBC:** Olimex A20-OLinuXino (~$50) or Pi Zero 2 W (~$15)
- **Total BOM:** ~$200–400

---

**Key takeaway:** The solar embedded ecosystem is mature and accessible. Between Voltaic/PowerFilm panels, TI/e-peas/Nexperia harvesting ICs, and ESP32/nRF52 platforms, you can source every component for a solar IoT project from reputable distributors with datasheets. For prototyping, start with breakout boards (Adafruit, SparkFun, DFRobot). For production, move to LCSC/DigiKey for component-level sourcing at volume pricing. The BQ25185 (2024) and Nexperia NEH71x0 (2025) represent the newest generation — watch these families.
