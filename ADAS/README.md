# ADAS Controller (ACC + LK)

## 🚗 Overview
This project implements a **basic ADAS control stack** — Adaptive Cruise Control (ACC) and Lane Keeping (LK) — integrated with the **Eclipse SDV ecosystem**.  
The goal is to create a working pipeline from **simulation (CARLA)** → **data broker (Kuksa with VSS)** → **communication (Zenoh)** → **control logic (ACC + LK)**.

```mermaid
flowchart LR
  %% === Stage 1: ENV ===
  subgraph ENV["ENV (CARLA Simulation)"]
    R["Radar"]
    C["Camera"]
  end

  %% === Stage 2: DECISION ===
  subgraph DECISION["Decision Layer"]
    D["ACC + LK Decision"]
  end

  %% === Stage 3: CONTROL ===
  subgraph CONTROL["Control Layer"]
    CT["Control Outputs"]
  end

  %% Radar -> Kuksa -> Decision
  R -->|"Sensor data"| KUKSA["Kuksa Databroker"]
  KUKSA -->|"pub/sub"| D

  %% Camera -> Zenoh -> Decision
  C -->|"Image/Lane data"| ZENOH["Zenoh Pub/Sub"]
  ZENOH --> D

  %% External algo params
  ACC["ACC_algo.py"] -->|"params"| D
  LK["LK_algo.py"]  -->|"params"| D

  %% Decision -> Control -> CARLA
  D --> CT
  CT -->|"Throttle / Brake / Steering"| CARLA["CARLA Ego Vehicle"]




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
