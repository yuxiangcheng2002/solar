# Chapter 7: Software & Firmware for Solar-Powered Systems

## 7.1 The Software Problem

Hardware gets the energy. Software decides how to spend it. A poorly written firmware can waste 100× more energy than a well-tuned one — the difference between a system that runs indefinitely and one that dies in a week.

**Three key firmware goals for solar systems:**
1. **Minimize active time** — do work fast, sleep immediately
2. **Adapt to available energy** — harvest-aware scheduling
3. **Never get stuck** — watchdogs, safe states, graceful degradation

## 7.2 Sleep Modes by Platform

### ESP32 / ESP32-S3

| Mode | Current (typical) | Wake Sources | RAM Retained | Wake Time |
|------|-------------------|-------------|-------------|-----------|
| Active (WiFi TX) | 160–240 mA | — | All | — |
| Active (CPU only, 240MHz) | 30–68 mA | — | All | — |
| Active (CPU only, 80MHz) | 20–25 mA | — | All | — |
| Modem sleep | 20 mA | CPU running | All | Instant |
| Light sleep | 0.8 mA | Timer, GPIO, UART, touch | All | ~1 ms |
| Deep sleep (RTC + RTC mem) | 10 µA | Timer, EXT0/1, touch, ULP | RTC memory (8KB) | ~200 ms (boot) |
| Deep sleep (timer only) | 5 µA | Timer only | RTC memory | ~200 ms |
| Hibernation | 2.5 µA | EXT0 only | Nothing | Full reboot |

**ESP32 Deep Sleep Code Pattern:**

```c
#include "esp_sleep.h"
#include "driver/rtc_io.h"

#define SLEEP_DURATION_US  (15 * 60 * 1000000ULL)  // 15 minutes

void app_main() {
    // Do work: read sensor, transmit data
    read_sensors();
    transmit_data();
    
    // Configure wake-up source
    esp_sleep_enable_timer_wakeup(SLEEP_DURATION_US);
    
    // Optional: keep GPIO state during sleep
    rtc_gpio_isolate(GPIO_NUM_12);  // Prevent leakage on unused RTC GPIOs
    
    // Enter deep sleep
    esp_deep_sleep_start();
    // Never reaches here — reboots on wake
}
```

**ESP32 Power Tips:**
- `esp_wifi_stop()` before sleep (frees WiFi memory, ensures clean shutdown)
- Use `esp_sleep_pd_config()` to power down unused domains:
  ```c
  esp_sleep_pd_config(ESP_PD_DOMAIN_RTC_PERIPH, ESP_PD_OPTION_OFF);
  esp_sleep_pd_config(ESP_PD_DOMAIN_RTC_FAST_MEM, ESP_PD_OPTION_OFF);
  ```
- Isolate unused RTC GPIOs: `rtc_gpio_isolate(pin)` — floating RTC pins leak µAs
- Use ESP32-S3 or ESP32-C6 for better deep sleep current than original ESP32
- **Board matters:** ESP32 dev boards (with USB-UART, LEDs, LDO) draw 20–50mA "sleep." Use bare modules.

### nRF52840

| Mode | Current (typical) | Wake Sources | RAM Retained | Notes |
|------|-------------------|-------------|-------------|-------|
| Active (CPU 64MHz) | 3–5 mA | — | All | Very efficient |
| Active (CPU + BLE TX) | 5–12 mA | — | All | Depends on TX power |
| System ON (idle, RTC running) | 2.35 µA | RTC, GPIO, NFC, LPCOMP | All (256KB) | Light sleep equivalent |
| System OFF | 0.3 µA | GPIO, NFC, LPCOMP | Nothing | Deep sleep |
| System OFF (no RAM, no wake) | 0.04 µA | Reset only | Nothing | Lowest possible |

**nRF52840 System OFF Code (Zephyr):**

```c
#include <zephyr/kernel.h>
#include <zephyr/pm/pm.h>
#include <hal/nrf_power.h>

void enter_system_off(void) {
    // Disable unused peripherals
    // Configure wake-up GPIO (e.g., button or RTC alarm)
    nrf_gpio_cfg_sense_set(WAKE_PIN, NRF_GPIO_PIN_SENSE_LOW);
    
    // Enter System OFF
    nrf_power_system_off(NRF_POWER);
    // Device resets on wake
}
```

