# Chapter 9: Practical Builds & Reference Designs

## 9.1 Solar-Powered ESP32 Weather Station

### Overview
A deploy-and-forget outdoor weather station: ESP32 reads temperature, humidity, and pressure, transmits via Wi-Fi, then deep-sleeps. Solar panel keeps it running indefinitely.

### Bill of Materials

| Component | Specific Part | Approx. Price | Notes |
|-----------|--------------|---------------|-------|
| MCU | ESP32-S3 DevKitC or FireBeetle ESP32-E (DFRobot DFR0654) | $6–10 | FireBeetle has ultra-low deep sleep (~10µA) |
| Sensor | BME280 breakout (Bosch) | $3–8 | Temp/humidity/pressure via I2C. Use genuine Bosch, not BMP280 |
| Solar panel | 6V 1W–2W mini panel (110×60mm typical) | $3–5 | Two 6V/1W panels in parallel for margin |
| Charge controller | CN3791 module or TP4056 + protection | $0.50–1 | CN3791 preferred (MPPT-like). TP4056 works but no MPPT |
| Battery | 3.7V 2000–3000mAh LiPo (18650 or pouch) | $3–6 | LiFePO4 (3.2V) for longevity; adjust charge IC |
| Regulator | Onboard (ESP32 dev board has 3.3V LDO) | — | If bare module, add HT7333 or ME6211 LDO |
| Enclosure | IP65 ABS junction box (~100×68×50mm) | $2–5 | Drill holes for sensor (mesh-covered) |

**Total cost: ~$18–35**

### Circuit

```
  6V Solar Panel(s)
       │
  ┌────┴────┐
  │  CN3791  │──── 4.2V charge ──── 18650 LiPo
  │  module  │                         │
  └─────────┘                         │
                                      │
                              ┌───────┴───────┐
                              │  ESP32-S3     │
                              │  (3.3V LDO)   │
                              │               │
                              │  GPIO21(SDA)──┤── BME280
                              │  GPIO22(SCL)──┘
                              │               │
                              │  ADC (GPIO34) ←── voltage divider on VBAT
                              └───────────────┘
```

### Energy Budget

| State | Current (3.7V) | Duration | Energy per Cycle |
|-------|---------------|----------|-----------------|
| Deep sleep | 10µA (FireBeetle) | 295 seconds | 10.9 µWh |
| Wake + sensor read | 30mA | 0.5s | 5.6 µWh |
| Wi-Fi TX | 160mA | 4s | 236 µWh |
| **Total per 5-min cycle** | — | 300s | **~253 µWh** |
| **Daily (288 cycles)** | — | — | **~73 mWh** |

With a 1W panel getting 4 peak sun hours: **4 Wh/day harvest** vs **0.073 Wh/day consumption** → massive surplus. This means:
- Works year-round in most climates
- A 0.5W panel is more than sufficient
- A 1000mAh battery provides ~50 days of autonomy without sun

### Firmware Skeleton (Arduino)

```cpp
#include <WiFi.h>
#include <Wire.h>
#include <Adafruit_BME280.h>
#include <HTTPClient.h>

#define SLEEP_US  300000000ULL  // 5 minutes
#define VBAT_PIN  34
#define VBAT_DIVIDER_RATIO 2.0

Adafruit_BME280 bme;

void setup() {
    // Read sensors
    Wire.begin(21, 22);
    bme.begin(0x76);
    float temp = bme.readTemperature();
    float hum  = bme.readHumidity();
    float pres = bme.readPressure() / 100.0F;

    // Read battery voltage
    float vbat = analogRead(VBAT_PIN) * (3.3 / 4095.0) * VBAT_DIVIDER_RATIO;

    // Connect and send
    WiFi.begin("SSID", "PASS");
    int retries = 0;
    while (WiFi.status() != WL_CONNECTED && retries++ < 20) delay(250);

    if (WiFi.status() == WL_CONNECTED) {
        HTTPClient http;
        String url = String("http://server/api/weather?t=") + temp
                   + "&h=" + hum + "&p=" + pres + "&v=" + vbat;
        http.begin(url);
        http.GET();
        http.end();
    }

    // Sleep
    esp_deep_sleep(SLEEP_US);
}

void loop() {} // Never reached
```

