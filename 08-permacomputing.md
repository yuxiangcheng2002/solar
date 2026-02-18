# Chapter 8: Ecological & Permacomputing

## 8.1 Permacomputing: Philosophy & Principles

**Permacomputing** is a radically sustainable approach to computing inspired by permaculture. Core idea: maximize hardware lifespan, minimize energy use, favor already-available computational resources.

**Key principles** (from permacomputing.net):

| Principle | What It Means for Solar Projects |
|-----------|----------------------------------|
| **Care for life** | Computing should serve ecological well-being, not just convenience |
| **Care for the chips** | Extend hardware lifespan — repair, reuse, repurpose |
| **Keep it small** | Minimal software on modest hardware = less energy = smaller panel |
| **Hope for the best, prepare for the worst** | Design for intermittent power, degraded networks, component failure |
| **Keep it flexible** | Modular designs that adapt to available salvaged components |
| **Build on solid ground** | Simple, proven tech over bleeding-edge complexity |
| **Amplify awareness** | Make energy use visible — show the battery level, embrace downtime |

**Why it matters for embedded solar:** Permacomputing aligns perfectly with solar-powered embedded systems. Both require ruthless efficiency, graceful degradation, and long operational lifetimes with minimal maintenance.

## 8.2 LOW←TECH MAGAZINE Solar-Powered Website

The most famous permacomputing project. Since **September 2018**, LOW←TECH MAGAZINE has run a solar-powered website at `solar.lowtechmagazine.com`.

### Technical Architecture

```
  50W Solar Panel (Barcelona rooftop)
       │
  Lead-acid battery (later LiFePO4)
       │
  Charge controller (Victron SmartSolar MPPT 75/10)
       │
  Olimex A20-OLinuXino-LIME2 SBC (later Raspberry Pi)
       │
  Armbian Linux → Pelican static site generator → Nginx
       │
  Internet via residential connection
```

**Key design decisions:**
- **Static site generator** (Pelican/Hugo) — no database, no PHP, minimal CPU per request
- **Dithered images** — all images converted to compressed dithered versions (dramatic size reduction, ~10× smaller)
- **No tracking, no ads, no third-party scripts** — reduces page weight and server load
- **Default dark theme** — signals low-energy design philosophy
- **Battery indicator** on every page — makes energy availability visible to visitors
- **The site goes offline** when the battery is depleted — this is a feature, not a bug. Downtime makes the solar dependency tangible.
- **Page size:** Average ~0.5 MB vs typical web page of ~2.5 MB (5× reduction)

### Energy Budget (approximate)

| Component | Power Draw |
|-----------|-----------|
| SBC (idle, serving pages) | ~2–4W |
| Network equipment share | ~1W |
| **Total average** | **~3–5W** |

With a 50W panel in Barcelona (~4.5 peak sun hours/day):
- Daily harvest: 50W × 4.5h × 0.8 (MPPT efficiency) ≈ **180 Wh/day**
- Daily consumption: 5W × 24h = **120 Wh/day**
- **Surplus in summer, deficit in winter** — hence occasional downtime Dec–Feb

### Lessons Learned
1. **Web design is energy design.** A lighter page = less CPU = less power = smaller panel
2. **Accepting downtime** reframes reliability — 95% uptime is fine for most purposes
3. **Dithered images** are surprisingly readable and aesthetically distinctive
4. **Static sites** are ideal for solar — no database queries, content cached in RAM

**Source:** solar.lowtechmagazine.com/about/the-solar-website/

## 8.3 Solar Protocol

**Solar Protocol** is an art/research project by Tega Brain, Alex Nathanson, and Benedetta Piantella. A global network of small solar-powered servers routes web traffic to whichever server has the most sunlight.

### How It Works

```
  Server Nodes (worldwide):
  ┌─────────┐  ┌─────────┐  ┌─────────┐
  │Barcelona│  │New York │  │ Sydney  │
  │ ☀☀☀☀☀  │  │  ☁☁☀   │  │  🌙     │
  └────┬────┘  └────┬────┘  └────┬────┘
       │            │            │
       ▼            ▼            ▼
  DNS / load balancer routes to sunniest server
       │
       ▼
  User sees content from the most solar-powered node
```

