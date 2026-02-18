# Chapter 2: Solar Panels for Small Systems

## 2.1 Panel Form Factors

| Form Factor | Typical Use | Pros | Cons |
|-------------|------------|------|------|
| **Rigid (glass/aluminum frame)** | Fixed outdoor installations, Pi systems | Durable, efficient, cheap per watt | Heavy, bulky, fragile glass |
| **Semi-flexible (ETFE/PET laminate)** | IoT enclosures, portable projects | Light, some flex, weather-resistant | Less durable than glass; can delaminate |
| **Flexible thin-film (a-Si, CIGS)** | Wearables, curved surfaces | Bendable, lightweight | Lower efficiency, more area needed |
| **PCB-mounted micro cells** | Energy harvesting, indoor | Solderable, tiny | Very low power (µW–mW) |

## 2.2 Panel Sizing Methodology

### Step 1: Calculate Daily Energy Budget

```
E_daily = Σ (P_mode × t_mode)  [in mWh or Joules]
```

### Step 2: Determine Available Solar Energy

```
E_solar = P_panel(STC) × PSH × η_system

Where:
  η_system = η_mppt × η_charge × η_regulator × derating
           ≈ 0.90  × 0.90    × 0.90      × 0.80
           ≈ 0.58 (typical end-to-end)
```

### Step 3: Size with Margin

```
P_panel(STC) = E_daily / (PSH_worst × η_system)
```

**Rule of thumb:** For year-round outdoor operation, panel capacity should be **3–5× average daily consumption** to handle worst-case months.

## 2.3 Recommended Panels by Application

### Wearable / Ultra-Low Power (< 1W)

| Panel | Type | Voc | Isc | Pmax | Size (mm) | Notes |
|-------|------|-----|-----|------|-----------|-------|
| **ANYSOLAR KXOB25-04X3F** | Mono-Si | 3.0V | 50mA | 110mW | 25×37 | Popular for BQ25570 designs; solderable |
| **ANYSOLAR KXOB25-02X8F** | Mono-Si | 4.8V | 25mA | 88mW | 22×25 | Higher voltage, lower current |
| **ANYSOLAR KXOB22-04X3F** | Mono-Si | 2.5V | 50mA | 84mW | 22×25 | Compact |
| **PowerFilm SP3-37** | a-Si | 3.0V | 50mA | 97mW | 36×64 | Flexible; good indoor performance |
| **PowerFilm LL200-3-37** | a-Si | 3.0V | 22µA | 42µW (200 lux) | 36×64 | **Indoor Light Series** — designed for indoor harvesting |
| **Voltaic P121 (2V 0.3W)** | Mono-Si | 2.5V | 170mA | 300mW | 55×35 | ETFE coated, rugged |

**Suppliers:** DigiKey, Mouser, Adafruit, PowerFilm direct

### IoT Node (1–5W)

| Panel | Type | Voc | Isc | Pmax | Size (mm) | Price (approx) |
|-------|------|-----|-----|------|-----------|----------------|
| **Voltaic 1W 6V (P105)** | Mono-Si | 7.0V | 200mA | 1W | 99×69 | ~$9 |
| **Voltaic 2W 6V (P106)** | Mono-Si | 7.0V | 380mA | 2W | 136×99 | ~$14 |
| **Voltaic 3.5W 6V (P107)** | Mono-Si | 7.0V | 640mA | 3.5W | 175×138 | ~$19 |
| **Seeed Studio 1W 5.5V** | Poly-Si | 6.5V | 170mA | 1W | 95×95 | ~$5 |
| **DFRobot 1W 5V Monocrystalline** | Mono-Si | 6.2V | 200mA | 1W | 90×60 | ~$5 |
| **Generic 6V 1W (AliExpress)** | Poly/Mono | 6.5–7V | 160–200mA | 1W | ~110×60 | ~$2–3 |

