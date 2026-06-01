# ⚙️ CMOS-Inverter-Layout-Design-Using-SKY130-PDK

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Tool](https://img.shields.io/badge/Tool-Magic%20%7C%20Ngspice-orange)](https://github.com/RTimothyEdwards/magic)
[![PDK](https://img.shields.io/badge/PDK-SKY130-green)](https://skywater-pdk.readthedocs.io/)

## 📌 Project Overview

This project presents the complete design, physical layout, and post-layout simulation of a **CMOS Inverter** using the open-source **SKY130** Process Design Kit (PDK). The primary goal is to demonstrate a hands-on, end-to-end analog/mixed-signal design flow, from schematic capture to layout and performance verification.

The entire flow is executed using open-source EDA tools:
- **Layout Design**: `Magic VLSI` layout tool.
- **Simulation**: `Ngspice` for all electrical analyses.

This repository serves as a foundational template for understanding CMOS circuit design on a 130nm node, emphasizing practical simulation and layout constraints.

---

## 🧱 SKY130 Process Stack & Layers

The design adheres to the **SKY130** process rules. This is a mature 130nm CMOS technology with 6 metal layers (M1-M5 + M6), featuring:

- **Low-k Dielectric** for reduced parasitic capacitance.
- **Multiple threshold voltage options** (LVP, HVT, etc.).
- **Deep N-Well** for isolated wells and noise-sensitive circuits.

For a detailed understanding of the layer stack, critical dimensions, and design rules, refer to the official documentation:

> 📖 **[SKY130 Process Stack Diagram & Assumptions](https://skywater-pdk.readthedocs.io/en/main/rules/assumptions.html#process-stack-diagram)**

Key layers used in this design include:
- **Active, N-Well, P-Well**
- **Poly Silicon (PC)**
- **N+ / P+ Implants (NSD, PSD)**
- **Local Interconnect (LI)**
- **Metal 1, Via 1, Metal 2**

---

## 🛠️ Project Structure & Analysis

The repository is organized into directories, each containing detailed simulation files and results. Below is a high-level summary of the analyses performed.

| Directory / Tag | Analysis Performed | Description |
| :--- | :--- | :--- |
| **`VTC/`** | **Voltage Transfer Characteristics (VTC)** | DC sweep to determine inverter switching threshold, gain, and logic levels (VIL, VIH, VOL, VOH). |
| **`TIMING/`** | **Propagation Delay** | Transient analysis measuring rising (t_pLH) and falling (t_pHL) propagation delays under capacitive load. |
| **`NOISE/`** | **Noise Margin & Immunity** | Quantifies the robustness of the inverter by measuring the maximum tolerable noise voltage at the input. |
| **`POWER/`** | **Power Consumption** | Evaluates static (leakage) and dynamic (switching) power dissipation at different frequencies. |
| **`SIMULATIONS/`** | **Transient & DC Analysis** | General time-domain and DC behavioral simulations of the extracted layout netlist. |
| **`LAYOUT/`** | **Physical Layout (Magic)** | GDS and Magic `.mag` files of the completed inverter layout, DRC-clean. |
| **`SPICE FILES/`** | **Netlists & Testbenches** | All extracted netlists (`Inverter.ext`), simulation models, and testbenches (`tb_*.spice`) for Ngspice. |

> ✅ **Note on LVS (Layout vs. Schematic):**  
> Due to the simplicity of the single-inverter design and manual verification of connections, **formal LVS was omitted**. The layout connectivity was visually verified in Magic. For larger circuits, LVS is mandatory.

---

## 🔬 Detailed Analysis Results (Summary)

### 1. Voltage Transfer Characteristics (VTC) 📈
- **Switching Threshold (V_M)**: ~0.65V (for VDD=1.8V).
- **High Gain Region**: Sharp transition zone, typical of a well-designed CMOS inverter.
- **Logic Levels**: Output swing fully rail-to-rail (0V to 1.8V).

### 2. Timing Analysis ⏱️
- **Rise Delay (t_pLH)**: 250 ps (with 50fF load).
- **Fall Delay (t_pHL)**: 210 ps (with 50fF load).
- The fall delay is slightly lower due to higher electron mobility in NMOS.

### 3. Noise Margin Analysis 🎧
- **High Noise Margin (NM_H)**: 0.72V.
- **Low Noise Margin (NM_L)**: 0.68V.
- The inverter shows good noise immunity, with margins > 35% of VDD.

### 4. Power Consumption ⚡
- **Static Power**: ~10 pA (negligible, dominated by sub-threshold leakage).
- **Dynamic Power at 100 MHz**: ~55 µW (dominant during switching).

---

## 🚀 Getting Started

### Prerequisites
- **Magic VLSI** (version 8.3 or later)
- **Ngspice** (version 40 or later)
- **SKY130 PDK** (installed and configured)

### Running Simulations

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/CMOS-Inverter-Layout-Design-Using-SKY130-PDK.git
   cd CMOS-Inverter-Layout-Design-Using-SKY130-PDK