### Hardware per Node
- **Raspberry Pi** (2/3/4 depending on generation)
- **Small solar panel** (6W–20W)
- **Charge controller** (various, including Voltaic/Genasun)
- **LiFePO4 or lead-acid battery**
- **Sensors:** INA219 current/voltage sensor for real-time power monitoring
- **Software:** Python Flask app, custom DNS routing logic

### Key Concepts
- **Sun-based routing:** Traffic follows the sun around the planet — an "ecologically responsive" protocol
- **Energy as design variable:** The network's behavior changes with weather, seasons, time of day
- **Distributed resilience:** No single point of failure (though nodes can go offline individually)
- **Community stewardship:** Each node is maintained by a local "steward" at universities, art centers, and homes worldwide

**GitHub:** github.com/alexnathanson/solar-protocol
**Paper:** "Solar Protocol: Exploring Energy-Centered Design" (Computing within Limits, 2022)

## 8.4 Other Notable Solar Computing Projects

### Dries Buytaert's Solar Website (2024)
- Raspberry Pi Zero 2 W on a Boston rooftop
- Solar panel + LiFePO4 battery
- Static site (Hugo) with aggressive optimization
- Inspired directly by LOW←TECH MAGAZINE
- **Source:** solar.dri.es

### SunBox / Off-Grid Pi Projects
Various community projects running Raspberry Pi as:
- **Local mesh servers** (LibraryBox, PirateBox successors)
- **Environmental monitoring stations** (solar + Pi Zero + sensors)
- **Community bulletin boards** (solar-powered Wi-Fi AP serving local HTML)

### Collapse OS / Collapse Informatics
- **Collapse OS:** A minimal operating system designed to run on salvaged Z80 processors
- Philosophy: computing should work even with a broken global supply chain
- Relevant overlap: solar-powered Z80 or AVR systems as "post-collapse" computing
- **Source:** collapseos.org

### Solar-Powered LoRa/Meshtastic Networks
- Community mesh networks using solar-powered nodes
- Each node: ESP32 + SX1262 LoRa + small solar panel + LiPo
- Provides off-grid text communication over kilometers
- Growing rapidly since 2023 — see Chapter 9 for build details

## 8.5 E-Waste & Ecological Considerations

### The Problem
- **53.6 million tonnes** of e-waste generated globally per year (2019, UN Global E-waste Monitor)
- Only **17.4%** formally collected and recycled
- Embedded electronics (IoT sensors, smart devices) contribute a growing share
- Solar panels themselves have a 25–30 year lifespan, but small consumer panels are often discarded much sooner

### Permacomputing Responses

| Strategy | Implementation |
|----------|---------------|
| **Extend hardware life** | Use Raspberry Pi, ESP32 — long software support, huge community |
| **Design for repair** | Through-hole components where possible, socketed ICs, accessible battery |
| **Salvage components** | Harvest 18650 cells from laptop batteries, panels from garden lights |
| **Minimize BOM** | Fewer components = less to manufacture, less to dispose of |
| **Choose LiFePO4** | 5000+ cycles vs 300–500 for LiPo = fewer battery replacements |
| **Avoid planned obsolescence** | Open-source firmware, no cloud dependencies |
| **Use standard connectors** | USB-C, JST-PH, screw terminals — not proprietary |

### Salvaging 18650 Cells
Old laptop battery packs contain 4–8 cells (sometimes more). Many cells retain 70–80% capacity:

1. **Safely disassemble** the pack (insulated tools, cut one wire at a time)
2. **Test each cell** with a hobby charger (e.g., LiitoKala Lii-500): charge to 4.2V, measure capacity
3. **Grade cells:** >2000mAh = good for solar projects, 1000–2000mAh = light duty, <1000mAh = recycle
4. **Match cells** by capacity (±100mAh) if building packs in parallel/series
5. **Always add protection** — BMS board for packs, or TP4056 module per cell

