# 🔉 Noise Margin Analysis of CMOS Inverter

This section presents the noise margin analysis of the CMOS inverter designed using the **SKY130A PDK** and simulated using **ngspice**. Noise margins quantify the ability of the inverter to tolerate unwanted voltage disturbances while maintaining correct logic operation.

---

# 1. 📖 Theoretical Background

## What is Noise Margin?

In practical digital circuits, signals are affected by various noise sources such as:

* Power supply fluctuations
* Crosstalk between interconnects
* Ground bounce
* Thermal noise
* Switching noise from neighboring circuits

A robust digital gate must continue to interpret logic levels correctly even when noise is present.

The maximum noise voltage that can be superimposed on a logic signal without causing a logic error is called the **Noise Margin**.

---

## 🔑 Important Voltage Parameters

The Voltage Transfer Characteristic (VTC) of a CMOS inverter defines four important voltage levels.


| Parameter | Description                                   |
| --------- | --------------------------------------------- |
| V_OH      | Output High Voltage                           |
| V_OL      | Output Low Voltage                            |
| V_IH      | Minimum Input Voltage recognized as Logic '1' |
| V_IL      | Maximum Input Voltage recognized as Logic '0' |

These parameters determine the noise immunity of the inverter.

---

## Determination of V_IL and V_IH

The values of V_IL and V_IH are obtained from the points where the magnitude of the inverter gain becomes unity:
<p align = "center">
<img width="750" height="400" alt="image" src="https://github.com/user-attachments/assets/d59c1189-20d6-4133-83c1-def605119af6" />
</p>

```
|dVout/dVin| = 1
```

* The first unity-gain crossing corresponds to **V_IL**
* The second unity-gain crossing corresponds to **V_IH**

These points define the boundaries between the valid logic regions and the transition region.

---

## 📐 Noise Margin Equations

Low Noise Margin:

```text
NM_L = V_IL - V_OL
```

High Noise Margin:

```text
NM_H = V_OH - V_IH
```

---

# 2. 🧪 Simulation Setup

### Testbench: `tb_noise_inverter.spice`

```spice
* CMOS Inverter Noise Analysis

.lib /home/praka/whyRD_eda_bundle/open_pdks/sky130/sky130A/libs.tech/ngspice/sky130.lib.spice tt

.include Inverter.spice

* Power Supplies
VDD VDD 0 1.8
VSS VSS 0 0

* DC Input Source
VIN Vin 0 DC 0

* Inverter Instance
XINV Vout Vin VSS VDD Inverter

* DC Sweep
.dc VIN 0 1.8 1m

.control
run

* VTC Curve
plot v(Vout) vs v(Vin) v(Vin) vs v(Vin)

.endc
.end

```

| Parameter      | Value                                   |
| -------------- | --------------------------------------- |
| Technology     | SKY130A PDK                             |
| Simulator      | ngspice                                 |
| Supply Voltage | 1.8 V                                   |
| Analysis Type  | DC Sweep                                |
| Input Sweep    | 0 V → 1.8 V                             |
| Step Size      | 1 mV                                    |
| Purpose        | Extraction of V_IL, V_IH, NM_L and NM_H |

---

# 3. 🖼️ Simulation Results

## 📌 VTC with Gain.png

<p align = "center">
<img width="1200" height="600" alt="VTC with Gain" src="https://github.com/user-attachments/assets/6ad6e51f-bc7e-41a9-98a7-1ffef6f4421c" />
</p>

### Description

This figure shows:

* Voltage Transfer Characteristic (Vout vs Vin)
* Gain profile of the inverter

### Observations

* Full rail-to-rail switching is achieved
* The output transitions sharply between logic levels
* The gain reaches its maximum near the switching threshold
* The transition region occupies a relatively small portion of the input range

### Significance

A steep transition region improves:

* Noise immunity
* Switching performance
* Logic-level restoration

---

## 📌 Slope (a).png

<p align = "center">
<img width="1200" height="600" alt="Slope (a)" src="https://github.com/user-attachments/assets/084d04d0-bb80-41e8-a8d8-a64e2aaea52d" />
</p>

### Description

This figure shows the magnitude of inverter gain across the complete input voltage range.

### Observations

* Gain remains small in stable logic regions
* Gain increases significantly in the transition region
* Two unity-gain crossings are visible

### Significance

These unity-gain crossings are used to determine:

* V_IL
* V_IH

which are required for noise-margin extraction.

---

## 📌 Slope (b).png

<p align = "center">
<img width="1200" height="600" alt="Slope (b)" src="https://github.com/user-attachments/assets/b75d861a-28b8-48b4-813d-ca08acebf9b6" />
</p>

### Description

This figure provides a zoomed view around the transition region.

### Observations

* The exact gain = 1 crossing points are visible
* V_IL and V_IH can be extracted with higher accuracy
* The steep gain profile confirms strong switching behavior

### Significance

The zoomed gain plot minimizes measurement uncertainty during noise-margin extraction.

---

## 📌 Noise Margin.png

