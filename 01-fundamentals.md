# Chapter 1: Fundamentals of Solar Power for Small Systems

## 1.1 Photovoltaic Physics (Practical Summary)

A **photovoltaic (PV) cell** converts photons into electrical current via the **photovoltaic effect**:

1. Photon strikes semiconductor (typically silicon), exciting an electron across the bandgap
2. Built-in electric field at the p-n junction sweeps carriers → current flows
3. Single silicon cell produces ~0.5–0.6V open-circuit regardless of size
4. Cell area determines current capacity (more area = more photons captured)

**Key insight for embedded designers:** Voltage is determined by *number of cells in series*. Current is determined by *cell area* and *irradiance*.

## 1.2 Solar Cell Types

| Type | Efficiency | Cost | Flexibility | Best For | Notes |
|------|-----------|------|-------------|----------|-------|
| **Monocrystalline (mono-Si)** | 20–24% | $$ | Rigid (unless cut thin) | Max power in small area | Most common for small panels |
| **Polycrystalline (poly-Si)** | 15–18% | $ | Rigid | Budget rigid panels | Blue speckled appearance |
| **Amorphous Silicon (a-Si)** | 6–10% | $ | Excellent | Indoor light, wearables | Works well in low/diffuse light |
| **CIGS (thin-film)** | 12–15% | $$$ | Good | Flexible panels | Copper Indium Gallium Selenide |
| **Organic (OPV)** | 5–12% | $ (at scale) | Excellent | Experimental, wearables | Short lifespan, improving rapidly |
| **Perovskite** | 25%+ (lab) | ? | Good | Future tech | Stability issues, not yet commercial for embedded |
| **GaAs (III-V)** | 28–30% | $$$$ | Thin-film possible | Space, military | Alta Devices; too expensive for most projects |

**For embedded/IoT projects:**
- **Outdoor, space-constrained:** Monocrystalline (IXYS/Anysolar KXOB25 series, Voltaic panels)
- **Indoor light harvesting:** Amorphous silicon (PowerFilm Indoor Light Series)
- **Wearables/flexible:** a-Si or CIGS thin-film (PowerFilm, custom flex panels)

## 1.3 Key Electrical Specifications

Every solar cell/panel has these critical specs (measured at **STC**: 1000 W/m², 25°C, AM1.5):

| Parameter | Symbol | What It Means | Typical (single Si cell) |
|-----------|--------|---------------|--------------------------|
| Open-Circuit Voltage | V_oc | Voltage with no load (max voltage) | 0.55–0.63V |
| Short-Circuit Current | I_sc | Current with zero resistance load (max current) | Proportional to area |
| Maximum Power Point Voltage | V_mp | Voltage at peak power | ~0.45–0.5V |
| Maximum Power Point Current | I_mp | Current at peak power | ~85–90% of I_sc |
| Maximum Power | P_max | V_mp × I_mp | — |
| Fill Factor | FF | P_max / (V_oc × I_sc) | 0.70–0.85 |
| Efficiency | η | P_max / (Irradiance × Area) | See table above |

**Fill Factor** indicates cell quality. FF > 0.75 is good. FF < 0.65 suggests losses (resistance, recombination).

## 1.4 The I-V Curve

```
  I (current)
  ^
  |___________
  |           \
  |            \        ← "knee" = Maximum Power Point (MPP)
  |             \
  |              \
  |               \
  |                \___
  +--------------------→ V (voltage)
  I_sc                   V_oc
```

**Critical concepts:**

- The **MPP (Maximum Power Point)** is where V × I is maximized — this is the "knee" of the curve
- A fixed resistive load rarely sits at the MPP → you lose power
- **MPPT circuits** dynamically adjust the load impedance to keep the panel at its MPP
- The I-V curve shifts with conditions:
  - **More light** → I_sc increases (nearly linear), V_oc increases slightly
  - **Higher temperature** → V_oc *decreases* (~−2mV/°C per cell), I_sc increases slightly
  - **Net effect of heat: less power** (V_oc drop dominates)

## 1.5 How Load Affects Output

| Scenario | What Happens |
|----------|-------------|
| **Load resistance too low** (near short circuit) | High current, low voltage, low power |
| **Load resistance too high** (near open circuit) | High voltage, low current, low power |
| **Load at MPP** | Optimal V × I, maximum power extracted |
| **Battery directly connected** (no MPPT) | Panel voltage clamped to battery voltage — may be far from MPP |

**Example:** A 6V panel (V_mp = 5.2V) connected directly to a 3.7V LiPo battery operates at 3.7V — potentially losing 30–40% of available power vs. MPPT.

## 1.6 Real-World Derating Factors

