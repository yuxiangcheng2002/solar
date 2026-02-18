# Chapter 4: Battery Management

## 4.1 Battery Chemistry Comparison

| Chemistry | Nominal V | Charge V | Discharge Cutoff | Energy Density | Cycle Life | Self-Discharge | Safety | Best For |
|-----------|----------|----------|-------------------|---------------|-----------|----------------|--------|----------|
| **LiPo (Li-ion polymer)** | 3.7V | 4.2V | 3.0V | 150–250 Wh/kg | 300–500 | 3–5%/month | Moderate (fire risk) | Compact IoT, wearables |
| **LiFePO4 (LFP)** | 3.2V | 3.6V | 2.5V | 90–130 Wh/kg | 2000–5000+ | 1–3%/month | Excellent | Long-life outdoor, solar |
| **NiMH** | 1.2V | 1.4–1.5V | 1.0V | 60–120 Wh/kg | 500–1000 | 15–30%/month (standard) | Excellent | Budget, high-temp |
| **Supercapacitor** | 2.5–2.7V/cell | Rated V | 0V (usable) | 5–10 Wh/kg | 500k–1M+ | 10–30%/day | Excellent | Burst power, infinite cycle |
| **Thin-film Li** | 3.7–4.1V | 4.2V | 3.0V | 10–50 Wh/kg | 1000+ | Very low | Excellent | Wearables, PCB-mount |

## 4.2 Chemistry Deep Dives

### LiPo / Li-ion (Lithium Polymer)

**The default choice for compact solar IoT.**

- **Charge profile:** CC-CV (constant current until 4.2V, then constant voltage until current tapers to ~C/10)
- **Never** discharge below 3.0V or charge above 4.2V — permanent damage / fire risk
- **Charge rate:** Typically 0.5C–1C (e.g., 500mA for a 500mAh cell)
- **Temperature:** Charge only 0–45°C. Discharge −20 to 60°C. **Do not charge below 0°C** — lithium plating → fire risk
- **Common sizes for IoT:** 100–2000mAh pouch cells, 18650 cylindrical (2000–3500mAh)

**Popular charge controller ICs:**

| IC | Charge Current | Features | Package | Price |
|----|---------------|----------|---------|-------|
| **MCP73831** | Up to 500mA | Simple, SOT-23-5, one resistor sets current | SOT-23-5 | ~$0.50 |
| **TP4056** | Up to 1A | Ubiquitous module ($0.50 on AliExpress), DW01 protection | SOP-8 | ~$0.20 |
| **BQ24074** | Up to 1.5A | Power path, USB + DC input, DPPM | QFN-24 | ~$3 |
| **BQ25895** | Up to 3A | Buck-boost, USB PD, I2C config | QFN-24 | ~$4 |
| **CN3791** | Up to 4A | MPPT solar, covered in Ch.3 | SOP-8 | ~$0.30 |

### LiFePO4 (LFP)

**Ideal for long-life solar installations. Superior safety and cycle life.**

- **Charge voltage:** 3.6V (vs 4.2V for LiPo) — important for charge IC selection!
- **Flat discharge curve:** Stays at ~3.2V for most of discharge — excellent for 3.3V systems
- **Cycle life:** 2000–5000+ cycles at 80% DoD (vs 300–500 for LiPo)
- **Safer:** No thermal runaway; won't catch fire
- **Cold weather:** Better than LiPo (can discharge to −30°C, charge to −10°C with derating)
- **Downside:** Lower energy density (larger/heavier for same capacity)

**Why LiFePO4 is great for solar IoT:**
1. Many more charge cycles (daily charge/discharge for years)
2. Flat 3.2V output is close to 3.3V MCU rail — sometimes usable with just an LDO
3. Tolerates partial charge/discharge well (no memory effect, no need for full cycles)
4. Safer unattended outdoors

**LiFePO4 charge ICs:**
- **CN3058E** — dedicated LFP charger, SOP-8, cheap (~$0.30)
- **MCP73123** — Microchip LFP charger, adjustable current
- Most LiPo chargers (MCP73831, TP4056) charge to 4.2V — **wrong for LFP!**
- Some boards have a resistor mod to set 3.6V charge voltage
- BQ25570 can be configured for LFP via its OV threshold resistors

