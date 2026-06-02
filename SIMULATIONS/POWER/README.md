# ⚡ Power Analysis of CMOS Inverter

This section presents the power analysis of the post-layout CMOS inverter implemented using the **SKY130A PDK** and simulated using **ngspice**. Power analysis is an important aspect of digital circuit design because it directly impacts battery life, thermal performance, and overall system efficiency.

The objective of this analysis is to evaluate the power consumed by the inverter during switching operation and to understand the contributions of different power dissipation mechanisms in CMOS technology.

---

# 1. 📖 Theoretical Background

## Why Power Analysis is Important

Although CMOS technology is widely known for its low-power operation, practical CMOS circuits still consume power due to transistor leakage currents and switching activity.

The total power dissipation of a CMOS inverter can be expressed as:

```text
P_total = P_static + P_dynamic + P_short-circuit
```

where:

* **P_static** = Leakage power
* **P_dynamic** = Capacitive switching power
* **P_short-circuit** = Power consumed during transitions

---

## 🔹 Static Power Dissipation

Static power is consumed even when the inverter is not switching.

Ideally, one transistor is OFF while the other is ON, preventing any direct current path between VDD and GND. However, real MOSFETs exhibit leakage currents due to:

* Subthreshold conduction
* Gate oxide leakage
* Junction leakage

Static power is given by:

```text
P_static = I_leakage × V_DD
```

where:

* I_leakage = Total leakage current
* V_DD = Supply voltage

For modern CMOS circuits, static power becomes increasingly important as device dimensions shrink.

---

## 🔹 Dynamic Power Dissipation

Dynamic power is the dominant component of power consumption in CMOS logic during switching.

Whenever the output node changes state, the load capacitance is charged or discharged, resulting in energy consumption.

Dynamic power is given by:

```text
P_dynamic = α C_L V_DD² f
```

where:

* α = Switching activity factor
* C_L = Load capacitance
* V_DD = Supply voltage
* f = Switching frequency

Key observations:

* Dynamic power increases quadratically with V_DD.
* Dynamic power increases linearly with switching frequency.
* Dynamic power increases with load capacitance.

---

## 🔹 Short-Circuit Power Dissipation

During an input transition, both NMOS and PMOS transistors conduct simultaneously for a brief period.

This creates a temporary direct path between VDD and GND, causing short-circuit current to flow.

```text
P_short-circuit = V_DD × I_SC(avg)
```

where:

* I_SC(avg) = Average short-circuit current

Short-circuit power depends on:

* Input rise time
* Input fall time
* Supply voltage
* Transistor sizing

In a well-designed CMOS inverter, short-circuit power is typically much smaller than dynamic power.

---

## 🔹 Supply Current (I_DD)

The supply current represents the current drawn from the power source.

For a capacitive load, the charging or discharging current is given by:

```text
I_C = C_L (dV/dt)
  ```
The supply current (I_DD) is dominated by the capacitive charging/discharging current and also includes small leakage and short-circuit current components.

Characteristics:

* Nearly zero during steady-state operation
* Peaks during switching transitions
* Determines the instantaneous power consumption of the inverter

The supply current waveform therefore provides valuable insight into inverter switching behavior.

---

# 2. 🧪 Simulation Setup

### Testbench: `tb_power_inverter.spice`

```
* CMOS Inverter Power Analysis

.lib /home/praka/whyRD_eda_bundle/open_pdks/sky130/sky130A/libs.tech/ngspice/sky130.lib.spice tt

.include Inverter.spice

* Supply
VDD VDD 0 1.8
VSS VSS 0 0

* Input stimulus
VIN Vin 0 PULSE(0 1.8 0 100p 100p 10n 20n)

* Instantiate extracted inverter
XINV Vout Vin VSS VDD Inverter

* Load capacitance
Cload Vout 0 0.5p

.tran 10p 100n

.control
run

* Waveforms
plot vdd#branch*1000 vout


*-----------------------------------------------
* Power Calculation
*-----------------------------------------------

* Integrate supply current from 20ns to 40ns
meas tran current_integrate integ vdd#branch from=30e-09 to=40e-09

let power_intermidiate = current_integrate*1.8

let average_power = power_intermidiate/(10e-09)

echo "--------------------------------"
echo "Average Power (W)"
print average_power

.endc
.end

```


The post-layout extracted inverter was simulated using transient analysis to measure the supply current and average power consumption.

| Parameter | Value |
|-----------|--------|
| Technology | SKY130A PDK |
| Simulator | ngspice |
| Supply Voltage (V_DD) | 1.8 V |
| Analysis Type | Transient |
| Simulation Time | 100 ns |
| Input Signal | Pulse Wave |
| Load Capacitance | 0.5 pF |
| Process Corner | TT |
| Purpose | Power Consumption Analysis |

