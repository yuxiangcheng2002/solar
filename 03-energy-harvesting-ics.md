# Chapter 3: Energy Harvesting ICs & MPPT

## 3.1 Maximum Power Point Tracking (MPPT) Explained

### The Problem
A solar panel's maximum power point (MPP) shifts with irradiance, temperature, and load. Connecting a panel directly to a battery wastes 20–50% of available energy because the battery clamps the panel voltage away from V_mp.

### MPPT Approaches

| Method | How It Works | Complexity | Efficiency |
|--------|-------------|-----------|------------|
| **Perturb & Observe (P&O)** | Periodically adjusts operating point, measures if power increased or decreased | Medium | Good, but oscillates around MPP |
| **Fractional Open-Circuit Voltage (FOCV)** | Periodically measures V_oc, sets V_mp ≈ 0.70–0.80 × V_oc | Low | Good enough for most embedded. ~95–98% of true MPP |
| **Fractional Short-Circuit Current** | Periodically measures I_sc, sets I_mp ≈ 0.85 × I_sc | Low | Good, but shorting panel wastes energy |
| **Incremental Conductance** | Compares dI/dV to −I/V | High | Excellent, fast convergence |
| **Constant Voltage** | Regulates input to a fixed voltage (set by resistor divider) | Lowest | Adequate if conditions are stable |

**For embedded energy harvesting ICs:** Most use **FOCV** (BQ25570, AEM10941, ADP5090) or **constant voltage** (CN3791). These are simple, low-power, and effective for small panels.

### How FOCV Works in Practice
1. IC momentarily disconnects panel (or opens a high-Z sample switch)
2. Measures V_oc
3. Sets the boost converter input regulation to V_mp = k × V_oc (k typically 0.78–0.80, set by external resistor)
4. Repeats every 4–16 seconds
5. During the brief V_oc sample (~256ms), no energy is harvested — acceptable overhead

## 3.2 Energy Harvesting IC Comparison

| IC | Mfr | V_in Range | Cold Start | P_in Range | MPPT Type | Outputs | Storage | Package | Price (1pc) |
|----|-----|-----------|------------|-----------|-----------|---------|---------|---------|-------------|
| **BQ25570** | TI | 100mV–5.1V | 330mV / 15µW | µW–mW | FOCV (80%) | Buck: 1.8–3.6V | Li-ion, supercap | QFN-20 (3.5×3.5) | ~$4–5 |
| **BQ25504** | TI | 130mV–5.1V | 600mV / 15µW | µW–mW | FOCV (80%) | VSTOR only | Li-ion, supercap | QFN-16 (3×3) | ~$3–4 |
| **AEM10941** | e-peas | 50mV–5V | 380mV / 3µW | µW–100mW | FOCV (configurable) | 2× LDO: 1.2–3.3V | Li-ion, supercap, thin-film | QFN-28 (5×5) | ~$5–7 |
| **ADP5090** | ADI | 80mV–3.3V | 380mV | 16µW–200mW | FOCV (programmable) | SYS, PG | Li-ion, supercap | LFCSP-16 (3×3) | ~$4–5 |
| **SPV1050** | ST | 150mV–18V | — | µW–mW | FOCV | 2× LDO: 1.8V, 3.3V | Li-ion, supercap | QFN-20 (4×4) | ~$3–4 |
| **LTC3105** | ADI | 225mV–5V | 250mV | mW–400mA out | MPPC (constant V_in) | Adjustable VOUT + 6mA LDO | External | DFN-12/MSOP-12 | ~$5–6 |
| **CN3791** | Consonance | 4.5V–6V typ | N/A (needs >4.5V) | 100mW–10W | Constant voltage | 4.2V (1S Li-ion) | Li-ion | SOP-8 | ~$0.30–0.50 |

## 3.3 Detailed IC Profiles

### TI BQ25570 — The Gold Standard for Ultra-Low Power Harvesting

**Best for:** µW–mW harvesting from small solar cells, TEGs. Wearables, tiny IoT nodes.