### NiMH (Nickel-Metal Hydride)

- **When to use:** Budget, wide temperature range, safety-critical, AAA/AA form factor convenience
- **Charge:** ΔV detection (voltage dip at full charge) or timed charging
- **Self-discharge:** High (use **Eneloop** or similar LSD types: ~10%/year)
- **Voltage:** 1.2V/cell — need 3 cells for 3.6V (awkward for 3.3V systems)
- **Not recommended for solar IoT** unless using AAA/AA form factor for easy field replacement

### Supercapacitors

**Not batteries — but essential for energy harvesting systems.**

| Parameter | Supercap | LiPo Battery |
|-----------|----------|-------------|
| Energy density | 5–10 Wh/kg | 150–250 Wh/kg |
| Power density | 10,000+ W/kg | 250–1000 W/kg |
| Cycle life | 500,000–1,000,000 | 300–500 |
| Charge time | Seconds | Hours |
| Self-discharge | 10–30%/day | 3–5%/month |
| Temperature range | −40 to +65°C | 0 to 45°C (charge) |
| Voltage per cell | 2.5–2.7V | 3.7V |

**When to use supercaps:**
- **Burst power:** WiFi/cellular TX bursts (ESP32: 500mA for 100ms)
- **Infinite cycle life needed:** Daily cycling for 10+ years
- **Extreme temperature:** −40°C operation
- **No overnight storage needed:** System sleeps without power at night, cold-starts at dawn
- **Hybrid:** Supercap for burst + small battery for overnight

**Popular supercaps for IoT:**

| Part | Capacitance | Voltage | ESR | Size | Price |
|------|------------|---------|-----|------|-------|
| **Eaton HV0810-2R7105-R** | 1F | 2.7V | 75Ω | 8×10mm | ~$2 |
| **Eaton HB1030-2R5106-R** | 10F | 2.5V | 30mΩ | 10×30mm | ~$5 |
| **AVX BestCap BZ015B503Z** | 50mF | 5.5V | 4Ω | 15×8mm | ~$3 |
| **Illinois Cap DGH105Q2R5** | 1F | 2.5V | 100mΩ | 8×13mm | ~$2 |
| **Murata DMF3Z5R5H224** | 220mF | 5.5V | — | 7.5×4.5mm | ~$3 |

**Energy stored in a supercap:**
```
E = ½CV² [Joules]

Example: 1F charged from 2.5V to 5.0V (two 2.7V caps in series)
E = ½ × 0.5F_effective × (5.0² − 2.5²) = ½ × 0.5 × 18.75 = 4.7 J

That's enough for ~18 ESP32 WiFi transmissions (260mJ each)
```

### Thin-Film Batteries

- **Cymbet CBC050** — 50µAh, rechargeable, PCB-mountable, rated for 5000+ cycles
- **Ilika Stereax** — solid-state thin-film, tiny form factor
- **Best for:** PCB-level energy storage alongside supercaps
- **Very low capacity** — only for µW-level loads (RTC backup, BLE beacons)

## 4.3 Battery Sizing for Solar Systems

### Method

```
Battery_capacity = (Daily_consumption × Days_of_autonomy) / (DoD × η_discharge)

Where:
  Daily_consumption = from energy budget (Ch.1)
  Days_of_autonomy = 2–5 (winter/cloudy resilience)
  DoD = max depth of discharge (0.8 for LiPo, 0.9 for LFP, 0.5 conservative)
  η_discharge = regulator efficiency (~0.90)
```

### Example: ESP32 Weather Station

From Ch.1: 71 mWh/day at 3.3V

```
At 3.7V battery: 71 / 3.7 = 19.2 mAh/day
3 days autonomy, 80% DoD, 90% efficiency:
  Battery = (19.2 × 3) / (0.8 × 0.9) = 80 mAh minimum

→ Use a 500mAh LiPo (6× margin — handles degradation, cold weather)
```

