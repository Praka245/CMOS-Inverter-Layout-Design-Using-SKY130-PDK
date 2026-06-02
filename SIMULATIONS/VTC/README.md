# ⚡ Voltage Transfer Characteristics (VTC) - CMOS Inverter

- This section documents the DC Voltage Transfer Characteristic (VTC) simulation of the CMOS Inverter designed using the **SKY130A PDK**, extracted from post-layout SPICE and simulated using **ngspice**.

---

## 1. 📘 Theoretical Background

### What is VTC?

- The Voltage Transfer Characteristic (VTC) is a DC plot of **V_out vs V_in** for a CMOS inverter. It describes how the inverter responds to a slowly sweeping input - essentially the static (steady-state) behaviour of the gate.

- An ideal inverter would have a perfectly vertical transition at VDD/2. Real CMOS inverters approximate this with a sharp sigmoid-shaped curve, which is one of the most important quality indicators of a digital gate.

---

### 🔑 Key Voltage Parameters

| Parameter | Symbol | Description |
|---|---|---|
| Output High Voltage | V_OH | Maximum output voltage when output = logic 1 → ideally VDD |
| Output Low Voltage | V_OL | Minimum output voltage when output = logic 0 → ideally 0 V |
| Input High Voltage | V_IH | Minimum input voltage recognised as logic 1 (slope = −1 point on upper transition) |
| Input Low Voltage | V_IL | Maximum input voltage recognised as logic 0 (slope = −1 point on lower transition) |
| Switching Threshold | V_M | Input voltage where V_out = V_in (midpoint of transition) |
| Noise Margin High | NM_H | V_OH - V_IH - tolerance to noise on a HIGH input |
| Noise Margin Low | NM_L | V_IL - V_OL - tolerance to noise on a LOW input |

---

### ⚙️ Switching Threshold (V_M)

- V_M is the point where the VTC curve crosses the **V_out = V_in** line (the 45° diagonal). At this point both PMOS and NMOS are in saturation, and the inverter gain is at its maximum magnitude.

- For a symmetric inverter (equal drive strengths):

   ```
    V_M ≈ VDD / 2
   ```

For an asymmetric inverter:
- Stronger PMOS → V_M shifts above VDD/2
- Stronger NMOS → V_M shifts below VDD/2

The measured V_M from simulation is **≈ 883 mV** (with VDD = 1.8 V),
Since 883 mV is slightly below 900 mV (0.9 V), the inverter exhibits a small shift toward lower input voltages, indicating that the NMOS pull-down network is marginally stronger than the PMOS pull-up network for the chosen transistor sizing.

---

## 2. 📊 VTC Curve & Operating Regions

### 📈 Theoretical VTC Shape

The characteristic S-shaped curve is divided into five distinct operating regions based on the state of the NMOS and PMOS transistors:

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/30de7c80-81cc-4038-8476-e37aeae12790" />

<br>
<br>

> VTC graph with all five regions and operating conditions labelled.

---

### 🔌 Operating Regions of MOS Transistors


| Region | V_in Range | NMOS State | PMOS State | V_out Behaviour |
|---|---|---|---|---|
| A | 0 → V_tn | Cut-off | Linear | V_out = VDD (flat) |
| B | V_tn → V_M | Saturation | Linear | V_out begins to drop |
| C | ≈ V_M | Saturation | Saturation | Steepest transition, gain peak |
| D | V_M → VDD+V_tp | Linear | Saturation | V_out continues to drop |
| E | VDD+V_tp → VDD | Linear | Cut-off | V_out = 0 V (flat) |

**Condition boundaries:**
- NMOS enters saturation when: `V_DSn > V_GSn − V_tn` → `V_out > V_in − V_tn`
- PMOS enters saturation when: `V_DSp < V_GSp − V_tp` → `V_out < V_in − V_tp`
- Both in saturation simultaneously defines Region C and the V_M point

---

## 3. 🧪 Simulation Setup

### Testbench: `tb_Inverter.spice`

The VTC was obtained by performing a **DC sweep** of V_in from 0 V to VDD with a fine step, while measuring V_out.