**Notes:**
- 6V nominal panels work well with single-cell LiPo/LiFePO4 charging (V_mp ~5–5.5V gives headroom above 4.2V charge voltage)
- Voltaic panels are premium quality with ETFE coating and tested reliability
- AliExpress panels are cheap but vary in quality; test before deploying

### Raspberry Pi Systems (10–25W)

| Panel | Voc | Pmax | Size | Notes |
|-------|-----|------|------|-------|
| **Voltaic 9W 18V (P109)** | 21V | 9W | 272×353 | Good for Pi Zero + battery |
| **Voltaic 17W 18V (P117)** | 21V | 17W | 430×353 | Comfortable for Pi 4/5 |
| **Generic 20W 18V** | 22V | 20W | ~350×450 | Requires 12V→5V buck |
| **Generic 25W 12V** | 15V | 25W | ~350×500 | Direct to 12V battery systems |
| **Renogy 10W 12V** | 14.4V | 10W | 337×255 | Quality brand, aluminum frame |

**Pi power requirements:**
- Pi Zero 2 W: ~0.4W idle, ~1W peak → 5W panel + 5000mAh battery is comfortable
- Pi 4: ~3W idle, ~6W peak → 15–20W panel + 10,000mAh+ battery
- Pi 5: ~4W idle, ~10W peak → 25W+ panel, requires careful power management

## 2.4 Indoor Light Harvesting Panels

Indoor light (~200–500 lux office lighting) produces only **0.1–1 W/m²** vs. 1000 W/m² for full sun. This requires specialized panels:

