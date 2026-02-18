# Chapter 6: System Design Patterns

This is the integration chapter. We combine panels (Ch.2), harvesting ICs (Ch.3), batteries (Ch.4), and regulators (Ch.5) into complete, working system designs for each power tier.

## 6.1 Design Methodology

For every solar-powered system, follow this sequence:

```
1. Define the load → What does the system do? How often?
2. Energy budget → How many mWh/day does it consume?
3. Size the battery → How many dark days must it survive?
4. Size the panel → How much energy must be harvested per day?
5. Select the power chain → Harvesting IC, charger, regulators, switches
6. Validate → Does worst-case harvest > worst-case consumption?
```

### Energy Budget Template

```
┌──────────────────┬──────────┬──────────┬───────────┬────────────┐
│ Mode             │ Current  │ Voltage  │ Duration  │ Energy     │
├──────────────────┼──────────┼──────────┼───────────┼────────────┤
│ Deep sleep       │ __ µA    │ __ V     │ __ s/day  │ __ mWh     │
│ Wake + sensor    │ __ mA    │ __ V     │ __ s/day  │ __ mWh     │
│ Radio TX         │ __ mA    │ __ V     │ __ s/day  │ __ mWh     │
│ Radio RX         │ __ mA    │ __ V     │ __ s/day  │ __ mWh     │
│ Processing       │ __ mA    │ __ V     │ __ s/day  │ __ mWh     │
├──────────────────┼──────────┼──────────┼───────────┼────────────┤
│ TOTAL            │          │          │           │ __ mWh/day │
└──────────────────┴──────────┴──────────┴───────────┴────────────┘
```

## 6.2 Tier 1: µW Wearable Solar Harvesting

### Use Case
BLE beacon, health sensor patch, or smart textile. Harvests from small flexible cell worn on clothing or wristband. Operates indefinitely in indoor/outdoor light.

### Architecture

```
┌─────────────┐    ┌──────────┐    ┌──────────────┐    ┌──────────┐
│ Flex Solar   │───→│ BQ25570  │───→│ 100mF–470mF  │───→│ nRF52832 │
│ KXOB25-04X3F│    │ Boost +  │    │ Supercap      │    │ BLE MCU  │
│ 3V / 50mA   │    │ MPPT     │    │ (2.5V rated)  │    │          │
└─────────────┘    │          │───→│ or 50mAh      │    └──────────┘
                   │ VOUT_BUCK│    │ thin-film LiPo │
                   │ 1.8V     │    └──────────────┘
                   └──────────┘
```

### Energy Budget

| Mode | Current | Voltage | Duration/day | Energy/day |
|------|---------|---------|-------------|-----------|
| System OFF (BQ25570 Iq) | 488 nA | 1.8V | 86,360 s | 0.021 mWh |
| BLE advertising (nRF52832) | 7 mA | 1.8V | 40 s (1s TX every 2s, 2160×/day × 18.5ms) | 0.14 mWh |
| Sensor read (accelerometer) | 1 mA | 1.8V | 10 s | 0.005 mWh |
| **Total** | | | | **~0.17 mWh/day** |

### Harvest Estimate

KXOB25-04X3F in outdoor shade/indirect light (~200 W/m², ~4 hours equivalent):
```
Panel output at 200 W/m²: ~20mW × 0.2 (fraction of STC) = ~4 mW average during sun
Harvest: 4 mW × 4 h = 16 mWh/day
System needs: 0.17 mWh/day
Margin: ~94× — can survive mostly indoors
```

Indoor (500 lux office, 8 hours):
```
Panel at 500 lux: ~50 µW
Harvest: 0.050 mW × 8 h = 0.4 mWh/day
Margin: ~2.4× — works, but tight. Reduce BLE rate for safety.
```

### Component BOM