### Example: Raspberry Pi Zero Solar

Pi Zero 2 W: ~0.5W average with light load = 12 Wh/day

```
At 3.7V: 12,000 / 3.7 = 3,243 mAh/day
2 days autonomy, 80% DoD:
  Battery = (3,243 × 2) / (0.8 × 0.9) = 9,009 mAh

→ Use 10,000mAh LiPo or 2× 18650 (3500mAh each, in parallel = 7000mAh — tight)
```

## 4.4 Charge Profiles

### CC-CV (LiPo / Li-ion / LiFePO4)

```
Current ───────────┐
                    │  CC phase
                    │  (constant current)
                    │
                    └──────────── CV phase
                                 (constant voltage,
Voltage  ──────────────────────  current tapers)
          slowly rising    │
                           V_charge (4.2V LiPo / 3.6V LFP)
```

**Termination:** Current drops below C/10 (e.g., 50mA for 500mAh cell)

**Solar-specific concern:** Panel may not provide enough current for full CC phase. This is fine — the charge IC will simply charge at whatever current is available (trickle charging). Most charge ICs handle this gracefully.

### Trickle Charging Considerations

With small solar panels, charge current may be very low (µA–mA):
- Most Li-ion charge ICs have a minimum charge current threshold
- Below this threshold, some ICs won't enter charge mode
- **BQ25570/BQ25504** handle this well — designed for µW harvesting
- **TP4056** may not start charging below ~30mA — not ideal for tiny panels

## 4.5 Battery Protection

### Required Protections

| Protection | Why | How |
|-----------|-----|-----|
| **Over-voltage (OV)** | Prevents overcharge (fire, venting) | Charge IC cutoff; dedicated protection IC |
| **Under-voltage (UV)** | Prevents deep discharge (permanent damage) | Protection IC disconnects load; BQ25570 VBAT_OK |
| **Over-current (OC)** | Prevents excessive discharge (heat, damage) | Protection IC current limit; PTC fuse |
| **Short-circuit (SC)** | Prevents catastrophic failure | Protection IC fast disconnect |
| **Over-temperature** | Prevents charging in unsafe conditions | NTC thermistor + charge IC TEMP pin |

### Protection ICs

| IC | Features | Package | Notes |
|----|----------|---------|-------|
| **DW01A** | OV/UV/OC/SC for 1S Li-ion | SOT-23-6 | Paired with FS8205A dual MOSFET. On every TP4056 module |
| **BQ29700** | OV/UV for 1S, programmable thresholds | SOT-23-6 | TI, works with BQ25570 systems |
| **S-8261** | 1S protection, very low Iq (3µA) | SOT-23-5 | Seiko, good for ultra-low power |
| **XB5353A** | 1S protection | SOT-23-5 | Budget option |

### NTC Thermistor Integration

Most charge ICs (TP4056, BQ24074, etc.) have a TEMP/TS pin:
- Connect a 10kΩ NTC thermistor between TS and GND
- IC monitors resistance → pauses charging outside safe range (typically 0–45°C)
- **Critical for outdoor solar:** Panels on hot roofs can heat batteries nearby
- **Critical for cold climates:** Must prevent charging below 0°C

## 4.6 Cold Weather Considerations

| Issue | Temperature | Impact | Mitigation |
|-------|------------|--------|------------|
| **Li-ion charging** | < 0°C | Lithium plating → dendrites → short circuit → fire | **Never charge below 0°C.** Use NTC + charge disable |
| **Li-ion capacity** | −20°C | 50–70% capacity loss | Oversize battery; use LFP if possible |
| **LiFePO4 charging** | < −10°C | Reduced acceptance, risk of plating | Charge at reduced rate; use self-heating if available |
| **LiFePO4 discharge** | −30°C | Works but reduced capacity | Acceptable for most applications |
| **Supercap** | −40°C | Reduced capacitance (~30–50% of rated) | Oversize; still functional |
| **NiMH** | −20°C | Severely reduced capacity | Not recommended for cold solar |

