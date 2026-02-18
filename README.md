# ✈ Flight Search Application

This is a Single Page Responsive Application built using **Vue.js 3** and **Vuetify 3**.

The application allows users to search available flights based on **Source** and **Destination** using the provided JSON dataset.

---

## 🚀 Tech Stack

- Vue.js 3 (Options API)
- Vuetify 3
- JavaScript (ES6)
- JSON Data

---

## 📌 Features

- Select **Source** from dropdown (derived from JSON data)
- Select **Destination** based on selected Source
- Prevent selecting same Source and Destination
- Display matching flights
- Show:
  - Airline Name
  - Route (Origin → Destination)
  - Total Duration
  - Points
  - Number of Stops
  - Segment Details (Departure & Arrival Time)
- Responsive layout (Mobile & Desktop view)

---

## 🧠 Logic & Implementation Details

### Source & Destination Extraction

- **Source** is derived from:
  `segment[0].origin`

- **Destination** is derived from:
  `segment[segment.length - 1].destination`

- Only the **first and last segment** are considered for filtering.

---

### Dropdown Data Handling

- Source and Destination values are manually filtered from the JSON dataset.
- Since values are extracted manually, repetitive cities may appear if present in multiple flights.
- Duplicate values are removed using array filtering logic.
- Intermediate segment cities are **not used for dropdown selection**.

---

### Flight Filtering Logic

Flights are filtered using the JavaScript `.filter()` method.

Matching condition:

- First segment origin must match selected Source.
- Last segment destination must match selected Destination.

---

### Segment Handling

- Segment data is used to:

  - Display route details
  - Show departure and arrival times
  - Calculate number of stops

- Only the **first and last segment** are used for filtering logic.
- Intermediate segments are displayed only for route information.

---

## 📱 Responsive Design

The layout adapts for:

- Mobile devices
- Tablets
- Desktop screens

---

## 📂 Sample JSON Structure

```json
{
  "id": "fc704c16fd79",
  "company": "US Airlines",
  "points": 25000,
  "duration": 590,
  "segment": [
    {
      "duration": 590,
      "departureTime": "2023-10-10T21:30-03:00",
      "arrivalTime": "2023-10-11T06:20-04:00",
      "origin": "Sao Paulo",
      "destination": "New York"
    }
  ]
}
```