| Component | Part Number | Description | Qty | Price (1pc) |
|-----------|-------------|-------------|-----|-------------|
| Solar cell | ANYSOLAR KXOB25-04X3F | 3V 50mA mono-Si, 25×37mm | 1 | ~$3.50 |
| Harvesting IC | TI BQ25570RGRR | Boost MPPT + buck, QFN-20 | 1 | ~$4.50 |
| Supercapacitor | Eaton HV0810-2R7105-R | 1F 2.7V, 8×10mm | 1 | ~$2.00 |
| MCU | Nordic nRF52832-QFAA | BLE 5.0 SoC, QFN-48 | 1 | ~$3.50 |
| Antenna | 2.4GHz chip antenna | e.g., Johanson 2450AT | 1 | ~$0.30 |
| Passives | Resistors for MPPT, OV/UV | 0402/0603 | ~8 | ~$0.50 |
| Capacitors | MLCC 1µF, 4.7µF, 10µF | 0402/0603 X5R | ~6 | ~$0.50 |
| **Total BOM** | | | | **~$15** |

### Design Notes
- BQ25570 MPPT ratio: set to 0.78 × V_oc via ROC1/ROC2 resistors
- OV threshold: 2.5V (supercap rating); UV threshold: 1.8V (MCU minimum)
- VBAT_OK signal → nRF52 interrupt pin: wakes MCU only when energy is sufficient
- No LDO needed: BQ25570's integrated buck provides 1.8V directly
- Consider AEM10941 instead of BQ25570 for even lower cold-start (3µW vs 15µW)

---

## 6.3 Tier 2: mW–W IoT Sensor Node

### Use Case
Outdoor environmental monitor: temperature, humidity, barometric pressure, transmitted every 15 minutes via LoRa or WiFi. Runs year-round, unattended.

### Architecture A: ESP32 + WiFi (Higher Power)

```
┌──────────────┐    ┌──────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────────┐
│ 6V 2W Panel  │───→│ CN3791   │───→│ 1S LiFePO4   │───→│ MCP1700  │───→│ ESP32-S3     │
│ Voltaic P106 │    │ MPPT     │    │ IFR14500     │    │ 3.3V LDO │    │ + BME280     │
│              │    │ Charger  │    │ 600mAh       │    │ 1.6µA Iq │    │ + WiFi TX    │
└──────────────┘    └──────────┘    └──────────────┘    └──────────┘    └──────────────┘
```

### Energy Budget (ESP32 WiFi, 15-min interval)

| Mode | Current @ 3.3V | Duration/cycle | Cycles/day | Energy/day |
|------|----------------|---------------|------------|-----------|
| Deep sleep (ESP32 + BME280) | 10 µA | 895 s | 96 | 0.83 mWh |
| Wake + sensor read | 40 mA | 0.5 s | 96 | 1.76 mWh |
| WiFi connect + TX | 180 mA | 3 s | 96 | 14.26 mWh |
| WiFi RX (ACK) | 95 mA | 1.5 s | 96 | 4.49 mWh |
| **Total** | | | | **21.3 mWh/day** |

**Average current: 21.3 mWh ÷ 24h ÷ 3.3V = 0.27 mA average**

### Panel & Battery Sizing

```
Location: New York, winter worst case: 2 PSH
System efficiency: 0.58 (MPPT × charge × regulator × derating)

Panel needed: 21.3 mWh / (2h × 0.58) = 18.4 mW at STC
→ 2W panel provides 108× margin. Very comfortable.
→ Could use 1W panel (54× margin) — still excellent.

Battery: 3 days autonomy
  21.3 mWh × 3 / (3.2V × 0.9 DoD × 0.9 efficiency) = 24.6 mAh
→ IFR14500 (600 mAh) provides 24× margin. Handles degradation + cold.
```

### Architecture B: nRF52840 + LoRa (Lower Power)

```
┌──────────────┐    ┌──────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────────────────┐
│ 6V 1W Panel  │───→│ CN3791   │───→│ 1S LiFePO4   │───→│ TPS7A02  │───→│ nRF52840             │
│ Voltaic P105 │    │ MPPT     │    │ IFR14500     │    │ 3.3V LDO │    │ + BME280             │
│              │    │ Charger  │    │ 600mAh       │    │ 25nA Iq  │    │ + RFM95W (LoRa)      │
└──────────────┘    └──────────┘    └──────────────┘    └──────────┘    └──────┬───────────────┘
                                                                               │
                                                                    TPS22860 load switch
                                                                    (gates LoRa + sensor power)
```