**nRF52 Power Tips:**
- Disable UART when not in use (`NRF_UARTE0->ENABLE = 0;`)
- Disable unused peripherals in device tree overlay (Zephyr)
- DCDC mode saves significant power over LDO mode:
  ```c
  NRF_POWER->DCDCEN = 1;    // Enable DC-DC for REG1 stage
  NRF_POWER->DCDCEN0 = 1;   // Enable DC-DC for high voltage stage (nRF52840)
  ```
- Use **System ON** with RTC wake for light sleep (retains RAM, 2.35µA)
- Use **System OFF** for deepest sleep (0.3µA, but loses all state)

### ATmega328P

| Mode | Current @ 3.3V, 8MHz | Wake Sources | Notes |
|------|----------------------|-------------|-------|
| Active | 3.5 mA | — | |
| Idle | 1.5 mA | Any interrupt | CPU halted, peripherals run |
| ADC Noise Reduction | 0.8 mA | ADC, INT0/1, TWI | For clean ADC readings |
| Power-save | 0.8 µA | Timer2 (async), INT0/1 | Timer2 with 32.768kHz crystal |
| Power-down | 0.1 µA | INT0/1, WDT | Deepest; WDT for timed wake |

**ATmega328P Power-Down with WDT Wake:**

```c
#include <avr/sleep.h>
#include <avr/wdt.h>
#include <avr/power.h>

ISR(WDT_vect) {
    // WDT interrupt — just wake up
}

void enter_sleep(void) {
    // Disable ADC (saves ~120µA)
    ADCSRA &= ~(1 << ADEN);
    
    // Disable unused peripherals
    power_all_disable();
    
    // Configure WDT for 8-second wake interval
    cli();
    MCUSR &= ~(1 << WDRF);
    WDTCSR |= (1 << WDCE) | (1 << WDE);
    WDTCSR = (1 << WDIE) | (1 << WDP3) | (1 << WDP0);  // 8s timeout
    sei();
    
    // Enter power-down sleep
    set_sleep_mode(SLEEP_MODE_PWR_DOWN);
    sleep_enable();
    sleep_cpu();
    sleep_disable();
    
    // Re-enable what you need
    power_adc_enable();
    ADCSRA |= (1 << ADEN);
}

void loop() {
    read_sensor();
    transmit_lora();
    
    // Sleep for ~15 minutes (112 × 8s WDT cycles)
    for (uint8_t i = 0; i < 112; i++) {
        enter_sleep();
    }
}
```

**ATmega328P Power Tips:**
- **Disable brown-out detector (BOD)** via fuses: saves ~20µA in sleep
- **Disable ADC** before sleep: saves ~120µA
- **Use `power_all_disable()`** then selectively re-enable needed peripherals
- **Use internal 8MHz oscillator** — no crystal oscillator power draw
- **Strip the Arduino**: use bare ATmega, not Arduino Uno (USB chip, power LED, voltage regulator all waste power)
- At 3.3V and 8MHz, power-down with WDT = **~4.5µA** (MCU + WDT + board leakage)

### Raspberry Pi (Linux)

The Pi doesn't have true hardware sleep modes. Strategies:

| Approach | Power Saved | How |
|----------|------------|-----|
| **Disable HDMI** | ~30 mA | `tvservice -o` / config.txt overlay |
| **Disable USB** | ~100 mA | Sysfs write (model-dependent) |
| **Disable WiFi/BT** | ~40 mA | dtoverlay in config.txt |
| **CPU frequency scaling** | 20–40% | `cpufreq-set -g powersave` |
| **Halt (shutdown)** | ~90% | `sudo halt` — but no auto-wake without external HW |
| **External power cut** | 100% | RTC + MOSFET cuts 5V rail entirely |

**Pi cannot sleep like an MCU.** For solar, you must either:
1. Run it always-on and size panel/battery accordingly
2. Power-cycle it externally (RTC alarm → MOSFET → 5V rail → Pi boots)