| Panel | Technology | Output at 200 lux | Output at 1000 lux | Size |
|-------|-----------|-------------------|---------------------|------|
| **PowerFilm Indoor Light Series** | a-Si | 10–50 µW | 50–250 µW | Various |
| **Panasonic AM-1815** | a-Si | ~35 µW (200 lux) | ~175 µW | 53×25 |
| **IXYS/Anysolar KXOB25** | Mono-Si | ~10 µW (200 lux) | ~50 µW | 22×25 |
| **Epishine LEH3 series** | Organic PV | ~40 µW (200 lux) | — | 52×27 |
| **[Panasonic AM-1417CA](https://www.digikey.com/en/products/detail/panasonic-energy/AM-1417CA-DGK-E/2165185)** | a-Si | ~30 µW (200 lux) | ~150 µW | 41×26 |
| **[ANYSOLAR SM111K04L](https://www.digikey.com/en/products/detail/anysolar-ltd/SM111K04L/9990435)** | Mono-Si | ~15 µW (200 lux) | ~80 µW | Small form factor |

**Why a-Si excels indoors:**
- Broader spectral response matches artificial light (LED, fluorescent)
- Better performance in diffuse/low light
- Monocrystalline cells are optimized for the solar spectrum, not indoor LED peaks

## 2.5 Photodiodes as Micro Solar Cells

Standard photodiodes like the **BPW34** can function as tiny solar cells, producing microwatts of power — enough for ultra-low-power BLE nodes when paired with an efficient harvester like the BQ25570.

- **BPW34** — silicon PIN photodiode, ~7mm² active area, produces ~50–100µW in direct sun
- Multiple BPW34s in series/parallel can reach voltages suitable for energy harvesting ICs
- Advantage: widely available, cheap (~$0.50), well-characterized, solderable

**Projects & References:**
- [PocketConcepts/Solar_nRF](https://github.com/PocketConcepts/Solar_nRF) — BPW34 solar-powered nRF BLE beacon, complete project with schematics
- [Ultra Low Power Energy Harvester from BPW34](https://hackaday.io/project/85457-ultra-low-power-energy-harvester-from-bpw34) — Hackaday project demonstrating BPW34 → BQ25504 → supercap → MCU

**When to consider photodiodes over solar cells:** When you need a standard, solderable component with a well-defined datasheet and your power budget is in the single-digit microwatt range (e.g., BLE beacons, RTC-only systems).

## 2.6 Connecting Cells: Series vs. Parallel

| Configuration | Effect | When to Use |
|---------------|--------|-------------|
| **Series** | Voltage adds, current = lowest cell | Need higher voltage (e.g., multiple cells → BQ25570 input) |
| **Parallel** | Current adds, voltage = lowest cell | Need more current, cells are matched voltage |
| **Mixed** | Series strings in parallel | Larger arrays (Pi systems) |

**⚠ Warning:** In series strings, the weakest/shadowed cell limits the entire string's current. Use bypass diodes on larger panels.

## 2.7 DIY vs. Commercial Panels

### DIY Approach
- Buy bare cells (ANYSOLAR, tabbed cells from AliExpress)
- Solder in series/parallel to desired voltage/current
- Encapsulate with epoxy, UV resin, or laminate
- **Pro:** Custom voltage/size, cheap
- **Con:** Fragile, inconsistent, weather sealing is hard

### Commercial Panels
- Pre-wired, tested, weather-sealed
- Consistent specs, sometimes with datasheet
- **Pro:** Reliable, time-saving
- **Con:** Limited size/voltage options, more expensive

**Recommendation:** Use commercial for production/outdoor. DIY for prototyping and learning.

## 2.8 Panel Testing & Verification

Always verify panel specs — especially cheap panels:

1. **Measure V_oc:** Multimeter in direct sun. Should match datasheet ±10%
2. **Measure I_sc:** Multimeter in ammeter mode, direct sun. ⚠ Only for small panels (<1A)
3. **Load test:** Connect a known resistor near R_mpp = V_mp/I_mp, measure voltage and current
4. **I-V curve tracing:** Sweep load from open to short circuit (variable resistor or electronic load)

**Quick sanity check:**
```
Expected I_sc (outdoor) ≈ Rated_I_sc × (Actual_irradiance / 1000)
```

## 2.9 Mechanical Considerations

| Concern | Notes |
|---------|-------|
| **Mounting angle** | Perpendicular to sun is ideal. For fixed installations, tilt = latitude gives best annual average |
| **Weatherproofing** | ETFE-coated panels survive outdoors. PET laminate degrades in UV. Glass is best long-term |
| **Connectors** | JST-PH 2.0mm (common on boards), bare wire, MC4 (larger panels) |
| **Weight** | Flexible panels: 50–200 g. Rigid glass panels: 500g–2kg for 10–25W |
| **Temperature** | Mount with airflow behind panel. Black surfaces on roofs can hit 65–80°C |

## 2.10 Key Suppliers

| Supplier | Specialty | URL |
|----------|-----------|-----|
| **Voltaic Systems** | Rugged small panels, IoT power kits | voltaicsystems.com |
| **PowerFilm Solar** | Flexible a-Si, indoor harvesting | powerfilmsolar.com |
| **ANYSOLAR (IXYS)** | Micro solar cells, solderable | anysolar.biz, DigiKey, Mouser |
| **Adafruit** | Breakout-friendly panels, tutorials | adafruit.com |
| **SparkFun** | Small panels, development boards | sparkfun.com |
| **DFRobot** | Budget panels, Arduino ecosystem | dfrobot.com |
| **Seeed Studio** | Panels, LoRa kits | seeedstudio.com |
| **AliExpress** | Cheapest bulk panels (verify specs!) | aliexpress.com |
| **Renogy** | Quality 10W+ panels | renogy.com |
| **Epishine** | Organic PV for indoor | epishine.com |

## 2.11 Summary & Quick Selection Guide

```
Need < 100mW indoor?     → PowerFilm Indoor Light Series or Panasonic AM-1815
Need < 1W wearable?      → ANYSOLAR KXOB25 series or PowerFilm flex
Need 1-5W IoT outdoor?   → Voltaic 1-3.5W 6V or generic 6V panels
Need 10-25W for Pi?      → Voltaic 9-17W or Renogy 10-20W with MPPT controller
Budget prototype?         → AliExpress 6V panels ($2-3) + verify specs
```

---

**Next:** [Chapter 3 — Energy Harvesting ICs & MPPT](03-energy-harvesting-ics.md)