### Description

<p align= "center">
<img width="810" height="700" alt="ngspice result" src="https://github.com/user-attachments/assets/cf6088ff-7a4f-4480-8b49-557488b7ec4b" />
</p>

This figure shows the ngspice measurement output used for extracting V_IL and V_IH.

### Commands

```
let gain = (abs(deriv(Vout)) >= 1) * 1.8
meas vil dc find Vin when gain=1 cross=1
meas vih dc find Vin when gain=1 cross=last

```

### Measured Values

```text
V_IL = 0.749555 V
V_IH = 1.003444 V
```

These measured values are used to calculate the inverter noise margins.

---

# 4. 📐 Calculations

## Extracted Parameters

The following values were extracted from the VTC and gain analysis of the CMOS inverter.

| Parameter | Symbol | Value |
|-----------|---------|--------|
| Supply Voltage | V_DD | 1.8 V |
| Output High Voltage | V_OH | 1.8 V |
| Output Low Voltage | V_OL | ≈ 0 V |
| Input Low Voltage | V_IL | 0.749555 V |
| Input High Voltage | V_IH | 1.003444 V |

---

## 🔹 Low Noise Margin (NM_L)

The low noise margin represents the maximum noise voltage that can be superimposed on a logic LOW signal without causing an incorrect logic interpretation.

```text
NM_L = V_IL - V_OL

     = 0.749555 - 0

     = 0.749555 V

     = 749.6 mV
```

---

## 🔹 High Noise Margin (NM_H)

The high noise margin represents the maximum noise voltage that can be superimposed on a logic HIGH signal without causing an incorrect logic interpretation.

```text
NM_H = V_OH - V_IH

     = 1.8 - 1.003444

     = 0.796556 V

     = 796.6 mV
```

---

## 🔹 Noise Margin as a Percentage of V_DD

Expressing the noise margins as a percentage of the supply voltage provides a normalized measure of noise immunity.

### Low Noise Margin Percentage

```text
(NM_L / V_DD) × 100

= (0.749555 / 1.8) × 100

= 41.64 %
```

### High Noise Margin Percentage

```text
(NM_H / V_DD) × 100

= (0.796556 / 1.8) × 100

= 44.25 %
```

---

## 🔹 Transition Region Width

The transition width represents the input voltage range over which the inverter switches from logic HIGH to logic LOW.

```text
ΔV_transition = V_IH - V_IL

              = 1.003444 - 0.749555

              = 0.253889 V

              = 253.9 mV
```

A smaller transition width generally indicates a sharper switching characteristic and stronger noise immunity.

---

# 5. 📈 Result Analysis

## 🔹 Noise Immunity

The extracted noise margins are:

- **NM_L = 749.6 mV**
- **NM_H = 796.6 mV**

Both values are large compared to the supply voltage of **1.8 V**, indicating that the inverter can tolerate significant voltage disturbances without causing logic errors.

The high values of NM_L and NM_H demonstrate strong immunity against noise arising from power supply fluctuations, crosstalk, switching activity, and other circuit-level disturbances.

---

## 🔹 Noise Margin Balance

The difference between the high and low noise margins is:

```text
NM_H - NM_L

= 796.6 - 749.6

= 47.0 mV
```

This small difference indicates that the inverter exhibits reasonably balanced noise immunity for both logic HIGH and logic LOW levels.

Balanced noise margins are desirable because they ensure reliable operation regardless of whether noise affects a HIGH or LOW logic state.

---

## 🔹 Switching Characteristics

The Voltage Transfer Characteristic (VTC) exhibits:

- A sharp transition region
- High gain near the switching threshold
- Full rail-to-rail output swing

The measured transition width is:

```text
ΔV_transition = 253.9 mV
```

A relatively narrow transition region indicates strong switching behaviour and improved logic-level restoration capability.

---

# 6. 📊 Summary

| Metric | Value |
|----------|----------|
| Supply Voltage (V_DD) | 1.8 V |
| Input Low Voltage (V_IL) | 749.6 mV |
| Input High Voltage (V_IH) | 1003.4 mV |
| Output Low Voltage (V_OL) | ≈ 0 V |
| Output High Voltage (V_OH) | ≈ 1.8 V |
| Low Noise Margin (NM_L) | 749.6 mV |
| High Noise Margin (NM_H) | 796.6 mV |
| NM_L / V_DD | 41.64 % |
| NM_H / V_DD | 44.25 % |
| Transition Width | 253.9 mV |

---

# 7. ✅ Conclusion

- The post-layout CMOS inverter implemented using the **SKY130A PDK** demonstrates strong noise immunity and robust switching behaviour.

- The extracted noise margins of **749.6 mV (NM_L)** and **796.6 mV (NM_H)** correspond to approximately **41.6%** and **44.3%** of the supply voltage, respectively, indicating excellent tolerance to input noise and voltage disturbances.

- The inverter exhibits a sharp transition region, balanced noise margins, and full rail-to-rail output swing, all of which are desirable characteristics for reliable digital logic operation.