**Scheduled Power Cycling with Witty Pi:**

```bash
# Install Witty Pi software
cd ~
wget https://www.uugear.com/repo/WittyPi4/install.sh
sudo bash install.sh

# Set schedule: wake at 8:00, sleep at 20:00 daily
# Via wittyPi/schedule.wpi script or web interface
```

**DIY: DS3231 RTC + P-MOSFET Power Gate:**

```python
# On Pi, before shutdown, set next wake alarm:
import smbus2
import datetime

bus = smbus2.SMBus(1)
DS3231_ADDR = 0x68

def set_alarm(hour, minute):
    """Set DS3231 Alarm 1 for daily wake-up."""
    bus.write_byte_data(DS3231_ADDR, 0x07, dec_to_bcd(0))       # seconds
    bus.write_byte_data(DS3231_ADDR, 0x08, dec_to_bcd(minute))  # minutes
    bus.write_byte_data(DS3231_ADDR, 0x09, dec_to_bcd(hour))    # hours
    bus.write_byte_data(DS3231_ADDR, 0x0A, 0x80)                # day (match h:m:s)
    # Enable Alarm 1 interrupt
    ctrl = bus.read_byte_data(DS3231_ADDR, 0x0E)
    bus.write_byte_data(DS3231_ADDR, 0x0E, ctrl | 0x05)  # A1IE + INTCN

# Set alarm for 8:00 AM, then shutdown
set_alarm(8, 0)
os.system("sudo shutdown -h now")
# DS3231 INT pin goes LOW at 8:00 → pulls MOSFET gate → 5V rail turns on → Pi boots
```

## 7.3 Solar-Aware Scheduling

### The Concept

Instead of a fixed duty cycle, adapt behavior to available energy:

```
Energy abundant (battery full, sun shining):
  → Increase sample rate
  → Enable WiFi for OTA updates
  → Run diagnostics
  → Upload backlog data

Energy scarce (battery low, cloudy):
  → Reduce sample rate
  → LoRa only (no WiFi)
  → Skip non-essential tasks
  → Extend sleep duration

Energy critical (battery near UV threshold):
  → Emergency sleep
  → Single sensor read per day
  → SOS beacon only
```

### Implementation: Tiered Power States

```c
typedef enum {
    POWER_CRITICAL,   // Battery < 20% → survival mode
    POWER_LOW,        // Battery 20-50% → conservative
    POWER_NORMAL,     // Battery 50-80% → normal operation
    POWER_ABUNDANT    // Battery > 80% + solar active → bonus tasks
} power_state_t;

typedef struct {
    uint32_t sleep_duration_s;
    bool     wifi_enabled;
    bool     extra_sensors;
    uint8_t  tx_power_dbm;
} power_profile_t;

const power_profile_t profiles[] = {
    [POWER_CRITICAL] = { .sleep_duration_s = 3600, .wifi_enabled = false,
                         .extra_sensors = false, .tx_power_dbm = 5 },
    [POWER_LOW]      = { .sleep_duration_s = 1800, .wifi_enabled = false,
                         .extra_sensors = false, .tx_power_dbm = 14 },
    [POWER_NORMAL]   = { .sleep_duration_s = 900,  .wifi_enabled = false,
                         .extra_sensors = true,  .tx_power_dbm = 20 },
    [POWER_ABUNDANT] = { .sleep_duration_s = 300,  .wifi_enabled = true,
                         .extra_sensors = true,  .tx_power_dbm = 20 },
};

power_state_t get_power_state(float battery_voltage, bool solar_active) {
    if (battery_voltage < 2.9f) return POWER_CRITICAL;
    if (battery_voltage < 3.1f) return POWER_LOW;
    if (battery_voltage > 3.4f && solar_active) return POWER_ABUNDANT;
    return POWER_NORMAL;
}
```

### Hysteresis

**Always use hysteresis** on power state transitions to prevent oscillation:

```c
// Bad: oscillates rapidly around threshold
if (voltage < 3.1) state = LOW;
else state = NORMAL;

// Good: requires crossing a band to change state
#define LOW_ENTER   3.1f    // Enter LOW when voltage drops below 3.1V
#define LOW_EXIT    3.2f    // Exit LOW only when voltage rises above 3.2V

if (state == POWER_NORMAL && voltage < LOW_ENTER) state = POWER_LOW;
if (state == POWER_LOW && voltage > LOW_EXIT) state = POWER_NORMAL;
```

## 7.4 Energy-Neutral Operation (ENO)

**Goal:** Over any rolling time window, energy consumed ≤ energy harvested. The battery never trends toward empty.

### Algorithm: EWMA Energy Tracking

```c
// Exponentially Weighted Moving Average of energy balance
float harvest_rate_mW = 0;    // Smoothed harvest rate
float consume_rate_mW = 0;    // Smoothed consumption rate
float alpha = 0.1f;           // Smoothing factor (0.1 = slow adaptation)

void update_energy_balance(float harvested_mJ, float consumed_mJ, float dt_s) {
    float instant_harvest = harvested_mJ / dt_s;  // mW
    float instant_consume = consumed_mJ / dt_s;    // mW
    
    harvest_rate_mW = alpha * instant_harvest + (1 - alpha) * harvest_rate_mW;
    consume_rate_mW = alpha * instant_consume + (1 - alpha) * consume_rate_mW;
}

float get_duty_cycle_target(void) {
    if (harvest_rate_mW < 0.001f) return 0.01f;  // Minimum duty cycle (dark)
    float ratio = harvest_rate_mW / consume_rate_mW;
    return fminf(ratio, 1.0f);  // Cap at 100% duty cycle
}
```

### Practical ENO for IoT Nodes

Simpler approach — adjust sleep time based on battery SOC trend:

```c
// Track battery voltage direction over last N readings
#define HISTORY_LEN 8
float v_history[HISTORY_LEN];
uint8_t v_idx = 0;

float voltage_trend(void) {
    // Returns positive if charging, negative if discharging
    float newest = v_history[(v_idx - 1) % HISTORY_LEN];
    float oldest = v_history[v_idx % HISTORY_LEN];
    return newest - oldest;
}

uint32_t adaptive_sleep_seconds(float battery_v) {
    float trend = voltage_trend();
    uint32_t base_sleep = 900;  // 15 minutes
    
    if (battery_v > 3.4f && trend > 0) {
        return base_sleep / 2;   // Battery charging: do more work
    } else if (battery_v < 3.0f || trend < -0.05f) {
        return base_sleep * 4;   // Battery dropping fast: conserve
    }
    return base_sleep;
}
```

## 7.5 Battery State Estimation

### Method 1: Voltage-Based (Simple, Inaccurate)

Map battery voltage to approximate SOC:

**LiFePO4 SOC vs. Voltage (under light load):**

| Voltage | SOC (approx) |
|---------|-------------|
| 3.60 V | 100% (charging) |
| 3.35 V | 100% |
| 3.30 V | 90% |
| 3.28 V | 70% |
| 3.25 V | 40% |
| 3.20 V | 20% |
| 3.00 V | 10% |
| 2.50 V | 0% (cutoff) |

**Problems:**
- Voltage sag under load distorts readings (take reading after sleep, before enabling peripherals)
- Temperature affects voltage
- LiFePO4 has a very flat discharge curve (3.2–3.3V for 70% of capacity) → hard to distinguish 30% from 80%

**Tip:** Measure voltage immediately after waking from sleep (no load) for most accurate reading. Use a voltage divider + ADC:

```c
// Assumes 2:1 voltage divider (R1=100k, R2=100k)
// and ADC reference = 3.3V internal
float read_battery_voltage(void) {
    uint16_t raw = analogRead(VBAT_ADC_PIN);
    float voltage = (raw / 4095.0f) * 3.3f * 2.0f;  // *2 for divider
    return voltage;
}
```

**⚠ Voltage divider draws current!** 100k+100k = 16.5µA from 3.3V battery. For ultra-low power:
- Use higher values (1MΩ + 1MΩ = 1.65µA, but noise increases)
- Or power the divider through a load switch / GPIO pin (only enable during reading)