### Energy Budget (nRF52 + LoRa, 15-min interval)

| Mode | Current @ 3.3V | Duration/cycle | Cycles/day | Energy/day |
|------|----------------|---------------|------------|-----------|
| System ON sleep (nRF52 + RTC) | 2.5 µA | 895 s | 96 | 0.21 mWh |
| Wake + sensor read | 5 mA | 0.2 s | 96 | 0.09 mWh |
| LoRa TX (20 dBm) | 120 mA | 0.1 s | 96 | 1.14 mWh |
| Processing | 5 mA | 0.1 s | 96 | 0.05 mWh |
| TPS22860 leakage (off) | 0.1 nA | 86,362 s | 1 | ~0 |
| TPS7A02 Iq | 25 nA | 86,400 s | 1 | 0.002 mWh |
| **Total** | | | | **1.49 mWh/day** |

**Average current: 1.49 mWh ÷ 24h ÷ 3.3V = 0.019 mA = 19 µA average**

This is **14× less** than the ESP32 WiFi version. LoRa + nRF52 is the sweet spot for solar IoT.

### Component BOM (Architecture B)

| Component | Part Number | Description | Qty | Price |
|-----------|-------------|-------------|-----|-------|
| Solar panel | Voltaic P105 | 1W 6V mono-Si, 99×69mm | 1 | ~$9 |
| MPPT charger | CN3791 module | 6V MPPT → 1S Li-ion | 1 | ~$3 |
| Battery | IFR14500 | 3.2V 600mAh LiFePO4, AA-size | 1 | ~$3 |
| LDO | TI TPS7A02 | 3.3V 25nA Iq, SOT-23-5 | 1 | ~$0.60 |
| Load switch | TI TPS22860 | 0.1nA leakage, SOT-23-6 | 1 | ~$0.80 |
| MCU | Nordic nRF52840 | BLE + Thread, QFN-73 | 1 | ~$4 |
| LoRa radio | HopeRF RFM95W | SX1276, 868/915 MHz | 1 | ~$5 |
| Sensor | Bosch BME280 | T/H/P, I2C, 3.6µA sleep | 1 | ~$3 |
| Antenna | LoRa whip or PCB | 868/915 MHz | 1 | ~$2 |
| Enclosure | IP65 junction box | 100×68×50mm | 1 | ~$5 |
| Passives | Resistors, caps, inductor | Various | — | ~$2 |
| **Total BOM** | | | | **~$38** |

### ⚠ CN3791 + LiFePO4 Note

The CN3791 charges to 4.2V by default (Li-ion). For LiFePO4 (3.6V charge), you need to modify the output voltage feedback resistor. Some CN3791 modules have a "LiFePO4" version. Alternatively, use a dedicated LFP charger like the **CN3058E**.

---

## 6.4 Tier 3: Multi-Watt Raspberry Pi System

### Use Case
Solar-powered edge computing: Pi runs a dashboard, camera, data logger, or serves a web interface. Needs to survive overnight on battery.

### Power Requirements

| Pi Model | Idle Power | Typical Load | Peak Power | Daily Energy (24h on) |
|----------|-----------|-------------|-----------|----------------------|
| Pi Zero 2 W | 0.4 W | 0.7 W | 1.2 W | 10–17 Wh |
| Pi 4 (4GB) | 2.7 W | 4.0 W | 7.6 W | 65–96 Wh |
| Pi 5 (4GB) | 2.2 W | 3.5 W | 10 W | 53–84 Wh |
| Pi 5 (2GB) | 1.5 W | 2.8 W | 8 W | 36–67 Wh |

**Key insight:** Even the Pi Zero 2 W at 0.7W average needs 17 Wh/day — that's ~170× more than our LoRa sensor node. Pi solar systems are fundamentally different from IoT nodes.

### Architecture A: Pi Zero — Modest System

