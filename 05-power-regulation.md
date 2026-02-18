# Chapter 5: Power Regulation & Distribution

## 5.1 Why Regulation Matters in Solar Systems

Solar-powered systems face unique regulation challenges:

- **Variable input voltage:** Panel output swings with irradiance (0V to V_oc)
- **Battery voltage drift:** LiPo ranges 3.0–4.2V over discharge; LiFePO4: 2.5–3.6V
- **Multiple voltage rails:** MCU needs 3.3V, sensors may need 1.8V, radios need stable supply
- **Efficiency is survival:** Every mW wasted in regulation is a mW not available to the load
- **Quiescent current kills:** In deep sleep, the regulator's own consumption can dominate total system draw

**The regulator you choose can make or break a solar IoT design.** A 50µA-quiescent LDO on a system that sleeps at 10µA means 83% of your sleep power is wasted in the regulator.

## 5.2 Regulator Types

| Type | How It Works | Efficiency | Noise | Iq Range | Best For |
|------|-------------|-----------|-------|----------|----------|
| **LDO (Low Dropout)** | Linear pass element drops excess voltage as heat | V_out/V_in (e.g., 3.3/4.2 = 78%) | Very low | 25 nA – 50 µA | Low-noise, small ΔV, sleep-mode supply |
| **Buck (step-down)** | Switching converter, inductor-based | 85–95% | Higher (switching noise) | 60 nA – 20 µA | V_in >> V_out (12V→3.3V), higher loads |
| **Boost (step-up)** | Switching converter, inductor-based | 80–93% | Higher | 1–50 µA | V_in < V_out (single cell → 5V) |
| **Buck-Boost** | Handles V_in above or below V_out | 80–92% | Higher | 5–50 µA | Battery systems where V_bat crosses V_out |

### When to Use LDO vs. Buck

```
LDO makes sense when:
  V_in − V_out < 1V  (e.g., 3.7V LiFePO4 → 3.3V = 0.4V drop, ~89% efficient)
  Load current < 100mA
  Need ultra-low noise (analog sensors, ADC reference)
  Ultra-low Iq matters more than efficiency

Buck makes sense when:
  V_in >> V_out (e.g., 12V → 3.3V = 27% LDO efficiency vs 90%+ buck)
  Load current > 100mA
  Total power saved exceeds switching noise concerns
```

**Example loss calculation:**
- 12V battery → 3.3V @ 50mA load
- LDO: P_waste = (12 − 3.3) × 50mA = **435 mW wasted** (only 165 mW useful = 28% efficient)
- Buck (90%): P_waste = 165/0.9 − 165 = **18 mW wasted**

## 5.3 Ultra-Low Quiescent Current LDOs

For solar IoT, the LDO's quiescent current (Iq) is critical during sleep. Here are the best options:

| IC | Iq (typ) | Iq (max) | I_out max | V_in range | Dropout | Package | Price |
|----|----------|----------|-----------|-----------|---------|---------|-------|
| **TI TPS7A02** | 25 nA | 50 nA | 200 mA | 1.1–6.5V | 300mV @ 200mA | DSBGA-4 (0.64×0.64mm), SOT-23-5 | ~$0.60 |
| **Microchip MCP1711** | 600 nA | 1.1 µA | 150 mA | 0.6–6.0V | 450mV @ 150mA | SOT-23-5 | ~$0.50 |
| **Microchip MCP1700** | 1.6 µA | 4 µA | 250 mA | up to 6.0V | 178mV @ 250mA | SOT-23-3, TO-92 | ~$0.35 |
| **TI TLV733P** | 5.7 µA | 10 µA | 300 mA | 1.4–5.5V | 200mV @ 300mA | SOT-23-5 | ~$0.30 |
| **ST STLQ015** | 1 µA | 1.4 µA | 150 mA | 1.5–5.5V | 200mV @ 150mA | TSOT-5 | ~$0.40 |
| **Torex XC6220** | 8 µA | 15 µA | 200 mA | 1.5–6.0V | 270mV | SOT-25 | ~$0.50 |
| **Diodes AP7361C** | 55 µA | — | 1 A | 1.5–6.0V | 340mV @ 1A | SOT-223, SOT-89 | ~$0.30 |
| **Richtek RT9080** | 0.5 µA | 1 µA | 600 mA | 1.2–5.5V | 280mV @ 600mA | TSOT-23-5 | ~$0.35 |

**Standout: TI TPS7A02** — At 25 nA quiescent, this is the industry's lowest-Iq LDO. Perfect as always-on supply for RTC or MCU sleep rail. Available in an absurdly tiny 0.64×0.64mm DSBGA package or the more hand-solderable SOT-23-5.