STC ratings are optimistic. Real-world output is always less:

| Factor | Impact | Notes |
|--------|--------|-------|
| **Irradiance** | Linear with power | Full sun = 1000 W/m². Overcast = 100–300 W/m². Indoor = 0.1–10 W/m² |
| **Panel angle** | cos(θ) loss | Perpendicular to sun = 100%. 45° off = ~70%. Flat horizontal varies by latitude/season |
| **Temperature** | −0.3 to −0.5%/°C | A panel at 65°C loses ~15–20% vs. STC (25°C) |
| **Shading** | Catastrophic if cells in series | One shaded cell in a series string can cut output by >50% |
| **Soiling** | 2–10% loss | Dust, bird droppings, condensation |
| **Aging** | ~0.5–1%/year | Degradation over decades |

### Irradiance Reference

| Condition | Irradiance (W/m²) | Fraction of STC |
|-----------|-------------------|-----------------|
| Bright direct sun | 900–1100 | ~100% |
| Light clouds | 400–700 | 40–70% |
| Heavy overcast | 100–300 | 10–30% |
| Indoor (bright office) | 2–10 | 0.2–1% |
| Indoor (dim) | 0.1–1 | 0.01–0.1% |

**Key takeaway for IoT designers:** Indoor light harvesting produces 100–1000× less power than outdoor. Design accordingly.

## 1.7 Peak Sun Hours (PSH)

**Peak Sun Hours** = total daily irradiance ÷ 1000 W/m². It represents how many hours of equivalent full sun you get per day.

| Location | Winter PSH | Summer PSH | Annual Avg |
|----------|-----------|-----------|------------|
| Seattle, WA | 1.0–1.5 | 5.0–6.0 | ~3.5 |
| New York, NY | 2.0–2.5 | 5.0–5.5 | ~3.8 |
| Phoenix, AZ | 4.5–5.0 | 7.0–8.0 | ~6.5 |
| London, UK | 0.5–1.0 | 4.0–5.0 | ~2.5 |
| Nairobi, Kenya | 4.5–5.5 | 4.5–5.5 | ~5.0 |

**Use NASA POWER** (power.larc.nasa.gov) or **PVGIS** (re.jrc.ec.europa.eu/pvgis/) for location-specific data.

## 1.8 Practical Calculations

### Energy Budget Example: Solar ESP32 Weather Station

**Load analysis:**
- ESP32 active (Wi-Fi TX): 160 mA @ 3.3V = 528 mW, for 5 seconds every 15 minutes
- ESP32 deep sleep: 10 µA @ 3.3V = 33 µW, remaining time
- Sensor readings: negligible vs. Wi-Fi

**Daily energy consumption:**
- Active periods: 24h × 4/hr = 96 wake-ups/day
- Active energy: 96 × 5s × 528 mW = 253,440 mJ = **253.4 J**
- Sleep energy: 86,400s × 0.033 mW = 2,851 mJ = **2.85 J**
- **Total: ~256 J/day = 71 mWh/day**

**Panel sizing (New York, winter worst-case: 2 PSH):**
- Need 71 mWh from 2 hours of equivalent sun
- Panel output needed: 71 / 2 = 35.5 mW at STC
- Add 50% margin for losses (MPPT efficiency, clouds, angle): **~55 mW panel**
- A small 0.5W (500 mW) panel provides >10× margin — very comfortable

**Battery sizing:**
- Need to survive 3 days without sun: 256 J × 3 = 768 J = 213 mWh
- At 3.7V: 213 / 3.7 = 58 mAh minimum
- With 20% DoD reserve: **~75 mAh minimum** → a 500–1000 mAh LiPo is very comfortable

### Quick Sizing Formula

```
Panel_watts = (Daily_consumption_Wh × Safety_factor) / Peak_Sun_Hours

Where:
  Daily_consumption_Wh = Σ(mode_power × mode_duration) over 24h
  Safety_factor = 1.5 to 3.0 (higher for critical/year-round systems)
  Peak_Sun_Hours = worst-month PSH for your location
```

## 1.9 Summary

| Concept | Key Point |
|---------|-----------|
| Cell voltage | ~0.5V per silicon cell; series cells for higher voltage |
| MPP | Always operate at the knee of the I-V curve for max power |
| Temperature | Heat = less voltage = less power |
| Shading | Devastating for series-connected cells |
| Indoor vs outdoor | 100–1000× less power indoors |
| Panel sizing | Use worst-month PSH with safety margin |
| For embedded | Even tiny panels (0.5–2W) can power most IoT nodes with duty cycling |

---

**Next:** [Chapter 2 — Solar Panels for Small Systems](02-solar-panels.md)