### Method 2: Coulomb Counting (Accurate, Complex)

Track charge flowing in and out of the battery:

```c
float soc_mAh = BATTERY_CAPACITY_MAH;  // Initialize at full (or calibrate)

void update_soc(float current_mA, float dt_hours) {
    soc_mAh -= current_mA * dt_hours;  // Negative current = charging
    
    // Clamp
    if (soc_mAh > BATTERY_CAPACITY_MAH) soc_mAh = BATTERY_CAPACITY_MAH;
    if (soc_mAh < 0) soc_mAh = 0;
}

float get_soc_percent(void) {
    return (soc_mAh / BATTERY_CAPACITY_MAH) * 100.0f;
}
```

**Requires:** Current sense resistor + ADC (e.g., INA219 I2C current sensor, or low-side shunt resistor).

**Problems:**
- Cumulative drift over time (small errors compound)
- Need calibration points (recalibrate when battery voltage hits known SOC reference, like full charge at 3.6V)
- Adds hardware complexity and quiescent current

### Method 3: Hybrid (Best Practice)

Combine voltage-based with coulomb counting:
- Use coulomb counting for short-term tracking
- Periodically recalibrate with voltage-based reference points (full charge, empty cutoff)
- For most IoT: **voltage-based is good enough** — simplicity wins

### Dedicated Fuel Gauge ICs

If precise SOC matters (e.g., user-facing battery percentage):

| IC | Interface | Battery | Features | Price |
|----|-----------|---------|----------|-------|
| **TI BQ27441** | I2C | 1S Li-ion | Impedance Track™, no calibration needed | ~$3 |
| **Maxim MAX17048** | I2C | 1S Li-ion | ModelGauge™, no sense resistor needed | ~$2 |
| **ST STC3115** | I2C | 1S Li-ion | Coulomb counting + voltage | ~$2 |
| **TI BQ34Z100** | I2C | Multi-chemistry | LiFePO4 support, configurable | ~$5 |

For most solar IoT, **skip the fuel gauge** and use voltage-based estimation. The added complexity and quiescent current (1–10µA) aren't worth it for a system that doesn't display battery level to a user.

## 7.6 Watchdog & Recovery Strategies

Solar systems run unattended for months or years. Firmware must handle every failure:

### Hardware Watchdog

Every MCU has a built-in watchdog timer (WDT). **Always enable it.**

```c
// ESP32: Task watchdog
esp_task_wdt_init(30, true);  // 30s timeout, panic on expire
esp_task_wdt_add(NULL);       // Add current task
// In main loop:
esp_task_wdt_reset();         // Pet the watchdog

// ATmega328P: WDT
wdt_enable(WDTO_8S);         // 8-second timeout
// In main loop:
wdt_reset();                  // Pet the watchdog
```

### External Watchdog (Belt and Suspenders)

For 10+ year deployments, add an external WDT IC:

| IC | Timeout | Iq | Features | Price |
|----|---------|-----|----------|-------|
| **TI TPS3813** | 1.6s fixed | 5 µA | Simple, SOT-23-5 | ~$0.70 |
| **Maxim MAX6369** | 1s–60s selectable | 2.5 µA | Pin-selectable timeout | ~$2 |
| **Microchip MCP1316** | 100ms–2.6s | 1 µA | With power-on reset | ~$0.50 |
| **TI TPL5010** | 100ms–7200s | 35 nA | **Nano-power timer** — perfect for solar IoT | ~$0.70 |

**TI TPL5010** is exceptional: 35 nA quiescent, programmable period up to 2 hours. It outputs a periodic pulse to wake the MCU, and expects a DONE signal back. If DONE doesn't arrive, it toggles RESET.

```
TPL5010 WAKE → MCU interrupt pin
MCU DONE → TPL5010 DONE pin

MCU wakes on WAKE pulse, does work, signals DONE.
If MCU hangs, TPL5010 resets it after next timeout.
```

### Software Recovery Patterns