```
┌──────────────┐    ┌──────────────────┐    ┌────────────────┐    ┌───────┐
│ 6W 6V Panel  │───→│ Waveshare Solar  │───→│ 3.7V 6000mAh   │───→│ Pi    │
│ or            │    │ Power Manager    │    │ LiPo (2S 18650) │    │ Zero  │
│ 10W 12V      │    │ (CN3791 + boost) │    │                │    │ 2 W   │
└──────────────┘    └──────────────────┘    └────────────────┘    └───────┘
```

### Architecture B: Pi 5 — Full System

```
┌──────────────┐    ┌──────────┐    ┌────────────────┐    ┌──────────┐    ┌───────┐
│ 25W 18V      │───→│ MPPT     │───→│ 12.8V 6Ah      │───→│ 5V/5A    │───→│ Pi 5  │
│ Solar Panel  │    │ Controller│    │ 4S LiFePO4     │    │ Buck     │    │       │
│ (Renogy etc) │    │ (Victron │    │ or 12V 7Ah     │    │ (Pololu  │    │       │
│              │    │  or EPever)    │ SLA            │    │  D24V50F5)    │       │
└──────────────┘    └──────────┘    └────────────────┘    └──────────┘    └───────┘
```

### Energy Budget: Pi Zero 2 W (Always-On)

| Component | Power | Hours/day | Energy/day |
|-----------|-------|----------|-----------|
| Pi Zero 2 W (headless, light load) | 0.6 W | 24 | 14.4 Wh |
| USB WiFi dongle (if external) | 0.2 W | 24 | 4.8 Wh |
| Sensor board (I2C) | 0.01 W | 24 | 0.24 Wh |
| **Total** | | | **~15–19 Wh/day** |

### Panel & Battery Sizing: Pi Zero 2 W

```
Location: New York, worst-case winter: 2 PSH
Efficiency: 0.58

Panel: 19 Wh / (2 h × 0.58) = 16.4 W → Use 20W panel for margin

Battery: 2 nights autonomy (assume no sun for 2 days)
  19 Wh × 2 = 38 Wh
  At 12V: 38 / 12 = 3.2 Ah → Use 7Ah SLA or 6Ah LiFePO4

  At 3.7V (LiPo): 38 / 3.7 = 10.3 Ah → Use 10,000–12,000 mAh LiPo
```

### Energy Budget: Pi 5 (Duty-Cycled, Power-Optimized)

Running Pi 5 24/7 solar is **very challenging**. Better approach: duty-cycle the Pi.

| Mode | Power | Duration/day | Energy/day |
|------|-------|-------------|-----------|
| Pi 5 active (headless, optimized) | 3.0 W | 6 h (daytime) | 18.0 Wh |
| Pi 5 off (controlled shutdown) | 0 W* | 18 h | 0 Wh |
| 5V buck converter Iq (no load) | 0.01 W | 18 h | 0.18 Wh |
| RTC + power controller | 0.001 W | 24 h | 0.024 Wh |
| **Total** | | | **~18.2 Wh/day** |

*Pi 5 has no hardware sleep/standby with µA consumption. "Off" means fully powered down via external switch or PMIC, scheduled wake via RTC.*

```
Panel: 18.2 / (2 × 0.58) = 15.7 W → Use 25W panel
Battery: 18.2 × 2 / 12 = 3.0 Ah → Use 6Ah 4S LiFePO4 (12.8V)
```

### Component BOM: Pi 5 Solar System

| Component | Part Number / Description | Qty | Price |
|-----------|--------------------------|-----|-------|
| Solar panel | Renogy 25W 12V mono (or Voltaic 17W) | 1 | ~$30–50 |
| MPPT controller | Victron SmartSolar 75/10 | 1 | ~$65 |
| | *or* EPever Tracer 1210AN | 1 | ~$25 |
| | *or* CN3722 module (budget) | 1 | ~$8 |
| Battery | 4S LiFePO4 6Ah 12.8V (e.g., TalentCell) | 1 | ~$35 |
| | *or* 12V 7Ah SLA (e.g., UPG UB1270) | 1 | ~$18 |
| 5V buck converter | Pololu D24V50F5 (5V 5A step-down) | 1 | ~$10 |
| | *or* LM2596 module (budget) | 1 | ~$2 |
| Power control | Witty Pi 4 (RTC + power scheduling) | 1 | ~$30 |
| | *or* DS3231 RTC + MOSFET (DIY) | 1 | ~$5 |
| Pi | Raspberry Pi 5 (2GB or 4GB) | 1 | ~$50–60 |
| SD card | 32GB A2 industrial (e.g., Samsung PRO Endurance) | 1 | ~$10 |
| Enclosure | IP65 box, cable glands | 1 | ~$15 |
| Wiring | Fuse, connectors, cable | — | ~$10 |
| **Total BOM** | | | **~$200–330** |

