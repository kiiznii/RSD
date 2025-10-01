# ADAS Controller (ACC + LK)

## 🚗 Overview
This project implements a **basic ADAS control stack** — Adaptive Cruise Control (ACC) and Lane Keeping (LK) — integrated with the **Eclipse SDV ecosystem**.  
The goal is to create a working pipeline from **simulation (CARLA)** → **data broker (Kuksa with VSS)** → **communication (Zenoh)** → **control logic (ACC + LK)**.






## ✨ Features
- **ACC (Adaptive Cruise Control)**
  - Reads radar sensor data (distance, relative speed, TTC) from CARLA.
  - Writes sensor signals into **Kuksa Databroker** using VSS schema.
  - Applies control logic for **Cruise / Follow / Emergency Brake** transitions.
  - Filters noisy sensor values for smoother braking and acceleration.

- **LK (Lane Keeping)**
  - Uses CARLA’s camera-based lane detection (baseline pipeline).
  - Provides steering control signals via Kuksa → Control bridge.
  - Keeps ego vehicle centered between detected lane markers.

- **Eclipse Integration**
  - **Kuksa Databroker** → standardizes vehicle signals (`Vehicle.ADAS.ACC.*`, `Vehicle.ADAS.LK.*`).
  - **Zenoh** → publishes/subscribes signals across services.
  - **Control Layer** → consumes signals and applies throttle, brake, and steering to CARLA ego vehicle.

---

## 🏗 Architecture