```c
// Pattern 1: Boot counter — detect repeated crashes
RTC_DATA_ATTR int boot_count = 0;  // Survives ESP32 deep sleep

void app_main() {
    boot_count++;
    
    if (boot_count > 5) {
        // Too many rapid reboots — enter safe mode
        boot_count = 0;
        enter_safe_mode();  // Minimal operation, skip WiFi
        return;
    }
    
    // Normal operation
    // ... 
    
    // Successful cycle → reset counter
    boot_count = 0;
    enter_deep_sleep();
}
```

```c
// Pattern 2: Task timeout — don't block forever
bool wifi_connect_with_timeout(uint32_t timeout_ms) {
    WiFi.begin(SSID, PASS);
    uint32_t start = millis();
    
    while (WiFi.status() != WL_CONNECTED) {
        if (millis() - start > timeout_ms) {
            WiFi.disconnect();
            return false;  // Give up, go to sleep, try next cycle
        }
        delay(100);
    }
    return true;
}

void main_cycle() {
    read_sensors();
    
    if (!wifi_connect_with_timeout(10000)) {
        // Store data locally, try again next cycle
        store_to_flash();
    } else {
        upload_data();
        upload_backlog();
    }
    
    enter_deep_sleep(SLEEP_DURATION);
}
```

```c
// Pattern 3: Filesystem integrity (Pi/Linux)
// Use read-only root filesystem to prevent SD corruption on power loss

// In /etc/fstab:
// /dev/mmcblk0p2  /  ext4  defaults,ro  0  1
// tmpfs  /tmp  tmpfs  defaults,noatime,mode=1777  0  0
// tmpfs  /var/log  tmpfs  defaults,noatime,mode=0755  0  0

// Remount RW only when needed:
// mount -o remount,rw /
// ... write data ...
// sync
// mount -o remount,ro /
```

### Graceful Degradation Priority

When energy is low, shed tasks in order of importance:

```
Priority (keep running longest):
  1. Watchdog timer feed (always)
  2. Battery voltage monitoring (always)
  3. Core sensor reading (reduce frequency)
  4. Local data storage (to flash/EEPROM)
  5. Radio transmission (reduce frequency, then skip)
  6. Secondary sensors (disable)
  7. LED indicators (disable first — they waste power and nobody's looking)
```

## 7.7 Data Buffering & Transmission Strategy

When radio TX is the largest energy cost, smart buffering helps:

```c
// Buffer readings in RTC memory (ESP32) or EEPROM (ATmega)
// Transmit in batch every N readings

#define BUFFER_SIZE 12  // 12 readings = 3 hours at 15-min intervals
RTC_DATA_ATTR sensor_data_t buffer[BUFFER_SIZE];
RTC_DATA_ATTR uint8_t buffer_count = 0;

void main_cycle() {
    buffer[buffer_count++] = read_sensor();
    
    if (buffer_count >= BUFFER_SIZE) {
        // Batch transmit — one radio session for 12 readings
        transmit_batch(buffer, buffer_count);
        buffer_count = 0;
    }
    
    enter_deep_sleep(SLEEP_DURATION);
}
```

**Energy savings:** WiFi connect overhead (~3s, 180mA) amortized over 12 readings instead of 1. Saves ~90% of radio energy if connect time dominates.

## 7.8 Over-The-Air (OTA) Updates

Solar devices need OTA capability — you can't drive to every node to update firmware.

### ESP32 OTA Pattern

```c
void check_for_ota(void) {
    // Only attempt OTA in POWER_ABUNDANT state
    if (get_power_state() != POWER_ABUNDANT) return;
    
    // Only check once per day
    if (!is_ota_check_due()) return;
    
    esp_http_client_config_t config = {
        .url = "https://ota.example.com/firmware.bin",
        .cert_pem = server_cert,
    };
    
    esp_err_t ret = esp_https_ota(&config);
    if (ret == ESP_OK) {
        esp_restart();
    }
}
```

**OTA Safety for Solar:**
- Only attempt OTA when battery > 80% and solar active
- Use A/B partition scheme (ESP32 supports natively) — failed update rolls back
- Set maximum OTA size to prevent downloading a corrupt giant file
- Timeout the download (WiFi uses ~180mA; 60s max = 3 mAh budget)