### Pi Power Management Options

| Solution | Features | Price |
|----------|----------|-------|
| **Witty Pi 4 (Mini)** | RTC wake schedule, voltage monitoring, temp logging | ~$30 |
| **PiJuice HAT** | LiPo charger, RTC, power management, solar input | ~$50 |
| **Geekworm X728** | UPS + power management, 18650 holders | ~$35 |
| **DIY: DS3231 + P-MOSFET** | RTC alarm triggers MOSFET, cuts 5V rail | ~$5 |
| **DIY: ATtiny85 watchdog** | MCU monitors battery, controls Pi power | ~$3 |

### Pi Power Reduction Techniques

```bash
# Disable HDMI (saves ~30mA)
tvservice -o                          # Pi 4
# or in /boot/firmware/config.txt:
dtoverlay=vc4-kms-v3d,nohdmi         # Pi 5

# Disable USB (if not needed, saves ~100mA)
echo 0 > /sys/devices/platform/soc/3f980000.usb/buspower  # Pi 4

# Disable WiFi (if using Ethernet or not needed)
dtoverlay=disable-wifi

# Disable Bluetooth
dtoverlay=disable-bt

# Reduce CPU frequency
echo 600000 > /sys/devices/system/cpu/cpu0/cpufreq/scaling_max_freq

# Disable LEDs
dtparam=act_led_trigger=none
dtparam=pwr_led_trigger=none
```

---

## 6.5 Tier 4: Permacomputing / Deploy-and-Forget

### Philosophy

Design for **10+ year unattended operation**. Everything degrades: panels lose ~0.5%/year efficiency, batteries lose capacity, connectors corrode. The system must survive all of this with zero maintenance.

### Design Principles

1. **Over-provision everything** — 3–5× panel margin, 5–10× battery capacity margin
2. **Choose longest-life components** — LiFePO4 (5000+ cycles), supercaps (1M cycles), conformal-coated PCB
3. **Minimize failure modes** — fewer connectors, no moving parts, no SD cards (use internal flash)
4. **Software resilience** — watchdog timers, filesystem integrity, graceful degradation
5. **Energy-neutral operation** — consume only what you harvest over a rolling average

### Architecture: Deploy-and-Forget LoRa Sensor

```
┌──────────────┐    ┌──────────┐    ┌──────────┐    ┌───────────────┐
│ 3.5W 6V Panel│───→│ BQ25570  │───→│ LiFePO4  │───→│ TPS62840 Buck │
│ Voltaic P107 │    │ Boost +  │    │ 1500mAh  │    │ 3.3V 60nA Iq  │
│ (oversized)  │    │ MPPT     │    │ 1S       │    └───────┬───────┘
└──────────────┘    │          │    │          │            │
                    │ OV=3.6V  │    └──────────┘    ┌───────┴───────┐
                    │ UV=2.8V  │                    │ ATmega328P    │
                    └──────────┘                    │ (bare, 8MHz   │
                                                    │ internal osc) │
                                                    │ + RFM95W LoRa │
                                                    │ + SHT40 temp  │
                                                    └───────────────┘
```

### Why ATmega328P for Permacomputing?

| Factor | ATmega328P | ESP32 | nRF52840 |
|--------|-----------|-------|----------|
| Power-down sleep | 0.1 µA | 5–10 µA | 0.3 µA (System OFF) |
| Active @ 8MHz | 4 mA | 30+ mA | 3 mA |
| Complexity | Minimal | Linux-like complexity | Medium |
| Supply chain lifespan | Decades (PDIP available) | Years | Years |
| Repairability | Through-hole possible | BGA/QFN | QFN |
| Community longevity | 20+ years of code | ~10 years | ~5 years |

