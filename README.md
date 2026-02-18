# Flight Search Application

This is a Single Page Responsive Application built using Vue.js 3 and Vuetify 3.

The app allows users to search available flights based on Source and Destination using the provided JSON data.

---

## Features

- Select Source from dropdown (loaded from JSON)
- Select Destination based on selected Source
- Prevent same Source and Destination selection
- Display all matching airlines
- Show:
  - Airline name
  - Route (Origin → Destination)
  - Total duration
  - Points
  - Stops
  - Segment details (Departure & Arrival time)
- Responsive design (Mobile & Desktop)

---

## Logic Used

- Source = `segment[0].origin`
- Destination = `segment[last].destination`
- Duplicate values removed using array filtering
- Flights filtered using `.filter()` method

---