### Enclosure Tips
- Mount solar panel on lid with silicone sealant
- BME280 should be **outside** the sealed box or in a vented Stevenson screen
- Use cable glands for any external wires
- Conformal coat the PCB (MG Chemicals 422B acrylic spray)

---

## 9.2 Solar LoRa Sensor Node

### Overview
Long-range (1–15 km), low-power sensor node using LoRa. Ideal for agricultural monitoring, remote environmental sensing, or Meshtastic mesh networking.

### Bill of Materials

| Component | Specific Part | Approx. Price | Notes |
|-----------|--------------|---------------|-------|
| MCU + LoRa | Heltec WiFi LoRa 32 V3 (ESP32-S3 + SX1262) | $15–20 | Integrated LoRa, OLED, battery connector |
| *Alternative* | RAK WisBlock (RAK4631 = nRF52840 + SX1262) | $20–25 | Lower power (nRF52), modular, Meshtastic-ready |
| *Alternative* | LILYGO T-Beam (ESP32 + SX1262 + GPS) | $25–35 | Good for Meshtastic with GPS |
| Sensor | BME280 or SHT40 (Sensirion) | $3–8 | SHT40 more accurate humidity |
| Solar panel | 6V 1W panel | $3–5 | 1W is generous for LoRa duty cycles |
| Charge controller | Onboard (Heltec/RAK have JST-PH battery + charge IC) | — | Most LoRa dev boards include TP4056 or equivalent |
| Battery | 3.7V 1000–2000mAh LiPo (JST-PH connector) | $3–5 | Match connector to board |
| Antenna | 868/915MHz LoRa antenna (SMA or IPEX) | $2–5 | Proper antenna matters more than TX power |
| Enclosure | IP65 box or PVC pipe enclosure | $3–10 | Vertical pipe mount for elevated antenna |

**Total: ~$25–50**

### Energy Budget (Heltec V3, LoRa TX every 10 min)

| State | Current | Duration | Energy per Cycle |
|-------|---------|----------|-----------------|
| Deep sleep | 20µA | ~598s | 44.3 µWh |
| Wake + sensor | 30mA | 0.5s | 5.6 µWh |
| LoRa TX (+20dBm) | 120mA | 1.5s | 66.6 µWh |
| **Per cycle (10 min)** | — | 600s | **~117 µWh** |
| **Daily** | — | — | **~17 mWh** |

A 1W panel with 3 peak sun hours = 3 Wh/day → **176× surplus**. LoRa nodes are incredibly efficient.

### RAK WisBlock Advantage
The nRF52840-based RAK4631 achieves ~2µA deep sleep (vs ~20µA for ESP32-S3), making it ideal for truly minimal solar:
- Daily consumption with nRF52: ~5 mWh
- Could run on a **0.3W panel** and **500mAh battery**
- RAK ecosystem has plug-in sensor modules (RAK1901 temp/humidity, RAK12500 GPS, etc.)

### Meshtastic Solar Node

For Meshtastic mesh networking with always-on receive:

| State | Current | Notes |
|-------|---------|-------|
| Idle (radio listening) | ~30mA | LoRa RX always on for mesh |
| TX burst | ~120mA | When forwarding or sending messages |
| **Average** | **~35mA** | Depends on mesh traffic |

Daily consumption: 35mA × 3.7V × 24h ≈ **3.1 Wh/day**
Panel needed: ~6W panel (assuming 3 peak sun hours, 80% efficiency)
Battery: 3000–6000mAh for overnight autonomy

**Tip:** Use Meshtastic's "Router" mode for solar relay nodes — disables screen, reduces unnecessary TX.

---