**For permacomputing, simplicity and repairability matter more than features.** An ATmega328P in DIP-28 can be socketed and replaced by hand.

### Energy Budget: 1-Hour LoRa Report

| Mode | Current @ 3.3V | Duration/cycle | Cycles/day | Energy/day |
|------|----------------|---------------|------------|-----------|
| Power-down (ATmega + WDT) | 4.5 µA* | 3,597 s | 24 | 0.30 mWh |
| Wake + sensor read | 5 mA | 0.2 s | 24 | 0.02 mWh |
| LoRa TX (14 dBm) | 40 mA | 0.1 s | 24 | 0.01 mWh |
| TPS62840 Iq | 60 nA | 86,400 s | 1 | 0.005 mWh |
| **Total** | | | | **0.34 mWh/day** |

*Includes ATmega WDT (0.1µA), TPS62840 Iq (0.06µA), SHT40 sleep (0.08µA), RFM95W sleep (0.2µA), leakage (~4µA board total with real PCB)*

**Average current: 0.34 mWh ÷ 24h ÷ 3.3V = 4.3 µA**

### Harvest Margin Analysis

```
Panel: 3.5W Voltaic P107
Location: London, UK — worst case: 0.5 PSH (December)
System efficiency: 0.50 (conservative — aging, dirt, suboptimal angle)

Daily harvest: 3,500 mW × 0.5 h × 0.50 = 875 mWh
Daily consumption: 0.34 mWh
Margin: 2,574× even in worst-case London winter

→ This system could operate with a panel 1/100th the size and still survive.
→ The massive over-provisioning ensures operation even if panel degrades 90%.
```

### Battery Longevity

```
1500 mAh LiFePO4 at 3.2V = 4,800 mWh
Days of autonomy (no sun at all): 4,800 / 0.34 = 14,117 days = 38.7 years
→ Battery will chemically degrade before it's discharged.

With LiFePO4 at 5000 cycles, one cycle per day = 13.7 years of daily cycling.
Reality: system barely cycles the battery (huge panel surplus), so cycle life is essentially infinite.
```

### Component BOM: Deploy-and-Forget Node

| Component | Part Number | Description | Qty | Price |
|-----------|-------------|-------------|-----|-------|
| Solar panel | Voltaic P107 | 3.5W 6V, ETFE coated | 1 | ~$19 |
| Harvesting IC | TI BQ25570 | Boost MPPT, QFN-20 | 1 | ~$4.50 |
| Battery | LiFePO4 IFR18650 1500mAh | 3.2V, long cycle life | 1 | ~$5 |
| Buck converter | TI TPS62840 | 3.3V, 60nA Iq | 1 | ~$1.20 |
| MCU | ATmega328P-PU (DIP-28) | 8MHz internal, socketed | 1 | ~$2.50 |
| IC socket | DIP-28 | For field replacement | 1 | ~$0.30 |
| LoRa radio | RFM95W (SX1276) | 868/915 MHz | 1 | ~$5 |
| Sensor | Sensirion SHT40 | Temp/humidity, I2C | 1 | ~$2.50 |
| Antenna | Wire quarter-wave | Cut to frequency | 1 | ~$0 |
| Conformal coat | MG Chemicals 419D | Silicone conformal coating | — | ~$15/bottle |
| Enclosure | IP67 ABS box | UV-resistant, sealed | 1 | ~$8 |
| Desiccant | Silica gel packet | Moisture control | 2 | ~$0.50 |
| Stainless hardware | SS316 screws, cable gland | Corrosion-resistant | — | ~$5 |
| PCB | Custom 2-layer | OSHPark or JLCPCB | 1 | ~$3 |
| **Total BOM** | | | | **~$72** |

### Physical Durability Checklist