### LoRa OTA (FUOTA)

LoRaWAN supports Firmware Update Over The Air (FUOTA), but it's slow and complex. For small deployments, consider:
- LoRa downlink commands to adjust parameters (sleep time, TX power, sensor config)
- Full firmware OTA via occasional WiFi connection (if available)
- Physical USB update as last resort

## 7.9 Pi-Specific Software Patterns

### Read-Only Root Filesystem

Essential for solar Pi systems that lose power unexpectedly:

```bash
# Option 1: overlayfs (built into Raspberry Pi OS)
sudo raspi-config
# → Performance Options → Overlay File System → Enable

# Option 2: Manual read-only mount
# In /boot/firmware/cmdline.txt, add:
#   ro
# In /etc/fstab, change root mount to ro

# Write data to a separate partition or tmpfs:
mkdir -p /tmp/data
# Application writes to /tmp/data (lost on reboot)
# Or mount a separate RW partition for persistent data with journaling
```

### Scheduled Operation Script

```bash
#!/bin/bash
# /usr/local/bin/solar-cycle.sh
# Called on boot; does work then schedules shutdown

LOG=/tmp/solar.log

echo "$(date): Boot" >> $LOG

# Wait for network (max 30s)
timeout 30 bash -c 'until ping -c1 8.8.8.8 &>/dev/null; do sleep 2; done'

# Do work
python3 /opt/app/collect_data.py >> $LOG 2>&1
python3 /opt/app/upload_data.py >> $LOG 2>&1

# Set next wake alarm via Witty Pi or DS3231
python3 /opt/app/set_wake_alarm.py --hours 4

# Shutdown
echo "$(date): Shutdown" >> $LOG
sync
sudo shutdown -h now
```

### Systemd Service for Boot Task

```ini
# /etc/systemd/system/solar-task.service
[Unit]
Description=Solar cycle task
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/solar-cycle.sh
TimeoutStartSec=300

[Install]
WantedBy=multi-user.target
```

## 7.10 Debugging Low-Power Systems

| Challenge | Solution |
|-----------|---------|
| Can't serial debug during sleep | Use RTC memory / flash to log state; review after wake |
| Power measurement during sleep | Nordic PPK2 or µCurrent Gold; USB meters lack resolution |
| Intermittent crashes | Boot counter in non-volatile storage; log crash reason |
| WiFi won't connect after deep sleep | Add timeout + retry limit; don't block forever |
| Unexpected current draw in sleep | Measure with all peripherals disconnected; add back one by one |
| SD card corruption (Pi) | Use read-only root + overlay filesystem |
| Time drift (no RTC) | Use ESP32 RTC memory for seconds counter; sync via NTP when online |

### Current Profiling Workflow

1. **Establish baseline:** Measure MCU alone in deepest sleep
2. **Add regulator:** Measure sleep with regulator but no MCU → shows Iq
3. **Add peripherals one by one:** Each sensor, radio, LED — measure sleep delta
4. **Profile active cycle:** Capture full wake-process-sleep current trace with PPK2/Otii
5. **Calculate daily energy:** Integrate current × time for each mode
6. **Compare to harvest:** Verify harvest margin ≥ 2× in worst month

## 7.11 Summary

| Principle | Implementation |
|-----------|---------------|
| Sleep aggressively | Use deepest sleep mode; wake only to do work |
| Adapt to energy | Tiered power states based on battery SOC + solar availability |
| Never get stuck | Hardware WDT + software timeouts + boot counter safe mode |
| Buffer data | Batch transmissions; one radio session per N readings |
| Measure everything | Profile sleep current; kill hidden power consumers |
| Protect storage | Read-only filesystem on Pi; wear-leveled flash on MCUs |
| Plan for failure | OTA updates; graceful degradation; external watchdog |
| Simplify | Every line of code running costs energy. Do less. |

**The best solar firmware does almost nothing, almost all of the time.**

---

**Next:** [Chapter 8 — Ecological & Permacomputing](08-permacomputing.md)