## 9.3 Solar Raspberry Pi Zero Dashboard

### Overview
An always-on (or scheduled) Raspberry Pi Zero 2 W serving a local web dashboard, environmental data logger, or solar-powered mini-server.

### Bill of Materials

| Component | Specific Part | Approx. Price | Notes |
|-----------|--------------|---------------|-------|
| SBC | Raspberry Pi Zero 2 W | $15 | Quad-core, Wi-Fi, 512MB RAM |
| Power board | Waveshare Solar Power Manager (D) | $20–25 | MPPT, 6–24V input, 5V/3A out, battery management |
| *Alternative* | PiJuice Solar | $50–60 | HAT format, RTC, programmable wake |
| *Alternative* | PhotonPower Zero | ~$30 | Purpose-built for solar Pi Zero |
| Solar panel | 6V 6W panel (Voltaic P126, 280×175mm) | $25–35 | Or 12V 10W panel with Waveshare D |
| Battery | 3.7V 6000mAh LiPo (or 2× 3000mAh parallel) | $8–15 | Pi Zero needs ~12 Wh for 24h |
| Storage | 16GB+ microSD (industrial grade) | $8–12 | Use industrial temp range, SLC preferred |
| Sensor (optional) | BME280 + TSL2591 (light) via I2C | $5–10 | Or any I2C/SPI sensor |

**Total: ~$80–130**

### Power Analysis

| Mode | Power Draw | Notes |
|------|-----------|-------|
| Pi Zero 2 W idle (no HDMI, Wi-Fi on) | 0.5–0.8W | Minimal services running |
| Pi Zero 2 W active (serving web) | 1.0–1.5W | Python/Flask or static nginx |
| Pi Zero 2 W peak (CPU 100%) | 2.5–3.0W | Avoid sustained peak on solar |
| **Daily average (light web server)** | **~0.7W → 16.8 Wh/day** | |

Panel sizing: 16.8 Wh ÷ (4 sun-hours × 0.8 eff) = **~5.3W minimum** → use 6–10W panel

Battery sizing for 24h autonomy: 16.8 Wh ÷ 3.7V = **4.5 Ah minimum** → use 6000mAh for margin

### Reducing Power Draw