**Cold-weather design pattern:**
1. Use NTC thermistor to disable charging below 0°C
2. Insulate battery compartment (foam, etc.)
3. Oversize battery by 2× for cold capacity loss
4. Consider LiFePO4 for better cold tolerance
5. Supercap for burst power even in extreme cold

## 4.7 Supercapacitor vs. Battery Trade-offs

| Criterion | Supercap Wins | Battery Wins |
|-----------|--------------|-------------|
| Overnight energy storage | ✗ (too much self-discharge) | ✓ |
| >10 year lifespan | ✓ (1M+ cycles) | ✗ (batteries degrade) |
| Burst current for radio TX | ✓ (low ESR, high pulse current) | ✗ (voltage sag) |
| −40°C operation | ✓ | ✗ (Li-ion fails) |
| Size for given Wh | ✗ (10–50× larger) | ✓ |
| No maintenance, no fire risk | ✓ | ✗ |
| Cost per Wh | ✗ ($10–50/Wh) | ✓ ($0.50–2/Wh) |

**Hybrid approach (recommended for many designs):**
- Supercap for burst power (WiFi/LoRa TX) + small LiFePO4 for overnight storage
- BQ25570 charges supercap as primary storage; battery backs up for dark periods

## 4.8 Battery Management System (BMS) for Multi-Cell

For Pi-class systems using multiple cells:

| Configuration | Typical BMS | Notes |
|--------------|-------------|-------|
| **2S Li-ion (7.4V)** | BQ29209 + FETs, HY2120 | Cell balancing important |
| **3S Li-ion (11.1V)** | BQ76920, S-8254A | For 12V solar systems |
| **4S LiFePO4 (12.8V)** | BQ76920, JBD BMS modules | Direct replacement for 12V lead-acid |
| **1S (most IoT)** | DW01A or charge IC built-in | Keep it simple |

**For IoT: Stick with 1S whenever possible.** Multi-cell adds complexity, cost, and failure modes.

## 4.9 Practical Recommendations by Application

### Wearable / Ultra-Low Power
- **Storage:** 50–200mAh LiPo or 0.1–1F supercap
- **Charge IC:** BQ25570 (manages both charge and protection thresholds)
- **Protection:** BQ25570's programmable OV/UV via resistors

### IoT Sensor Node (ESP32, nRF52)
- **Storage:** 500–2000mAh LiPo or 1S LiFePO4 (e.g., IFR14500 = AA-size, 600mAh)
- **Charge IC:** CN3791 (budget) or BQ25504 + external LDO
- **Protection:** DW01A + FS8205A (on most LiPo modules) or TP4056 module

### Raspberry Pi Solar
- **Storage:** 10,000mAh+ LiPo, or 4S LiFePO4 (12.8V) with 5V buck
- **Charge:** Dedicated MPPT controller (Victron SmartSolar 75/10, EPever Tracer, etc.)
- **Alternative:** Voltaic V75 battery + Voltaic panel kit (integrated USB output)

### Permacomputing / 10+ Year Deploy
- **Storage:** LiFePO4 (longest cycle life) or supercap-only (if overnight storage not needed)
- **Charge IC:** BQ25570 + LiFePO4 (configure OV to 3.6V)
- **Strategy:** Massive over-provisioning of panel + battery; system survives even at 50% degradation

## 4.10 Summary

| Chemistry | Use When | Avoid When |
|-----------|----------|------------|
| **LiPo** | Compact, moderate lifespan OK | Cold, long-term deploy, safety-critical |
| **LiFePO4** | Long life, outdoor, safety | Space-constrained, need max energy density |
| **Supercap** | Burst power, extreme temp, infinite cycle | Need overnight storage, size matters |
| **NiMH** | Budget, AA form factor, high temp | Self-discharge matters, compact needed |
| **Thin-film** | PCB-mount, µW loads | Anything requiring >1mAh |

**Golden rule:** For solar IoT, **LiFePO4 + small supercap** is the most robust combination. For compact wearables, **LiPo + BQ25570** is the proven path.

---

**Next:** [Chapter 5 — Power Regulation & Distribution](05-power-regulation.md)
