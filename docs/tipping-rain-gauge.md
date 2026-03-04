
## How the Microcontroller Gathers Rainfall Data (Tipping Rain Gauge)

### 1️⃣ Physical Rainfall Detection

Rain falls into a **funnel** that directs water into a **tipping bucket mechanism**.

* Each bucket tip corresponds to a **fixed rainfall amount** (e.g., 0.2 mm).
* When the bucket tips, it briefly closes a **reed switch or magnetic contact**.

This converts **rainfall (mechanical)** → **electrical signal**.

---

### 2️⃣ Signal Input to the Microcontroller

* The switch output is connected to a **digital input pin** on the microcontroller (ESP32/Arduino).
* Each tip generates a **LOW–HIGH or HIGH–LOW pulse**.

The microcontroller does **not measure voltage** — it **counts events**.

---

### 3️⃣ Pulse Counting Using Interrupts

To avoid missing tips during heavy rain:

* The input pin is configured as an **interrupt pin**.
* Every time a pulse is detected, an **interrupt service routine (ISR)** increments a counter.

```cpp
void IRAM_ATTR tipDetected() {
  tipCount++;
}
```

This ensures accurate counting even if the MCU is doing other tasks.

---

### 4️⃣ Debouncing (Signal Stability)

Because the switch may bounce:

* A **short debounce delay** (e.g., 50–100 ms) is applied in software.
* Prevents false multiple counts per tip.

This improves measurement reliability.

---

### 5️⃣ Rainfall Calculation

At fixed intervals (e.g., every 1 minute):

```
Rainfall (mm) = Number of tips × Rain per tip
Rainfall rate (mm/hr) = Rainfall × (60 / measurement interval)
```

Example:

* 5 tips in 1 minute
* 0.2 mm per tip
  → Rainfall rate = **60 mm/hr**

---

### 6️⃣ Data Packaging and Transmission

The microcontroller:

* Resets the counter after each interval
* Packages the rainfall value with:

  * Node ID
  * Zone ID
  * Timestamp
* Sends data to the cloud (Firebase) via Wi-Fi

```json
{
  "rainfall_mm_hr": 60,
  "timestamp": "2026-02-03T10:45:00Z"
}
```

---

## Why This Method Is Suitable for Your Thesis

* ✔ Simple and robust
* ✔ Event-based (low power, accurate)
* ✔ Widely used in professional weather stations
* ✔ Easy to explain and defend

---

## One-Liner for Defense (Use This)

> “Rainfall is measured by counting discrete tipping events from a rain gauge, where each tip represents a calibrated rainfall volume, allowing the microcontroller to compute rainfall intensity accurately over time.”

