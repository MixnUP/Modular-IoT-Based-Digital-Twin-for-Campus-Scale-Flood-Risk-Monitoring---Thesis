
## System Focus

**Each node now measures:**

* 🌧 **Rainfall** (rain gauge)
* 🌡 **Temperature**
* 💧 **Humidity**

**What you’re modeling:**

* Rain *input*
* Atmospheric conditions that **influence rainfall intensity, duration, and accumulation risk**

You are **not directly measuring flooding** — you are **predicting flood likelihood** based on weather behavior.

That distinction matters. Own it.

---

## 1️⃣ Modular Prototype (Updated Node Definition)

Each prototype is a **Weather Monitoring Node (WMN)**.

### Each node contains:

* ESP32 (Wi-Fi + controller)
* Tipping-bucket rain gauge
* DHT11 / DHT22 (temperature & humidity)
* Power (USB / battery / solar)
* Weatherproof enclosure

All **three nodes are identical**.

Only placement and ID differ.

---

## 2️⃣ What Makes It Modular (Still Strong)

### ✅ Hardware Modularity

* Same enclosure
* Same wiring
* Same sensor set
* Replace one node without touching others

> “Each node is a self-contained weather monitoring unit.”

---

### ✅ Firmware Modularity

All nodes run **one firmware**.

Only config values change:

```cpp
#define NODE_ID "NODE_1"
#define ZONE_ID "ZONE_A"
```

Rain gauge interrupt + DHT reading logic is identical everywhere.

---

### ✅ Data Modularity

Each node sends data in the same format:

```json
{
  "nodeId": "NODE_1",
  "zoneId": "ZONE_A",
  "rainfall": 12.6,
  "temperature": 28.4,
  "humidity": 91,
  "timestamp": "2026-02-03T10:30:00Z"
}
```

Visualization doesn’t care *which node* — it aggregates by `zoneId`.

---

## 3️⃣ How Flood Risk Is Now Interpreted (Critical Change)

Since you removed soil moisture and water level, your **flood logic becomes atmospheric**.

### Example Flood Risk Logic (Explainable)

```
Flood Risk =
  0.6 × Rainfall Intensity +
  0.25 × Rainfall Duration +
  0.15 × Humidity Index
```

**Why this works:**

* High humidity → slower evaporation
* Sustained rainfall → accumulation risk
* Intense rainfall → drainage overload risk

You are modeling **likelihood**, not water height.

That is defensible.

---

## 4️⃣ Visualization Changes (Very Important)

### ❌ What You Should NOT Show Anymore

* No water level rising animations
* No canal depth diagrams
* No “actual flood height”

That would be dishonest.

---

### ✅ What You SHOULD Show Instead

#### 🧩 1. Zone Weather State Panel

```
ZONE A – DRAINAGE ENTRANCES
Rainfall Rate: 14.2 mm/hr
Humidity: 92%
Temp: 28.1°C
Flood Risk: HIGH
```

---

#### 🧩 2. Rainfall-Centric Flow Diagram

```
🌧 Rainfall (Intensity + Duration)
        ↓
💧 High Humidity (Low Evaporation)
        ↓
⚠️ Drainage Overload Risk
        ↓
Flood Risk Level
```

This replaces the old soil/water diagram.

---

#### 🧩 3. Mini Campus Overlay (Still Valid)

* Static campus image
* One highlighted zone
* Color intensity = **predicted flood risk**
* Label: *“Weather-based flood risk estimate”*

This is key wording.

---

## 5️⃣ How to Justify Sensor Removal (Say This)

Use this exact logic in defense:

> “To maintain simplicity and ensure reliability, the system focuses on weather-driven flood indicators such as rainfall intensity, duration, and humidity, which are primary contributors to urban flooding due to drainage overload.”

That shuts down “why no water sensor?” questions.

---

## 6️⃣ Bottom Line (Straight Talk)

* Yes, you reduced physical sensing
* No, your project did not become weaker
* You shifted from **flood detection** → **flood risk prediction**
* Modularity is still strong because:

  * Same node design
  * Same firmware
  * Same visualization logic
  * Same digital twin abstraction
