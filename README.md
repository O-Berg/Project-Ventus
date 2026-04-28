<div align="center">
  <img width="1200" alt="CanSat-Exploded-Transparent" src="https://github.com/user-attachments/assets/bc88a406-60a3-4ad0-b26c-2e08c98cd1ba" />
</div>

# Project Ventus

**🥇 1st place — Swedish National CanSat Competition 2024**  
**🥈 2nd place — Swedish National CanSat Competition 2023**

CanSat is an ESA competition where student teams build a satellite the size of a soda can, launched to ~1 km by rocket to collect and transmit atmospheric data in real time. Project Ventus spans two years of participation in the Swedish national CanSat competition, culminating in a research paper proposing the **Successive Kalman Filter (SKF)**: a new approach to sensor fusion for wind profile estimation.

---

## How it works

The probe is released from ~1 km altitude and descends under a parachute. During descent, two sensor systems are fused:

| Sensor | Strength | Weakness |
|---|---|---|
| IMU (BNO085) — 100 Hz | High frequency, responsive | Velocity random walk accumulates over time |
| GPS + Barometer — 10 Hz | No drift | Low frequency, lower accuracy |

The **Successive Kalman Filter** uses pre-filtered IMU acceleration as a control input (`Bu` term) rather than a measurement, reducing the state vector from 9D to 6D and eliminating the constant-acceleration assumption between correction steps. GPS and barometer data correct horizontal and vertical position respectively in the update step.

Wind velocity is then derived from the estimated probe velocity and the rotated acceleration vector:

$$v_{\text{wind}} = \dot{x} + \sqrt{\frac{2ma}{C_d \rho A}}$$

Parachute-induced oscillations (~1.62 Hz) are identified via FFT and removed with a notch filter in post-processing.

---

## Hardware

<div align="center">
  <img width="900" alt="CanSat-Exploded-white" src="https://github.com/user-attachments/assets/7c55091b-61e7-4d71-ba33-2a3032942c41" />
</div>

The probe is split into two 3D-printed PETG units — a **payload unit** and a **telemetry unit** — totalling 325 g, within ESA's 350 g limit.

| Component | Role |
|---|---|
| Adafruit ESP32 Feather V2 | Main MCU (dual-core, 240 MHz) |
| CEVA BNO085 | IMU — orientation + linear acceleration |
| Bosch BMP390 | Barometer — altitude |
| GlobalTop FGPMMOPA6H | GPS — horizontal position & velocity |
| HopeRF RFM96W + Pro Trinket | LoRa telemetry to ground station |
| Amphenol MC65F232A | NTC thermistor — air temperature |
| SHT45 | Digital temperature & humidity |

---

## Software architecture

<div align="center">
  <img width="700" alt="CanSat-Architecture-white" src="https://github.com/user-attachments/assets/0521b890-5cc5-4ab9-81f1-d93df5062d57" />
</div>

**Core0 (200 Hz interrupt)** — IMU flip-flop: alternates between fetching quaternion and linear acceleration from BNO085 at 100 Hz each. Runs the SKF prediction step and computes wind velocity on every acceleration fetch.

**Core1 (10 Hz interrupt)** — Reads BMP390, SHT45, and thermistor via SPI/I²C. Polls GPS asynchronously. Runs the SKF correction step: barometer on the vertical axis at 10 Hz, GPS on horizontal axes whenever a new fix arrives.

Both cores write to separate SD card buffers (raw data at 100 Hz, mission data at 10 Hz) and package telemetry for the Pro Trinket → LoRa radio chain.

---

## Repository structure
```
Project-Ventus/
├── Ventus/                     # ESP32 firmware (C++)
│   └── ...                     # SKF, sensor drivers, dual-core architecture
├── Base-station/               # Ground station code
├── MissionDataPlot.py          # Mission data visualisation
├── MissionHDPlot.py            # High-density mission data plot
├── RawDataPlot.py              # Raw sensor data plot
├── Wind_Data_Plotter.py        # Wind profile + notch filter
├── QuatPosVis.py               # Quaternion & position visualisation
├── Quaternion_Plot.py          # Quaternion time series
├── Surface-Plot.py             # 3D surface wind plot
└── My_Psi.py                   # Turbulence index computation
```

---

## Results

<div align="center">
  <img width="600" alt="Wind-Pipeline-Vertical" src="https://github.com/user-attachments/assets/00a8a62e-8b68-4315-a32c-717beffbd3be" />
</div>

---

## Project members

**Figaro Stenlund · Olle Berg · William Bergklint**  
Hvitfeldtska gymnasiet, Gothenburg
2023/2024

📄 [Read the full paper](https://github.com/O-Berg/Project-Ventus/releases/tag/paper)