**Workhorse: Microchip MCP1700** — The classic IoT LDO. 1.6µA Iq, TO-92 through-hole option for prototyping, ultra-low dropout (178mV). Available everywhere.

**Datasheet references:**
- TPS7A02: [ti.com/lit/ds/symlink/tps7a02.pdf](https://www.ti.com/lit/ds/symlink/tps7a02.pdf)
- MCP1700: [ww1.microchip.com/downloads/en/DeviceDoc/MCP1700-Data-Sheet-20001826F.pdf](https://ww1.microchip.com/downloads/en/DeviceDoc/MCP1700-Data-Sheet-20001826F.pdf)

## 5.4 Ultra-Low Quiescent Current Buck Converters

When you need switching efficiency with nanoamp-level quiescent current:

| IC | Iq (typ) | I_out max | V_in range | V_out | Efficiency | Package | Price |
|----|----------|-----------|-----------|-------|-----------|---------|-------|
| **TI TPS62840** | 60 nA | 750 mA | 1.8–6.5V | 0.4–5.5V (ext. divider) | Up to 95% | DSBGA-9 (1.6×1.6mm), SOT-23-6 | ~$1.20 |
| **TI TPS62802** | 60 nA | 200 mA | 1.8–5.5V | 0.6–4.5V | Up to 92% | DSBGA-6 (0.9×1.2mm) | ~$0.80 |
| **Torex XC9265** | 200 nA | 300 mA | 2.5–6.0V | 0.8–4.0V | Up to 90% | USP-6B (1.0×1.2mm) | ~$1.00 |
| **Maxim MAX38640A** | 330 nA | 700 mA | 1.8–5.5V | 0.5–3.95V | Up to 94% | WLP-6 (1.12×0.84mm) | ~$1.50 |
| **TI TPS62740** | 360 nA | 300 mA | 2.2–5.5V | 1.8–3.3V (pin-selectable) | Up to 90% | DSBGA-9, QFN-12 | ~$1.50 |
| **Analog Devices LTC3388** | 720 nA | 50 mA | 2.7–20V | 1.2–5V | Up to 90% | DFN-10, MSOP-10 | ~$4.00 |

**Standout: TI TPS62840** — 60 nA Iq with 750mA output capability. The go-to buck converter for solar IoT. Output voltage set by external resistor divider (allows any voltage). Features a VSEL pin for on-the-fly output voltage switching (e.g., 3.3V active, 1.8V sleep).

**Wide-input: Analog Devices LTC3388** — Accepts up to 20V input, perfect for 12V/18V panel systems feeding a Pi. 720 nA quiescent is excellent for a wide-input device. Burst mode for light loads.

**Breakout boards:**
- **olmBoard TPS62840** — Elecrow/Lectronz, ~$8 — breadboard-friendly
- **Pololu 3.3V step-down (D24V5F3)** — cheap, but Iq is ~15µA (fine for Pi, bad for µW IoT)

## 5.5 Power Path Management

**Problem:** Solar systems need to simultaneously charge a battery AND power the load. Without proper power path management:
- Battery might discharge through the solar panel at night (reverse current)
- Load loses power during battery switchover
- Battery never charges if load draws all panel current

### Power Path Topologies

#### Topology 1: Charge-Then-Use (Simplest)

```
Panel → Charge IC → Battery → Regulator → Load
```

- Battery is always in the power path
- Load powered from battery even when sun is available
- **Pro:** Simple; charge IC handles everything
- **Con:** All power cycles through battery (efficiency loss, wear)

#### Topology 2: Ideal Diode OR-ing

```
Panel → Regulator ──→ ┐
                       OR → Load
Battery → Regulator ──→ ┘
```

- Uses ideal diode controllers (e.g., **LTC4412**, **TPS2113A**) to select higher-voltage source
- Panel powers load directly when available; battery takes over when panel drops
- **Pro:** Reduces battery cycling; efficient
- **Con:** More complex; need careful voltage management

#### Topology 3: Dynamic Power Path Management (DPPM)

```
Panel → DPPM IC ─┬→ System Load (priority)
                  └→ Battery (remaining current)
```

- **BQ24074/BQ24075** family implements this
- System load has priority; remaining current charges battery
- If load exceeds panel capacity, battery supplements
- **Pro:** Best of both worlds; integrated solution
- **Con:** Limited to specific ICs

### Key Power Path ICs

| IC | Type | V_in | I_charge | Features | Price |
|----|------|------|----------|----------|-------|
| **TI BQ24074** | DPPM charger | 4.35–6.4V | 1.5A | Solar input, power path, USB/DC | ~$3 |
| **TI BQ24075** | DPPM charger | 4.35–6.5V | 1.5A | Similar, separate IN/USB pins | ~$3 |
| **TI BQ25570** | Boost harvester | 0.1–5.1V | — | VSTOR powers load directly; built-in power path | ~$4 |
| **LTC4412** | Ideal diode controller | 2.5–28V | External FET | Low-loss OR-ing between sources | ~$3 |
| **TPS2113A** | Auto-switching mux | 2.8–5.5V | 1.5A | Automatic source selection, 60µA Iq | ~$1.50 |
| **MAX40200** | Ideal diode | 0–5.5V | 1A | 20mV forward drop (vs 400mV Schottky) | ~$0.80 |

### Preventing Reverse Current

Without protection, current flows backward from battery through panel at night:

| Solution | Forward Drop | Iq/Loss | Notes |
|----------|-------------|---------|-------|
| **Schottky diode** (e.g., BAT54) | 300–500 mV | Zero Iq | Simplest; wastes power |
| **Ideal diode IC** (e.g., MAX40200) | 20–50 mV | ~1 µA | Low loss; recommended |
| **P-MOSFET + controller** (LTC4412) | <20 mV | ~8 µA | Lowest loss; more complex |
| **Charge IC built-in** (BQ25570, CN3791) | N/A | N/A | IC handles it internally |

## 5.6 Voltage Sequencing for Multi-Rail Systems

Some systems need multiple voltages that must power up in order:

```
Example: ESP32 + LoRa + Sensor
  Rail 1: 3.3V → MCU, LoRa (main rail)
  Rail 2: 1.8V → Sensor (must come up after MCU to avoid bus contention)
  Rail 3: 5.0V → Actuator (only when needed)
```

### Approaches

| Method | How | When |
|--------|-----|------|
| **RC delay on enable** | R-C network on LDO enable pin delays turn-on | Simple 2-rail systems |
| **Power-good (PG) cascading** | LDO1 PG output → LDO2 EN input | Multi-rail, sequential |
| **MCU-controlled** | GPIO enables downstream regulators | Most flexible; MCU must be up first |
| **Dedicated sequencer** (TPS3808, MAX16000) | IC controls sequence and monitors | Complex systems, high reliability |

**For most IoT:** MCU GPIO + load switches is the simplest and most flexible. MCU wakes → enables sensor power → takes reading → disables sensor power → sleeps.

## 5.7 Load Switches & Power Gating

**Power gating** turns off entire subsystems when not in use. Critical for solar IoT — sensor that draws 5mA continuously when it's only read once per hour wastes 99.97% of its energy.

### Load Switch ICs

| IC | Ron | I_leak (off) | I_max | Iq | V_in | Features | Price |
|----|-----|-------------|-------|-------|------|----------|-------|
| **TI TPS22860** | 1.0 Ω | 0.1 nA | 200 mA | 10 nA | 0–5.5V | Ultra-low leakage, bidirectional | ~$0.80 |
| **TI TPS22918** | 55 mΩ | 10 nA | 2 A | 1 µA | 1.0–5.5V | Low Ron, fast turn-on (60µs) | ~$0.40 |
| **TI TPS22919** | 120 mΩ | 10 nA | 1.5 A | 1 µA | 1.0–5.5V | Similar, lower cost | ~$0.35 |
| **Vishay SiP32431** | 70 mΩ | — | 1 A | 1 µA | 1.1–5.5V | Tiny SOT-23-5 | ~$0.30 |
| **AP22811** | 110 mΩ | — | 1.2 A | 8 µA | 2.5–5.5V | With overcurrent protection | ~$0.25 |

**Standout: TI TPS22860** — 0.1 nA leakage when off. The ultimate power gating switch. When you power-gate a 5mA sensor with this, the off-state waste is unmeasurable.

### DIY Alternative: P-Channel MOSFET Switch

For prototyping or budget designs, a P-MOSFET controlled by MCU GPIO works:

```
V_bat ──┤S    D├── Sensor V+
        │ PFET │
  GPIO ─┤G     │
        └──────┘

Pull-up R (100kΩ) from G to S ensures OFF when GPIO is floating/high.
MCU drives GPIO LOW to turn ON the MOSFET.
```

**Recommended P-MOSFETs for load switching:**

| Part | Vds | Rds(on) | Vgs(th) | Package | Notes |
|------|-----|---------|---------|---------|-------|
| **DMG2305UX** | −20V | 60 mΩ | −0.7V | SOT-23 | Works with 3.3V GPIO |
| **Si2301** | −20V | 100 mΩ | −0.7V | SOT-23 | Very common, cheap |
| **IRLML6402** | −20V | 65 mΩ | −0.7V | SOT-23 | Classic choice |
| **AO3401** | −30V | 55 mΩ | −1.0V | SOT-23 | May need level shifter for 1.8V GPIO |

### What to Power-Gate

| Subsystem | Typical I_idle | Power-Gate? | Notes |
|-----------|---------------|-------------|-------|
| GPS module | 20–30 mA | **Yes** | Only need fix every N minutes |
| LoRa radio | 1–3 mA (idle) | **Yes** | Enable only for TX/RX windows |
| SD card | 100–200 µA | **Yes** | Power-cycle between writes |
| Environmental sensor (BME280) | 3.6 µA sleep | Maybe | Sleep current already low |
| Display (e-ink) | 0 µA (after refresh) | No | E-ink holds image with zero power |
| Display (OLED SSD1306) | ~10 mA | **Yes** | Blank doesn't mean off |
| Level shifters, pull-ups | 10–100 µA each | **Yes** | Resistive dividers always draw current |
| External flash/EEPROM | 1–10 µA standby | Maybe | Most have sleep commands; gate if no SW control |

## 5.8 Practical Regulator Chains by Application

### Wearable (µW Harvesting)

```
KXOB25 solar cell → BQ25570 (boost + MPPT)
  ├── VSTOR → Supercap (100mF–1F, 2.5V)
  └── VOUT_BUCK (1.8V) → nRF52 MCU

Total regulator chain Iq: ~500 nA (BQ25570 only, no external regulator needed)
```

### IoT Sensor Node (mW–W)

```
6V panel → CN3791 (MPPT charge) → LiFePO4 (3.2V)
  └── MCP1700 (3.3V LDO, 1.6µA Iq) → ESP32/nRF52
      └── GPIO → TPS22860 (load switch) → Sensors

Total always-on Iq: ~2 µA (MCP1700 + leakage)
```

### Raspberry Pi System

```
20W 18V panel → MPPT controller → 12V lead-acid/LiFePO4
  └── LTC3388 buck (3.3V/5V, 720nA Iq) or Pololu D24V50F5 (5V 5A)
      └── 5V → Pi via USB-C or GPIO header

Note: Pi draws 2–6W continuously — Iq of regulator is irrelevant.
      Focus on buck efficiency at 500mA–2A load.
```

### Permacomputing Node

```
Oversized panel → BQ25570 → LiFePO4 (primary)
  ├── TPS7A02 (3.3V, 25nA Iq) → Always-on RTC + voltage monitor
  └── TPS62840 (3.3V, 60nA Iq) → MCU main rail (enabled by RTC alarm)
      └── GPIO → TPS22860 → Sensors, radio

Total sleep Iq: <100 nA (regulator chain) + MCU sleep
```

## 5.9 Practical Tips

### LDO Input/Output Capacitors

Every LDO needs input and output capacitors for stability:
- **Input:** 1–10µF ceramic (X5R/X7R), close to VIN pin
- **Output:** 1–10µF ceramic, close to VOUT pin
- Check datasheet for **minimum ESR requirements** — some LDOs oscillate with too-low-ESR ceramics (older designs)
- TPS7A02 and MCP1700 are stable with standard MLCC ceramics

### Buck Converter Layout

- Keep inductor, input cap, output cap, and IC close together
- Short, wide traces for switching nodes
- Ground plane under converter
- Keep switching noise away from ADC/analog signals
- **Ferrite bead** between buck output and sensitive analog circuits

### Thermal Considerations

LDOs dissipate `P = (V_in − V_out) × I_load` as heat:
- 12V → 3.3V @ 500mA = **4.35W** → SOT-23 package will burn. Use a buck converter.
- 4.2V → 3.3V @ 50mA = **45mW** → SOT-23 is fine.

## 5.10 Summary

| Need | Best Part | Why |
|------|----------|-----|
| Lowest Iq LDO | TPS7A02 (25 nA) | Always-on rail for MCU sleep |
| Workhorse LDO | MCP1700 (1.6 µA) | Cheap, available, good enough |
| Lowest Iq buck | TPS62840 (60 nA) | When LDO efficiency is unacceptable |
| Wide-input buck | LTC3388 (720 nA, 20V in) | 12V/18V panel → 3.3/5V |
| Power path | BQ24074 (DPPM) | Solar + battery + load, integrated |
| Load switch | TPS22860 (0.1 nA leak) | Power-gating sensors/radios |
| Budget load switch | P-MOSFET (Si2301) | Prototyping, cost-sensitive |
| Reverse current block | MAX40200 (ideal diode) | Panel → battery path |

**Key takeaway:** In a solar system sleeping 99% of the time, the regulator quiescent current is often the single largest power consumer. Choose accordingly.

---

**Next:** [Chapter 6 — System Design Patterns](06-system-design-patterns.md)
