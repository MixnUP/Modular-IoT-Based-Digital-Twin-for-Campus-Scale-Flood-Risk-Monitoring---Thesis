
## Methodology

### System Overview

This study implements a **modular IoT-based environmental sensing system** for campus-scale rainfall monitoring. The system collects **rainfall, ambient temperature, and relative humidity** data using a custom-fabricated rain gauge and digital sensors. Data are processed by a microcontroller and transmitted to a centralized platform for visualization and analysis within a digital twin environment.

---

### Custom 3D-Printed Tipping-Bucket Rain Gauge Design

Instead of using a commercial rain gauge, a **DIY tipping-bucket rain gauge** is fabricated using **3D printing technology**. The design files are sourced from open-access repositories (Instructables) and printed using durable plastic material suitable for outdoor deployment.

The rain gauge consists of:

* A funnel to collect rainfall
* A pivoting dual-bucket tipping mechanism
* A mounting enclosure for sensors

Each bucket is designed to tip after collecting a fixed volume of water, corresponding to a known rainfall depth.

---

### Rainfall Detection Using Hall Effect Sensor

Rainfall events are detected using a **Hall effect sensor–magnet pair**. A small permanent magnet is attached to the tipping bucket assembly. Each time the bucket tips, the magnet passes near the Hall effect sensor, generating a digital pulse.

The microcontroller:

* Monitors the Hall effect sensor via a digital input pin
* Counts each pulse as one “tip” event
* Converts pulse counts into rainfall depth using a predefined calibration constant (e.g., mm per tip)

This approach provides reliable, contactless detection and reduces mechanical wear compared to reed switches.

---

## Temperature and Humidity Measurement

Ambient **temperature and relative humidity** are measured using a digital temperature–humidity sensor installed within each sensing node. The sensor is placed in a **ventilated but rain-shielded enclosure** to ensure accurate atmospheric readings while preventing direct water exposure.

### Fixed-Interval Sampling

Temperature and humidity data are acquired at **fixed time intervals** (e.g., every 1 or 5 minutes), independent of rainfall events. Fixed-interval sampling is used because atmospheric parameters change **gradually** compared to rainfall tipping events, making continuous high-frequency sampling unnecessary and power-inefficient.

This interval-based approach provides:

* Stable trend monitoring
* Reduced sensor noise
* Lower power consumption
* Predictable data timestamps for analysis

### Synchronization with Rainfall Data

All sensor readings are **timestamped using the microcontroller’s internal clock**. While rainfall is recorded using **event-based detection** (each bucket tip), temperature and humidity readings are time-based. Synchronization is achieved by aligning all sensor data using a **common timestamp reference**.

When rainfall occurs between two fixed sampling intervals:

* The rain gauge counts tipping events continuously
* The accumulated rainfall is associated with the **nearest or enclosing time window**
* Rainfall intensity is computed relative to the same interval used for temperature and humidity

This ensures that environmental conditions (temperature and humidity) are directly correlated with rainfall activity during analysis.

### Data Packaging and Transmission

At each fixed interval, the microcontroller aggregates:

* Total rainfall accumulated during the interval
* Average temperature reading
* Average or latest humidity reading

These synchronized data packets are then transmitted together to the cloud database, ensuring consistency across all environmental parameters.

Perfect — that’s actually a **strong design choice**, not a limitation. Here’s a **clean, thesis-ready way** to explain it that won’t get you grilled.

---

## Intermittent Connectivity and Local Data Storage Strategy

Each sensing node is designed to operate under **intermittent or unavailable internet connectivity**, which is common in outdoor campus environments. To ensure continuous data collection, the system adopts a **local-first data acquisition approach**.

### Local Data Buffering

Sensor data (rainfall, temperature, and humidity) are first stored **locally on the microcontroller** using non-volatile memory (e.g., EEPROM, SPI flash, or microSD). This allows the node to continue recording environmental data even when Wi-Fi connectivity is unavailable.

Each data record includes:

* Timestamp
* Rainfall accumulation
* Temperature value
* Humidity value
* Node identifier

This guarantees that no critical data is lost during network outages.

### Store-and-Forward Transmission Model

Instead of streaming data continuously, the system uses a **store-and-forward model**. When internet connectivity becomes available, the node automatically initiates data synchronization by uploading all locally stored records to the cloud database in chronological order.

This approach:

* Reduces dependency on constant connectivity
* Prevents data gaps in long-term analysis
* Optimizes power and bandwidth usage

### Data Integrity and Duplication Control

To avoid duplicate uploads, each data record is assigned a **unique timestamp-based identifier**. Once a record is successfully transmitted and acknowledged by the cloud database, it is marked as synchronized or removed from local storage.

### Impact on Digital Twin Accuracy

By decoupling data collection from data transmission, the digital twin remains **temporally accurate** even when real-time visualization is temporarily unavailable. Once synchronization occurs, historical data is replayed on the dashboard, preserving the continuity of the flood monitoring model.

### One-liner you can drop in defense:

> “The system prioritizes reliable data acquisition over continuous connectivity by using local storage and delayed cloud synchronization.”

---

### Relevance to Flood Risk Analysis

Synchronizing temperature, humidity, and rainfall measurements enables meaningful analysis of:

* Rainfall patterns under varying atmospheric conditions
* Short-term weather changes preceding heavy rainfall events
* Environmental context within the digital twin visualization

This structured sampling strategy ensures both **temporal accuracy** and **data coherence**, which are essential for reliable campus-scale flood monitoring.

---

### Microcontroller Data Processing

The microcontroller functions as the data acquisition and processing unit. Its responsibilities include:

* Interrupt-based counting of rain gauge tip events
* Computation of total rainfall and rainfall intensity
* Periodic acquisition of temperature and humidity readings
* Timestamping all collected data

This ensures accurate rainfall measurement even during periods of high precipitation intensity.

---

### Data Transmission and Visualization

Collected sensor data are transmitted wirelessly to a cloud-based database. The data are then visualized through a **mini campus digital twin overlay**, where each sensing node represents a drainage-zone monitoring point. Real-time and historical rainfall trends are displayed to support flood risk analysis.

---

### Calibration and Validation

The 3D-printed rain gauge is calibrated by manually introducing a known volume of water and correlating the number of tip events to actual rainfall depth. Sensor readings are validated through repeated trials to ensure consistency and measurement accuracy.

---

### Modularity of the Prototype

Each sensing node is designed as a **self-contained module** consisting of:

* A 3D-printed rain gauge
* Hall effect sensor and magnet
* Temperature–humidity sensor
* Microcontroller and communication module

This modular architecture allows easy replication, replacement, or scaling of nodes across multiple drainage zones.