- [ ] **Conformal coat** all PCB surfaces (silicone or acrylic)
- [ ] **IP67 enclosure** minimum; IP68 for flood-prone locations
- [ ] **Stainless steel fasteners** (SS316 marine grade)
- [ ] **ETFE-coated panel** (UV-resistant, 20+ year outdoor life)
- [ ] **No SD card** — use MCU internal flash/EEPROM for data
- [ ] **Potted antenna connector** or integrated PCB antenna
- [ ] **Desiccant packs** inside enclosure (+ humidity indicator card)
- [ ] **Cable glands** (not holes with sealant)
- [ ] **No electrolytic capacitors** — use MLCC and tantalum (electrolytics dry out)
- [ ] **Battery holder with spring contacts** (no soldered wires to battery)

---

## 6.6 Design Pattern Comparison

| Parameter | Wearable (6.2) | IoT WiFi (6.3A) | IoT LoRa (6.3B) | Pi System (6.4) | Permacomputing (6.5) |
|-----------|----------------|------------------|------------------|-----------------|---------------------|
| **Panel** | KXOB25 (110mW) | 2W 6V | 1W 6V | 25W 18V | 3.5W 6V |
| **Harvester/Charger** | BQ25570 | CN3791 | CN3791 | Victron MPPT | BQ25570 |
| **Storage** | 1F supercap | 600mAh LFP | 600mAh LFP | 6Ah LFP/7Ah SLA | 1500mAh LFP |
| **Regulator** | BQ25570 buck | MCP1700 LDO | TPS7A02 LDO | Pololu buck | TPS62840 buck |
| **MCU** | nRF52832 | ESP32-S3 | nRF52840 | Pi 5 | ATmega328P |
| **Radio** | BLE | WiFi | LoRa (RFM95W) | WiFi/Ethernet | LoRa (RFM95W) |
| **Avg current** | ~0.5 µA | 270 µA | 19 µA | ~600 mA | 4.3 µA |
| **Daily energy** | 0.17 mWh | 21.3 mWh | 1.49 mWh | 18,200 mWh | 0.34 mWh |
| **Autonomy (no sun)** | Hours (supercap) | 25 days | 1.1 years | 1.6 days | 38+ years |
| **Design lifespan** | 1–3 years | 3–5 years | 5–10 years | 2–5 years | 10–20+ years |
| **BOM cost** | ~$15 | ~$38+ | ~$38 | ~$200–330 | ~$72 |

## 6.7 Common Mistakes

| Mistake | Impact | Fix |
|---------|--------|-----|
| Using AMS1117 (5mA Iq!) as regulator | Dominates power budget in sleep | Use TPS7A02 or MCP1700 |
| Sizing panel for average conditions | System dies in winter | Size for worst-month PSH |
| No reverse current protection | Battery drains through panel at night | Ideal diode or charge IC handles it |
| Using LiPo where LiFePO4 fits | Battery dies after 1–2 years of daily cycling | LiFePO4 for solar duty cycling |
| Trusting USB power meters for µA | No resolution below ~1mA | Use µCurrent Gold or INA219 shunt |
| Ignoring peripheral sleep current | "10µA MCU sleep" becomes 500µA with peripherals | Power-gate unused peripherals |
| Dev board instead of bare MCU | Arduino Uno draws 20+mA with USB chip, LED, regulator | Bare ATmega328P or modules without extras |
| Soldering wires to battery terminals | Vibration + corrosion = failure | Spring contacts or proper spot welds |

## 6.8 Measurement & Validation Tools

| Tool | What It Measures | Resolution | Price |
|------|-----------------|------------|-------|
| **Nordic PPK2** | Current (nA–1A) | 200 nA | ~$90 |
| **µCurrent Gold** | Current (nA–mA) | 1 nA | ~$60 |
| **Otii Arc Pro** (Qoitech) | Current + voltage, automated | 100 nA | ~$500 |
| **INA219 breakout** | Current shunt, I2C | 100 µA | ~$5 |
| **Multimeter (Fluke 87V)** | Voltage, current | 0.1 µA | ~$400 |
| **USB power meter** | USB voltage/current | 1 mA | ~$10 |

**Recommendation:** For IoT sleep current validation, the **Nordic PPK2** is the best value. Captures nA to A with real-time profiling and Zephyr/SDK integration.

---

**Next:** [Chapter 7 — Software & Firmware](07-software-firmware.md)