```spice
* CMOS Inverter VTC Analysis
.include "Inverter.spice"
.include "/path/to/sky130A/libs.tech/ngspice/sky130.lib.spice tt"

* Power supply
VDD VDD 0 1.8
VSS VSS 0 0

* Input stimulus (pulse)
VIN Vin 0 DC 0

XINV Vout Vin VSS VDD Inverter

* DC analysis
.dc VIN 0 1.8 0.001

.control
run
plot v(Vin) v(Vout)
.endc
.end
````

| Parameter      | Value                |
| -------------- | -------------------- |
| Temperature    | 27°C                 |
| Simulator      | ngspice              |
| Process Corner | TT (Typical-Typical) |
| SPICE Model    | SKY130A              |

---

## 4. 📊 Simulation Results

### VTC Plot

<img width="1000" height="500" alt="VTC_2" src="https://github.com/user-attachments/assets/fe8347d0-827d-40d1-a413-00abdd76f470" />

<br>
<br>

> See: `VTC_2.png` - Full VTC : V_out (red) vs V_in (blue diagonal), sweep from 0 to 1.8 V.

The plot clearly shows:

* V_out is flat at **1.8 V** for V_in from 0 to ~0.6 V (NMOS off, PMOS fully on)
* A **sharp transition** occurs between ~0.7 V and ~1.0 V
* V_out drops to **~0 V** for V_in above ~1.0 V (PMOS off, NMOS fully on)
* The V_out = V_in diagonal line (45°) intersects the VTC curve at the switching threshold V_M.

<p align="center">
<img width="550" height="400" alt="Screenshot 2026-06-02 123627" src="https://github.com/user-attachments/assets/a881ce42-d1ea-478e-84c1-f03fd90eb217" />
</p>


- The switching threshold V_M is determined from the intersection of Vin and Vout curves plotted against Vout, showing Vin = Vout at approximately 883 mV.

   ```
   V_M ≈ 883 mV   (measured by ngspice .meas)
  ```

---

### 🧾 V_M Measurement - ngspice Output

<p align="center">
  <img width="684" height="482" alt="VTC Plot"
       src="https://github.com/user-attachments/assets/0a243531-6944-493a-86df-0c5a80e88988">
</p>


> See: `Vm_Point.png` - ngspice terminal showing the `.meas` result for V_M.

   ```
    .meas dc Vm find Vin when vout=vin
   ```

   **V_M = 883.05 mV**

---

## 5. 📈 Result Analysis & Calculations

### Extracted Parameters

| Parameter | Measured Value | Ideal Value |
| --------- | -------------- | ----------- |
| V_OH      | 1.8 V          | VDD         |
| V_OL      | ~0 V           | 0 V         |
| V_M       | **883 mV**     | VDD/2       |
| VDD       | 1.8 V          | 1.8 V       |


### ⚖️ V_M Analysis

   ```
     Ideal V_M = 900 mV
     Simulated V_M = 883 mV
     Deviation = 17 mV (~1.9%)
   ```

---



## 6. 🔗 Correlation Between Theory and Simulation

The simulated VTC closely follows the expected theoretical behaviour of a CMOS inverter.

| Theoretical Prediction | Simulation Observation |
|------------------------|------------------------|
| V_out ≈ VDD when V_in < V_tn | ✅ Output remains near 1.8 V for low input voltages |
| Sharp transition when both transistors conduct | ✅ Steep transition observed between approximately 0.7 V and 1.0 V |
| Switching threshold near VDD/2 | ✅ V_M = 883 mV, close to the ideal 900 mV |
| V_out ≈ 0 V for high input voltages | ✅ Output approaches 0 V when V_in approaches VDD |
| Rail-to-rail output swing | ✅ Full output swing from 0 V to 1.8 V achieved |

---


## 7. 🎯 Conclusion

- The post-layout extracted CMOS inverter exhibits the expected Voltage Transfer Characteristic (VTC) behaviour.
- The measured switching threshold of 883 mV is within 1.9% of the ideal value of 900 mV, indicating good balance between the pull-up and pull-down networks.
- Overall, the simulation results validate the functionality of the CMOS inverter implemented using the SKY130A process.

---

## 8. 🧾 Summary

- A post-layout Voltage Transfer Characteristic (VTC) analysis of the CMOS inverter was performed using the SKY130A PDK and ngspice.
- The inverter exhibited the expected CMOS switching behaviour, with the output transitioning from logic HIGH to logic LOW as the input voltage increased.
- The measured switching threshold was **V_M = 883 mV**, which is within **1.9%** of the ideal value of **900 mV (VDD/2)**, indicating good balance between the NMOS and PMOS devices.