```bash
# Disable HDMI (saves ~30mA)
sudo tvservice -o
# Or in /boot/config.txt: dtoverlay=vc4-kms-v3d,nohdmi

# Disable Bluetooth (saves ~20mA)
sudo systemctl disable bluetooth
# In /boot/config.txt: dtoverlay=disable-bt

# Disable LEDs
echo none | sudo tee /sys/class/leds/ACT/trigger

# Use lightweight web server
sudo apt install nginx-light  # not apache
# Or busybox httpd for absolute minimum

# Set CPU governor to powersave
echo powersave | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

### Scheduled Operation (Cron Solar)
If 24/7 operation isn't needed, use the power manager's programmable wake:

```
# PiJuice example: wake at 6 AM, run for 2 hours, shutdown
# Set in pijuice_cli → Wakeup Alarm
# In crontab:
0 8 * * * sudo shutdown -h now  # 2 hours after 6 AM wake
```

Or with Waveshare Solar Power Manager (D): use the onboard MCU to control enable pin based on battery SOC.

---

## 9.4 Solar Raspberry Pi 5 Portable

### Overview
A more powerful solar-powered portable computing setup. The Pi 5 draws significantly more power, requiring a larger panel and battery system. Suitable for a field workstation, portable development machine, or solar-powered media server.

### Bill of Materials

| Component | Specific Part | Approx. Price | Notes |
|-----------|--------------|---------------|-------|
| SBC | Raspberry Pi 5 (4GB or 8GB) | $60–80 | 2.4GHz quad-core, 4K display, USB3 |
| Power supply | USB-C PD from battery bank, or custom MPPT | — | Pi 5 needs 5V/5A (25W peak) |
| Solar panel | 12V 30–50W rigid or foldable panel | $30–60 | Foldable for portability (e.g., Renogy E.FLEX 30W) |
| MPPT controller | Genasun GV-5 (5A, LiFePO4) or Victron SmartSolar 75/10 | $40–90 | Charges 12V battery bank |
| Battery | 12V 12–20Ah LiFePO4 (e.g., TalentCell 12V/12Ah) | $50–80 | Or 4S LiFePO4 pack |
| 5V converter | USB-C PD trigger board + buck converter | $5–15 | 12V→5V/5A. Or USB-C PD powerbank as intermediary |
| *Alternative path* | Large USB-C PD power bank (100Wh+) + solar panel with USB out | $60–100 | Simpler but less efficient |
| Display | 7" HDMI touchscreen (optional) | $30–50 | For portable workstation use |
| Case | Pelican-style waterproof case | $20–40 | Panel mounted on lid or external |

**Total: ~$200–400** (varies widely with choices)

### Power Analysis

| Mode | Power Draw |
|------|-----------|
| Pi 5 idle (headless) | 3–4W |
| Pi 5 desktop (light use) | 5–8W |
| Pi 5 peak (CPU + GPU) | 12–25W |
| **Realistic average (portable workstation)** | **~6–8W** |

Daily energy at 7W average: **168 Wh/day** — this is a serious power requirement.

- **30W panel, 4 peak sun hours:** 30 × 4 × 0.8 = 96 Wh → not enough for 24/7
- **50W panel, 4 peak sun hours:** 50 × 4 × 0.8 = 160 Wh → barely enough
- **Practical approach:** Use during daytime only (8h = 56 Wh), 30W panel provides surplus for next day

### Architecture Options

**Option A: 12V Battery System (recommended for stationary)**
```
  30–50W Panel → MPPT Controller → 12V LiFePO4 Battery
                                          │
                                    12V→5V/5A Buck
                                          │
                                    USB-C → Pi 5
```

**Option B: USB Power Bank (recommended for portable)**
```
  Foldable 30W Panel (USB-C output)
          │
    100Wh USB-C PD Power Bank
          │
    USB-C PD → Pi 5 (5V/5A)
```

Option B is simpler but less efficient (panel→bank conversion + bank→Pi conversion = ~70% overall). Option A with a dedicated MPPT controller achieves ~85–90%.

### Practical Notes
- Pi 5 requires USB-C PD **or** a proper 5V/5A supply — it will throttle below 4.63V
- Active cooling recommended (official Pi 5 cooler draws ~0.5W)
- For intermittent use: charge the battery bank during the day, compute in the evening
- NVMe storage (via HAT) adds ~2W — use microSD for solar builds unless IO performance is critical

---

## 9.5 Wearable Solar Charging

### Overview
Integrating small solar cells into clothing or accessories to trickle-charge wearable electronics (smartwatches, fitness trackers, BLE sensor tags).

### Power Available from Wearable Solar

| Placement | Panel Size | Typical Power | Notes |
|-----------|-----------|--------------|-------|
| Hat brim | 5×10cm | 100–300mW | Good sun exposure when outdoors |
| Backpack flap | 15×20cm | 1–3W | Excellent — commonly done by Voltaic |
| Jacket shoulder | 10×10cm | 200–500mW | Partial shading, variable angle |
| Wristband | 3×5cm | 20–80mW | Very small, inconsistent orientation |
| Shoe tongue | 3×3cm | 10–40mW | Novelty only — too small and shaded |

### Recommended Components for Wearable Solar

| Component | Part | Why |
|-----------|------|-----|
| Flex solar cell | PowerFilm MP3-37 (3V, 50mA, flex) | Thin, flexible, sewable |
| *Alternative* | IXYS/Anysolar KXOB25-04X3F (3V, 50mA) | Rigid but tiny (25×25mm) |
| Energy harvester IC | TI BQ25570 | Ultra-low power, works from µW inputs |
| Storage | Seiko CPH3225A supercap (11mF, 3.3V) | Tiny coin-cell format for burst power |
| *Alternative* | Small LiPo (40–100mAh, 3.7V) | For sustained loads |
| MCU | Nordic nRF52840 | BLE, 1.7µA sleep, excellent for wearables |
| Sensor | MAX30102 (heart rate) or LIS2DW12 (accel) | Ultra-low power wearable sensors |

### Typical Wearable Solar Energy Chain

```
  Flex solar cell (3V, 50mA outdoor)
       │
  ┌────┴────────┐
  │  BQ25570    │
  │  MPPT boost │
  │  + buck out │
  └──┬──────┬───┘
     │      │
  Supercap  3.3V → nRF52840 + BLE sensor
  (backup)
