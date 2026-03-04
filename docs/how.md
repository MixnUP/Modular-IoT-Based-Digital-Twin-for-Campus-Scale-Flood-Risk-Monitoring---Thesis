
## **1️⃣ Get a Campus Base Map**

Since this is a **student project / small area**, you don’t need full GIS:

* Option 1: **Static Image**

  * Take a screenshot from Google Maps / your campus map.
  * Crop to the area around your drainage zone.
  * Keep resolution moderate (800–1200px width).

* Option 2: **Open-Source Map Tiles**

  * Use **Leaflet.js** + OpenStreetMap.
  * Lets you overlay shapes, colors, and zoom dynamically.

> For 1 zone, **static image + canvas overlay** is easiest and fully defensible in thesis.

---

## **2️⃣ Define the Zone**

* Draw a polygon or rectangle over the area prone to flooding.
* Assign **coordinates** relative to the map image or Leaflet map.
* Give it a **unique ID**: e.g., `zoneA`.

**Example:**

```json
{
  "zoneId": "zoneA",
  "coordinates": [[x1,y1], [x2,y1], [x2,y2], [x1,y2]]
}
```

* If using a static image, coordinates can be **pixels**.
* If using Leaflet, coordinates are **lat/lng**.

---

## **3️⃣ Assign Flood Risk Color**

* Each zone has a **risk score (0–100)** from your digital twin.
* Map this score to **color intensity**:

| Risk Score | Color            |
| ---------- | ---------------- |
| 0–30       | Green            |
| 31–60      | Yellow           |
| 61–100     | Blue / Dark Blue |

* Color is updated dynamically whenever `/riskSnapshots/zoneId` updates.

---

## **4️⃣ Render Overlay on Map**

### **Static Image Approach**

1. Load image as **background**.
2. Use **HTML canvas / SVG**.
3. Draw your zone polygon.
4. Fill color based on risk score.
5. Optional: animate **opacity** or **color gradient** as risk changes.

**Pseudo-code (JS + canvas)**:

```javascript
ctx.fillStyle = getColor(riskScore);
ctx.beginPath();
ctx.moveTo(x1,y1);
ctx.lineTo(x2,y1);
ctx.lineTo(x2,y2);
ctx.lineTo(x1,y2);
ctx.closePath();
ctx.fill();
```

---

### **Leaflet / OpenStreetMap Approach**

1. Initialize map centered on your campus.
2. Draw a **polygon** for the zone:

```javascript
L.polygon([
  [lat1, lng1],
  [lat2, lng2],
  [lat3, lng3],
  [lat4, lng4]
], {color: getColor(riskScore)}).addTo(map);
```

3. Update polygon color in **real-time** whenever new risk snapshot comes in.

---

## **5️⃣ Optional Enhancements**

* **Water animation:** subtle ripple effect in the colored zone.
* **Tooltip:** Hover shows sensor readings & timestamp.
* **Mini legend:** Shows what colors mean (Low / Medium / High).

---

## **6️⃣ How It Looks in Defense**

* Show **1 zone colored**.
* Color updates as rain falls / soil saturates / water rises.
* Examiner instantly sees:

  > “Ah, zone turns blue when risk is high → digital twin predicts flooding correctly.”

---

💡 **Summary Logic**:

1. Base map → image or Leaflet map
2. Zone polygon → defines the monitored area
3. Risk snapshot → generates color
4. Render → overlay color on zone, live update

