# ⚡ Voltage Transfer Characteristics (VTC) - CMOS Inverter

- This section documents the DC Voltage Transfer Characteristic (VTC) simulation of the CMOS Inverter designed using the **SKY130A PDK**, extracted from post-layout SPICE and simulated using **ngspice**.

---

## 1. 📘 Theoretical Background

### What is VTC?

- The Voltage Transfer Characteristic (VTC) is a DC plot of **V_out vs V_in** for a CMOS inverter. It describes how the inverter responds to a slowly sweeping input - essentially the static (steady-state) behaviour of the gate.

A ideal inverter would have a perfectly vertical transition at VDD/2. Real CMOS inverters approximate this with a sharp sigmoid-shaped curve, which is one of the most important quality indicators of a digital gate.

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

- V_M is the point where the VTC curve crosses the **V_out = V_in** line (the 45° diagonal). At this point both NMOS and NMOS are in saturation, and the inverter gain is at its maximum magnitude.

- For a symmetric inverter (equal drive strengths):

   ```
    V_M ≈ VDD / 2
   ```

For an asymmetric inverter:
- Stronger PMOS → V_M shifts above VDD/2
- Stronger NMOS → V_M shifts below VDD/2

The measured V_M from simulation is **≈ 883 mV** (with VDD = 1.8 V),
Since 883 mV is slightly below 900 mV or 0.9v, the inverter exhibits a small shift toward lower input voltages, indicating that the NMOS pull-down network is marginally stronger than the PMOS pull-up network for the chosen transistor sizing.

---

### 📉 Noise Margins

```
NM_H = V_OH - V_IH
NM_L = V_IL - V_OL
```

Large noise margins mean the gate can tolerate more noise on its inputs without producing an incorrect output. A well-designed CMOS inverter aims for NM_H ≈ NM_L ≈ VDD/2.

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

#### NMOS Terminal Voltages

```
V_GSn = V_in
V_DSn = V_out
```

#### PMOS Terminal Voltages

```
V_GSp = V_in - VDD
V_DSp = V_out - VDD
````

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

VDD VDD 0 DC 1.8
Vin in 0 DC 0

Xinv in out VDD 0 Inverter

.dc Vin 0 1.8 0.001

.meas dc Vm find Vin when vout=vin

.control
run
plot v(vout) v(vin) vs v(vin)
.endc
````

| Parameter            | Value                        |
| -------------------- | ---------------------------- |
| Supply Voltage (VDD) | 1.8 V                        |
| Input Sweep          | 0 V → 1.8 V, step 1 mV       |
| Temperature          | 27°C (TT corner)             |
| Simulator            | ngspice 44+                  |
| SPICE Model          | SKY130A TT (typical-typical) |

---

## 4. 📊 Simulation Results

### VTC Plot — Full Range

> See: `VTC_2.png` — Full VTC: V_out (red) vs V_in (blue diagonal), sweep from 0 to 1.8 V.

The plot clearly shows:

* V_out is flat at **1.8 V** for V_in from 0 to ~0.6 V (NMOS off, PMOS fully on)
* A **sharp transition** occurs between ~0.7 V and ~1.0 V
* V_out drops to **~0 V** for V_in above ~1.0 V (PMOS off, NMOS fully on)

The V_out = V_in diagonal line (45°) intersects the VTC curve at the switching threshold V_M.

---

### 🔍 VTC Plot — Zoomed Transition Region (V_M extraction)

> See: `VTC_1.png` — Zoomed view of the transition region showing the V_M crossing point.

The inset plot zooms into the 840 mV – 960 mV region where the transition occurs. At the intersection of V_out = V_in:

```
V_M ≈ 883 mV   (measured by ngspice .meas)
```

---

### 🧾 V_M Measurement — ngspice Output

> See: `Vm_Point.png` — ngspice terminal showing the `.meas` result for V_M.

```
ngspice 56 -> meas dc Vm find Vin when vout=vin
dc    =    8.830505e-01
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

---

### ⚖️ V_M Analysis

```
Ideal V_M = 900 mV
Simulated V_M = 883 mV
Deviation = 17 mV (~1.9%)
```

---

### 📉 Gain in Transition Region

```
|dV_out/dV_in| > 1
```

---

### 📡 Noise Margin Estimation

```
V_IL ≈ 0.75 V
V_IH ≈ 0.95 V

NM_L ≈ 750 mV
NM_H ≈ 850 mV
```

---

## 6. 🔗 Connecting Theory to Simulation

| Theory                   | Observation |
| ------------------------ | ----------- |
| V_out = VDD at low input | ✅           |
| Sharp transition region  | ✅           |
| V_M ≈ VDD/2              | ✅           |
| V_out = 0 at high input  | ✅           |
| Gain > 1                 | ✅           |

---

## 7. 🎯 Conclusion

* Post-layout CMOS inverter shows correct VTC behaviour
* V_M = 883 mV is close to ideal 900 mV
* Full rail-to-rail swing achieved
* Good noise margins ensure robust logic operation

---

## 🖼️ Images Reference

| File           | Description              |
| -------------- | ------------------------ |
| `VTC_1.png`    | Zoomed transition region |
| `VTC_2.png`    | Full VTC plot            |
| `Vm_Point.png` | ngspice V_M measurement  |

---

## 8. 🧾 Summary

| Item | Value   |
| ---- | ------- |
| VDD  | 1.8 V   |
| V_M  | 883 mV  |
| V_OH | 1.8 V   |
| V_OL | ~0 V    |
| NM_H | ~850 mV |
| NM_L | ~750 mV |