---

# 3. 📊 Simulation Results

## 📌 Output Voltage Response

<p align = "center">
<img width="1200" height="650" alt="Vout Vs Current" src="https://github.com/user-attachments/assets/18815b6d-f29f-4526-bcbe-f447c59b184c" />
</p>

### Observations

* Full rail-to-rail switching is achieved.
* Output transitions between approximately 0 V and 1.8 V.
* Proper logic inversion is observed throughout the simulation.
* Fast transitions indicate strong drive capability.

### Significance

The observed output waveform confirms correct functionality of the post-layout CMOS inverter under dynamic operating conditions.

---

## 📌 Supply Current Analysis

<p align = "center">
<img width="846" height="500" alt="image" src="https://github.com/user-attachments/assets/4c2f5f1a-559c-446d-aec2-eed8bc8537e2" />
</p>

  > The red waveform represents the supply current (I_DD).

### Observations

* Current spikes appear only during switching transitions.
* Near-zero current is observed during steady logic states.
* Peak current occurs when both NMOS and PMOS conduct simultaneously.
* Current pulses correspond directly to charging and discharging of the output node capacitance.

### Significance

The current waveform confirms the expected power behavior of a CMOS inverter, where power is primarily consumed during switching events.

---

## 📌 Average Power Measurement

<p align = "center">
<img width="684" height="604" alt="Average Power1" src="https://github.com/user-attachments/assets/59d2bf15-2663-4d58-986f-75f446cbc0f2" />
</p>

### Average Power Formula

<p align = "center">
<img width="860" height="134" alt="image" src="https://github.com/user-attachments/assets/681ffd4d-9503-4b1f-810e-4894a0ad064f" />
</p>

```
P_avg = 1/T * (V_DD × ∫ I_DD(t) dt) 
```

### ngspice Measurement

```text
Average Power = -1.61106e-04 W
```

The negative sign indicates that power is delivered by the voltage source.

Therefore, the magnitude of the average power consumed by the inverter during the measurement interval is:

```text
P_avg = 1.61106 × 10⁻⁴ W
```

```text
P_avg = 161.1 µW
```

---

# 4. 📐 Power Calculation

## Average Power

```text
P_avg = 161.1 µW
```

## Energy Consumed During Measurement Interval

Measurement interval:

```text
T = 10 ns
```

Energy consumed:

```text
E = P × T
```

```text
E = 161.1 × 10⁻⁶ × 10 × 10⁻⁹
```

```text
E = 1.611 × 10⁻¹² J
```

```text
E = 1.611 pJ
```

Therefore, the energy consumed by the inverter during the 10 ns measurement interval is approximately **1.611 pJ**.

---

# 5. 📈 Result Analysis

## 🔹 Static Power

The measured current remains close to zero during stable logic states, indicating negligible static power consumption.

This behavior is expected in CMOS technology because one transistor remains OFF during steady-state operation.

---

## 🔹 Dynamic Power

The dominant power component observed in the simulation is dynamic power.

Current spikes occur only during output transitions, confirming that most power is consumed while charging and discharging the output capacitance.

---

## 🔹 Short-Circuit Power

Small current pulses are also produced when both NMOS and PMOS conduct simultaneously during switching.

These short-duration currents contribute to short-circuit power dissipation.

The observed waveform confirms the presence of this expected CMOS behavior.

---

## 🔹 Overall Power Efficiency

The inverter demonstrates:

* Negligible static power consumption
* Limited short-circuit power
* Efficient dynamic switching operation
* Full rail-to-rail output swing

These characteristics indicate an energy-efficient CMOS implementation.

---

# 6. 📊 Summary

| Metric                   | Value         |
| ------------------------ | ------------- |
| Technology               | SKY130A       |
| Supply Voltage (V_DD)    | 1.8 V         |
| Analysis Type            | Transient     |
| Simulation Time          | 100 ns        |
| Average Power | 161.1 µW |
| Energy (30 ns – 40 ns Window) | 1.611 pJ |
| Static Power             | Negligible    |
| Dominant Power Component | Dynamic Power |
| Output Swing             | 0 V – 1.8 V   |
| Verification Status      | ✅ Functional  |

---

# 7. ✅ Conclusion

* The post-layout CMOS inverter demonstrates the expected low-power characteristics of CMOS technology with negligible static power consumption.
* Power is primarily consumed during switching transitions, with dynamic power being the dominant dissipation mechanism.
* The measured average power consumption of **161.1 µW** confirms the efficient operation and correct post-layout implementation of the SKY130A CMOS inverter.
  