**Key specs:**
- Input: 100mV–5.1V (after start), cold start at 330mV
- Quiescent current: 488 nA (typical)
- Integrated boost charger + nano-power buck converter
- Buck output: 1.8–3.6V, up to 100mA (from storage)
- MPPT: FOCV, ratio set by resistor (ROC1/ROC2), sampled every 16s
- Battery management: programmable OV/UV thresholds via resistor dividers
- VBAT_OK output: signals when battery is in good range (use as MCU enable)

**Typical circuit:**
```
Solar Cell → VIN_DC ─┐
                      │
              ┌───────┴───────┐
              │   BQ25570     │
              │               │─── VOUT_BUCK (3.3V) → MCU
              │               │─── VBAT_OK → MCU_EN
              └───────┬───────┘
                      │
              Battery/Supercap
```

**Design notes:**
- Use with ANYSOLAR KXOB25-04X3F (3V, 50mA) for outdoor micro-harvesting
- For supercap storage: set OV threshold to supercap max rating
- VBAT_OK hysteresis prevents MCU from oscillating on/off at threshold
- TI provides excellent **SLUC484** reference design and **BQ25570EVM** evaluation module (~$49)

**Datasheet:** [ti.com/lit/ds/symlink/bq25570.pdf](https://www.ti.com/lit/ds/symlink/bq25570.pdf)

---

### TI BQ25504 — Simpler Sibling

**Best for:** When you don't need the integrated buck converter. Slightly lower cold-start voltage requirement when paired with external regulation.

**Key differences from BQ25570:**
- No integrated buck converter (need external LDO/regulator)
- Cold start: 600mV (higher than BQ25570's 330mV)
- Otherwise very similar MPPT and battery management
- Slightly smaller package (QFN-16 vs QFN-20)

**When to choose BQ25504 over BQ25570:**
- You already have a downstream regulator
- You need the VSTOR voltage directly (e.g., charging a supercap at the storage voltage)
- Cost-sensitive designs

**Datasheet:** [ti.com/lit/ds/symlink/bq25504.pdf](https://www.ti.com/lit/ds/symlink/bq25504.pdf)  
**DigiKey:** [BQ25504RGTR](https://www.digikey.com/en/products/detail/texas-instruments/BQ25504RGTR/2799286)

---

### e-peas AEM10941 — Best-in-Class Solar Harvester

**Best for:** Solar harvesting from 1–7 cell panels, indoor and outdoor. Advanced dual-output system.

**Key specs:**
- Input: 50mV–5V (lowest cold start in class at 380mV from just 3µW!)
- Boost efficiency: up to 97%
- Two regulated LDO outputs (1.2V–3.3V each, configurable)
- Supports primary battery backup (for when solar is unavailable)
- MPPT: FOCV with configurable ratio (0.3–0.9 × V_oc via resistor)
- Quiescent: 400 nA

**Advantages over BQ25570:**
- Lower cold-start power (3µW vs 15µW)
- Dual regulated outputs (no external buck needed for 2-rail systems)
- Primary battery backup input
- Higher boost efficiency

**Evaluation board:** AEM10941-QFN28-EVK

**Datasheet:** [mouser.com/datasheet/2/1087/e_peas_AEM10941_datasheet](https://www.mouser.com/datasheet/2/1087/e_peas_AEM10941_datasheet_energy_harvesting-2325372.pdf)  
**App note:** [e-peas.com/documents/AEM10941/AN-AEM10941.pdf](https://e-peas.com/documents/AEM10941/AN-AEM10941.pdf)  
**Ready-made breakouts:** [Jasper Sikken's AEM10941 Solar Harvesting Boards](https://www.hackster.io/news/jasper-sikken-s-solar-harvesting-boards-make-it-easy-to-integrate-e-peas-aem10941-into-your-designs-fb1e976c0ab6) — open-hardware breakout boards for easy prototyping

---

### Analog Devices ADP5090 — Flexible Harvester

**Best for:** PV and TEG harvesting, 16µW–200mW range.

**Key specs:**
- Input: 80mV–3.3V
- Cold start: 380mV
- Integrated boost with synchronous rectification
- MPPT: FOCV, programmable ratio and minimum V_oc threshold (down to 100mV)
- Programmable battery UV/OV protection
- Backup battery path (SYS output switches to backup when primary is low)
- Quiescent: 250 nA (boost disabled), 585 nA (boost enabled)

**Unique feature:** Boost can be temporarily disabled via external pin to prevent RF interference — useful for BLE/LoRa nodes that need clean RF during transmit.

**Datasheet:** [analog.com/media/en/technical-documentation/data-sheets/adp5090.pdf](https://www.analog.com/media/en/technical-documentation/data-sheets/adp5090.pdf)

---

### ST SPV1050 — Versatile, Wide Input Range

**Best for:** Systems needing wide input voltage range (up to 18V). Works with TEG and PV.

**Key specs:**
- Input: 150mV–18V (widest range in this class)
- Boost + buck-boost topology (transformerless)
- Two LDO outputs: 1.8V and 3.3V (fixed)
- MPPT: FOCV
- Can charge Li-ion or supercap

**Advantage:** Can work with higher-voltage panels (e.g., 12V/18V) that other harvesters can't accept.

**Eval board:** STEVAL-ISV020V1

**Datasheet:** [st.com/resource/en/datasheet/spv1050.pdf](https://www.st.com/resource/en/datasheet/spv1050.pdf)

---

### Analog Devices LTC3105 — Higher Power Step-Up

**Best for:** Mid-range solar harvesting (mW to hundreds of mW). When you need more output current than BQ25570 can provide.

**Key specs:**
- Input: 225mV–5V, start-up from 250mV
- Output: adjustable, up to 5.25V
- Output current: up to 400mA (from sufficient source)
- Integrated MPPC (Maximum Power Point Control) — sets minimum input voltage via resistor
- Auxiliary 6mA LDO output (for always-on circuits)
- Burst Mode® for light loads

**Key difference: MPPC vs MPPT:**  
LTC3105 uses **MPPC** — it regulates the *input voltage* to a programmed minimum (set by MPPC pin resistor). This is essentially constant-voltage MPPT. Works well when panel characteristics are known.

**Typical application:** 1–3W solar panel → LTC3105 → 3.3V or 5V rail → ESP32/nRF52 + sensors

**Datasheet:** [analog.com/media/en/technical-documentation/data-sheets/3105fb.pdf](https://www.analog.com/media/en/technical-documentation/data-sheets/3105fb.pdf)

---

### CN3791 — Budget MPPT Charge Controller

**Best for:** Simple, cheap solar charging of 1S LiPo/Li-ion. Great for Arduino/ESP32 hobby projects.

**Key specs:**
- Input: 4.5–6V (designed for 6V panels)
- Charge voltage: 4.2V ±1% (1S Li-ion)
- Max charge current: 4A (2A typical with standard boards)
- MPPT: Constant voltage method (MPPT pin voltage regulated to 1.205V; resistor divider sets panel regulation point)
- PWM switching: 300 kHz
- Package: SOP-8

**Why it's popular:**
- **$0.30–0.50** per chip (vs $4–5 for BQ25570)
- Pre-built modules available on Amazon/AliExpress for **$2–4**
- Dead simple: connect solar panel + LiPo battery + done
- Handles higher power than energy harvesting ICs (watts, not milliwatts)

**Limitations:**
- No cold start from low voltage (needs >4.5V)
- No boost capability — panel voltage must exceed battery voltage
- No integrated output regulation
- No supercap support
- Fixed 4.2V charge (not configurable for LiFePO4's 3.6V without mods)

**Board variants:**
- 6V MPPT version (most common): set for ~5.5V panel regulation
- 9V/12V versions available with different MPPT resistor

**Datasheet:** Available from [laskakit.cz/user/related_files/dse-cn3791.pdf](https://www.laskakit.cz/user/related_files/dse-cn3791.pdf)

## 3.4 Evaluation Boards & Breakouts

| Board | IC | Price | Source |
|-------|-----|-------|--------|
| **BQ25570EVM-206** | BQ25570 | ~$49 | TI, DigiKey, Mouser |
| **CJMCU-25504** | BQ25504 | ~$5–10 | AliExpress (clone breakout) |
| **MikroElektronika Solar Energy Click** | BQ25570 | ~$30 | MikroE, DigiKey |
| **AEM10941-QFN28-EVK** | AEM10941 | ~$90 | e-peas, Mouser |
| **ADP5090-1 EVALZ** | ADP5090 | ~$55 | ADI, DigiKey |
| **STEVAL-ISV020V1** | SPV1050 | ~$35 | ST, DigiKey |
| **Adafruit Universal USB/DC Solar Charger** | BQ24074 | ~$17 | Adafruit |
| **SparkFun Sunny Buddy** | LT3652 | ~$15 | SparkFun |
| **CN3791 Module** | CN3791 | ~$2–4 | AliExpress, Amazon |

## 3.5 Decision Tree: Which IC?

```
What's your power level?
│
├── µW to low mW (indoor, tiny outdoor cell)
│   ├── Need ultra-low cold start? → AEM10941 (380mV / 3µW)
│   ├── Need proven ecosystem + buck? → BQ25570
│   └── Budget, no buck needed? → BQ25504
│
├── mW to ~200mW (small outdoor panel)
│   ├── Need RF-quiet operation? → ADP5090
│   ├── Wide input voltage (>5V)? → SPV1050
│   └── Need more output current? → LTC3105
│
└── 100mW to several watts (6V+ panel, higher power)
    ├── Budget / simple 1S LiPo charge? → CN3791 ($0.30!)
    ├── USB + solar input? → BQ24074 (Adafruit charger)
    └── Higher voltage / Pi systems? → Dedicated MPPT controller
```

## 3.6 DIY MPPT Circuits

For learning or when commercial ICs don't fit:

### Simple FOCV MPPT with ATtiny/Arduino

1. Use a MOSFET switch to disconnect panel, measure V_oc
2. Calculate V_mp = 0.78 × V_oc
3. Use PWM-controlled buck/boost converter
4. Feedback loop: adjust PWM duty cycle to keep panel voltage at V_mp
5. Re-measure V_oc periodically (every 10–30 seconds)

**Components:**
- ATtiny85 or similar ($1)
- Si MOSFET for switching (e.g., IRLML6344, $0.20)
- Inductor + Schottky diode (or synchronous FET)
- Voltage divider for ADC measurement

**Open-source projects:**
- **MPPT Solar Charger** by Lukas Fässler (GitHub: LibreSolar) — ARM-based, well-documented
- **Tim Nolan's Arduino MPPT** — classic reference design

### When DIY Makes Sense
- Learning / education
- Unusual panel configurations
- Need features no IC provides (e.g., logging, custom algorithms)
- **When DIY doesn't make sense:** Production IoT devices — use a tested IC

## 3.7 Application Notes Worth Reading

| App Note | Source | Topic |
|----------|--------|-------|
| **SLUC484** | TI | BQ25570 reference design for solar |
| **SLUUAA9** | TI | Designing with BQ25570 — detailed calculations |
| **AN-AEM10941** | e-peas | PV harvesting with AEM10941 |
| **AN-1444** | ADI | ADP5090 design guide |
| **DN536** | ADI (LT) | LTC3105 solar harvesting demo |
| **AN4113** | ST | SPV1050 energy harvesting design |

## 3.8 Summary

| Need | Best Choice | Why |
|------|------------|-----|
| Absolute lowest power, proven | BQ25570 | Industry standard, excellent docs |
| Best cold start, dual output | AEM10941 | 3µW cold start, 97% efficiency |
| RF-sensitive application | ADP5090 | Boost disable pin |
| Higher voltage panels (12V+) | SPV1050 | 18V input range |
| More current (>100mA) | LTC3105 | 400mA output, step-up |
| Cheapest, simple LiPo charge | CN3791 | $0.30, available everywhere |

---

**Next:** [Chapter 4 — Battery Management](04-battery-management.md)
