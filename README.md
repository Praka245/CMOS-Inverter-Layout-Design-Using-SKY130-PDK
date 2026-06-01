# ⚙️ CMOS-Inverter-Layout-Design-Using-SKY130-PDK

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Tool](https://img.shields.io/badge/Tool-Magic%20%7C%20Ngspice-orange)](https://github.com/RTimothyEdwards/magic)
[![PDK](https://img.shields.io/badge/PDK-SKY130-green)](https://skywater-pdk.readthedocs.io/)

## 📌 Project Overview

This project presents the complete design, physical layout, and post-layout simulation of a **CMOS Inverter** using the open-source **SKY130** Process Design Kit (PDK). The primary goal is to demonstrate a hands-on design flow from layout and performance verification.

The entire flow is executed using open-source EDA tools:
- **Layout Design**: `Magic VLSI` layout tool.
- **Simulation**: `Ngspice` for all electrical analyses.

This repository serves as a foundational template for understanding CMOS circuit design on a 130nm node, emphasizing practical simulation and layout constraints.

---

## 🧱 SKY130 Process Stack & Layers

This project is implemented using the **SKY130 Open-Source Process Design Kit (PDK)**, a 130nm CMOS technology developed by SkyWater Technology. The PDK provides the design rules, device models, layer definitions, and process information required for custom integrated circuit design and simulation.

The CMOS inverter layout was designed in **Magic VLSI** following the SKY130 design rules and layer specifications. The implementation primarily utilizes the fundamental fabrication layers required for CMOS device formation and interconnection.

For a detailed understanding of the SKY130 process stack, layer definitions, and fabrication assumptions, refer to the official documentation:

> 📖 [**SKY130 Process Stack Diagram & Assumptions**](https://skywater-pdk.readthedocs.io/en/main/rules/assumptions.html#process-stack-diagram)

### 🎞️ Key Layers Used in This Design

* **N-Well** – Formation of the PMOS transistor
* **P-Substrate / Active Region** – Formation of the NMOS transistor
* **Polysilicon (Poly)** – Gate electrode formation
* **N+ and P+ Diffusion Regions** – Source and drain terminals
* **Local Interconnect (LI)** – Short-range device interconnections
* **Metal 1, 2 (M1, M2)** – Signal and power routing
* **Contacts and Vias** – Electrical connections between layers

These layers collectively form the physical structure of the CMOS inverter and provide the necessary routing resources for implementing the circuit layout.

---
## 📂 Repository Structure

```text
CMOS-Inverter-Layout-Design-Using-SKY130-PDK
│
├── LAYOUT/
│   └── CMOS Inverter Layout Images
│
├── SKY130/
│   └── SKY130 README
|   └── Stack Layer Image
│
├── SPICE FILES/
│   ├── Inverter.spice
│   ├── Inverter.ext
│   ├── tb_Inverter.spice
│   ├── tb_delay.spice
│   ├── tb_noise_inverter.spice
│   └── tb_power.spice
│
├── SIMULATIONS/
│   ├── VTC/
│   ├── NOISE/
│   ├── TIMING/
│   └── POWER/
│
└── README.md
```
---
## 🛠️ Project Structure & Analysis

The repository contains the CMOS inverter layout, extracted SPICE netlists, simulation testbenches, and post-layout analysis results.

| Directory | Description |
| :--- | :--- |
| **`LAYOUT/`** | CMOS inverter layout files created using Magic VLSI. |
| **`SPICE FILES/`** | Extracted netlists and simulation testbenches. |
| **`VTC/`** | Voltage Transfer Characteristic (VTC) analysis. |
| **`TIMING/`** | Timing and propagation delay analysis. |
| **`NOISE/`** | Noise margin and noise immunity analysis. |
| **`POWER/`** | Power consumption analysis. |

<br>

> 📌 **Note on LVS (Layout vs. Schematic):**  
> Due to the simplicity of the single-inverter design and manual verification of connections, **formal LVS was omitted**. The layout connectivity was visually verified in Magic. For larger circuits, LVS is mandatory.

---

## 🔬 Analyses Performed

To characterize the electrical behavior of the CMOS inverter, several post-layout simulations were performed using the extracted SPICE netlist in NGSpice.

### 1. Voltage Transfer Characteristic (VTC) Analysis 📈

The VTC analysis was carried out to study the relationship between the input and output voltages of the inverter. This analysis helps evaluate the switching behavior, voltage gain, and logic-level characteristics of the CMOS inverter.

### 2. Timing Analysis ⏱️

Transient simulations were performed to analyze the dynamic response of the inverter. This includes the investigation of signal transition behavior and propagation characteristics during switching events.

### 3. Noise Margin Analysis 🔊

Noise margin analysis was conducted to evaluate the inverter's tolerance to unwanted voltage disturbances. This analysis provides insight into the reliability and robustness of the circuit under noisy operating conditions.

### 4. Power Analysis ⚡

Power characterization was performed to study the energy consumption of the inverter during operation. The analysis focuses on understanding the power behavior associated with switching activity and overall circuit operation.

---

> **📌Note:**

> 📂 Detailed simulation waveforms, calculations, observations, and results for each analysis are available in their respective directories within this repository.

> 📂 The simulation results presented in this repository are based on the extracted SPICE netlist generated from the Inverter layout designed in Magic VLSI using the SKY130 PDK. The results represent the behavior of this specific implementation and may vary with changes in device sizing, layout parasitics, process corners, operating conditions, and simulation parameters.

---
### 🙋‍♂️ CONTRIBUTOR

**Prakadeesh N**  
Electronics and Communication Engineering (ECE)  
Interested in Digital Design, VLSI, PCB