```

### Energy Math: Solar Hat → BLE Sensor Tag

- Panel: PowerFilm MP3-37, ~150mW peak outdoor
- Realistic outdoor exposure: 2 hours/day = 150mW × 2h = **300 mWh/day**
- nRF52840 BLE beacon (1s advertising interval): ~15µA average → 3.3V × 15µA × 24h = **1.2 mWh/day**
- **Surplus ratio: 250×** — the panel could keep this running with only minutes of daily sun

Even indoor light (200 lux) produces ~10–30µW from a small cell, which with a BQ25570 and supercap can sustain a BLE beacon transmitting every few seconds.

---

## 9.6 Build Tips & Common Mistakes

### Do ✅

- **Oversize the panel** — real-world output is 50–70% of rated (angle, clouds, dirt)
- **Oversize the battery** — you need autonomy for consecutive cloudy days (3–7 days for reliable systems)
- **Use a blocking/Schottky diode** or MOSFET on panel output to prevent battery drain at night (many MPPT ICs handle this internally)
- **Conformal coat** outdoor PCBs
- **Test in winter** — solar harvest in December (northern hemisphere) can be 20% of June values
- **Monitor battery voltage** in firmware — it's your best health indicator
- **Use voltage divider** for ADC battery measurement (ESP32 ADC max 3.3V, battery is 3.7–4.2V)

### Don't ❌

- **Don't use TP4056 for solar without understanding its limits** — it has no MPPT, charges at a fixed rate, wastes energy when panel voltage droops
- **Don't put the temperature sensor inside a sealed black box** — you'll measure enclosure temp, not ambient
- **Don't skip the battery protection circuit** — overdischarge kills LiPo cells permanently
- **Don't use cheap microSD cards** — they fail in temperature extremes. Use industrial grade (e.g., ATP, Swissbit)
- **Don't assume STC panel ratings** — your 6V/1W panel makes ~0.4–0.6W in typical outdoor use
- **Don't use ESP32 light sleep** thinking it's deep sleep — light sleep is 0.8mA vs deep sleep 10µA

---

## 9.7 Reference Designs & Open-Source Projects

| Project | What It Is | Link |
|---------|-----------|------|
| **SoLoRa** | Solar LoRa IoT node with power conditioning | hackaday.io/project/159658 |
| **OpenEnergyMonitor** | Open-source energy monitoring | openenergymonitor.org |
| **ESP32 Solar Weather Station V3** | Detailed Instructables build | instructables.com (search title) |
| **PhotonPower Zero** | Solar Pi Zero power board (open hardware) | github.com (search PhotonPower) |
| **RAK WisBlock** | Modular LoRa+sensor platform | docs.rakwireless.com |
| **Meshtastic** | LoRa mesh networking firmware | meshtastic.org |
| **Solar Protocol** | Distributed solar server network | solarprotocol.net |
| **LOW←TECH MAGAZINE** | Solar-powered website (source available) | github.com/lowtechmag |

---

**Key takeaway:** Solar-powered embedded projects are achievable at every budget and skill level. The energy math almost always works out favorably for duty-cycled IoT — a $3 solar panel can power a sensor node indefinitely. The challenge is rarely energy, but rather weatherproofing, reliability, and maintenance.