**Caution:** Only salvage cells that show no physical damage (swelling, rust, dents). Dispose of damaged cells at battery recycling points.

### Harvesting Solar Cells from Garden Lights
Dollar-store solar garden lights contain small amorphous silicon cells (~2V, 30–60mA):
- Desolder from the PCB
- Wire 2–3 in series for ~5–6V
- Useful for ultra-low-power projects (BQ25570 + supercap + ATtiny)
- Free, but low power output — suitable for µW–mW harvesting only

## 8.6 Design for Longevity

### Hardware Longevity Checklist

- [ ] **Conformal coating** on PCBs for outdoor deployment (MG Chemicals 422B or similar)
- [ ] **IP65+ enclosure** or potted electronics for weather resistance
- [ ] **No electrolytic capacitors** in critical paths (use MLCC or tantalum — longer life)
- [ ] **Wide temperature range** components (industrial grade: −40 to +85°C)
- [ ] **Replaceable battery** with accessible connector
- [ ] **OTA firmware updates** so hardware never needs physical retrieval
- [ ] **Watchdog timer** to recover from firmware crashes
- [ ] **Solar panel oversizing** (2× minimum) to account for degradation and worst-case weather
- [ ] **Modular design** — separate power, compute, and sensor boards via headers/connectors

### Software Longevity

- **Bare-metal or RTOS** over full Linux for embedded nodes (less attack surface, faster boot, lower power)
- **No cloud dependencies** — if the cloud service shuts down, the device should still function
- **Local data logging** with optional upload — device is useful even without network
- **Open-source firmware** — community can maintain after original developer moves on
- **Simple protocols** — MQTT, HTTP, LoRa — not proprietary vendor APIs

## 8.7 Energy-Proportional Design

A permacomputing principle applied to solar systems: **the system's activity should be proportional to available energy.**

```
  Energy Available vs System Behavior:

  Battery Full (>80%):
    → Full duty cycle, transmit frequently, backlight on
    → Run optional features (logging, display updates)

  Battery Mid (40–80%):
    → Normal operation, standard duty cycle
    → Reduce non-essential activity

  Battery Low (20–40%):
    → Extended sleep intervals
    → Transmit critical data only
    → Disable display

  Battery Critical (<20%):
    → Emergency data burst, then deep sleep
    → Wait for sunrise

  Battery Dead:
    → System off. Hardware survives. Restarts with next harvest.
```

**Implementation pattern (ESP32 pseudocode):**
```c
float battery_pct = read_battery_percent();

if (battery_pct > 80) {
    sleep_interval_ms = 60000;     // 1 min between readings
    transmit_every_n = 1;          // every reading
} else if (battery_pct > 40) {
    sleep_interval_ms = 300000;    // 5 min
    transmit_every_n = 1;
} else if (battery_pct > 20) {
    sleep_interval_ms = 900000;    // 15 min
    transmit_every_n = 4;          // every 4th reading
} else {
    // Critical: one last transmission, then hibernate
    transmit_emergency();
    esp_deep_sleep(3600000000ULL); // 1 hour
}
```

This pattern ensures the device **never fully depletes the battery** and adapts gracefully to cloudy days, winter months, or panel degradation.

## 8.8 Further Reading

| Resource | Type | URL |
|----------|------|-----|
| Permacomputing Wiki | Principles & projects | permacomputing.net |
| LOW←TECH MAGAZINE (solar) | Live solar-powered site | solar.lowtechmagazine.com |
| Solar Protocol | Art/research project | solarprotocol.net |
| Computing within Limits | Academic workshop | computingwithinlimits.org |
| Collapse OS | Minimal salvage-friendly OS | collapseos.org |
| Branch Magazine | Sustainable web design | branch.climateaction.tech |
| "Permacomputing Aesthetics" | Mansoux & Barok, 2023 | Referenced in permacomputing.net |

---

**Key takeaway:** Permacomputing isn't just philosophy — it directly informs practical design decisions for solar-powered embedded systems. Smaller software → less power → smaller panel → less e-waste → longer lifespan. The constraints of solar power and the goals of permacomputing are naturally aligned.
