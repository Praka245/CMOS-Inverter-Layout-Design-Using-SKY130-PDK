# 🧠 CMOS Inverter Layout Design - SKY130A PDK

This section documents the physical layout of a CMOS Inverter designed using **Magic VLSI** with the **SkyWater SKY130A** process design kit (PDK). The layout was drawn, verified with DRC, extracted to SPICE, and exported as a GDS file for tape-out readiness.

---

## 🛠️ Tool & Technology

| Item | Details |
|---|---|
| 🧩 Layout Tool | Magic VLSI 8.3 (revision 529) |
| 🏭 PDK | SkyWater SKY130A |
| 📄 Technology File | sky130A.tech |
| 📐 Grid | 0.05 µm × 0.05 µm |
| 🔢 Internal Units | 2 Magic internal units = 1 Lambda |

---

## 🧱 SKY130A Stack Layer Usage

The inverter layout strictly follows the SKY130A backend stack. Each layer is selected based on its electrical role and design rules.

| Layer | Magic Name | Role in This Design |
|---|---|---|
| 🔌 Local Interconnect | `li` | Internal routing, source/drain short connections |
| 🟦 Metal 1 | `m1` | VOUT routing, local horizontal interconnect |
| 🟪 Metal 2 | `m2` | VDD, VSS rails, VIN routing |
| 🟢 N-Diffusion | `ndiff` | NMOS active region |
| 🟣 P-Diffusion | `pdiff` | PMOS active region |
| 🔗 N-Tap | `ntap` | N-well contact tied to VDD |
| 🔗 P-Tap | `ptap` | P-substrate contact tied to VSS |
| ⚙️ N-Diff Contact | `ndc` | ndiff → li1 connection |
| ⚙️ P-Diff Contact | `pdc` | pdiff → li1 connection |
| 🧿 Polysilicon | `poly` | Gate electrode for NMOS & PMOS |
| 🔗 Via (li1→met1) | `mcon` | Local interconnect to Metal 1 |
| 🔗 Via (met1→met2) | `via1` | Metal 1 to Metal 2 connection |
| 🧱 N-Well | `nwell` | PMOS isolation region |

---

## 🏗️ Layout Architecture

The inverter follows a standard CMOS complementary topology with a vertical stacked structure.


<img width="1192" height="479" alt="image" src="https://github.com/user-attachments/assets/902797bd-2e3d-4511-bfba-1830faa6ee46" />

<br>
<br>

- 🟪 **N-Well**: Houses PMOS transistor. Connected to **VDD** using an N-Tap to prevent floating body effects.
- 🟢 **P-Substrate**: Hosts NMOS transistor. Connected to **VSS** using P-Tap to prevent latch-up.

---

## 🔌 Signal Connections & Metal Assignments

### ⚡ VDD (Power)
- Routed using **Metal 2 → via1 → Metal 1 → mcon → li**
- Wide top rail for low resistance
- Connected to:
  - PMOS source
  - N-Well tap

---

### ⏚ VSS (Ground)
- Routed using **Metal 2 → via1 → Metal 1 → mcon → li → licon**
- Bottom rail of the cell
- Connected to:
  - NMOS source
  - P-Substrate tap

---

### 📥 VIN (Input Gate)
- Routed using **Metal 1 → mcon → li → licon → poly**
- Drives both NMOS and PMOS gates simultaneously

---

### 📤 VOUT (Output)
- Routed using **Metal 1 → mcon → li → licon → poly**
- PMOS drain and NMOS drain are shorted together via `li1`

---

## 🧷 Tap Contacts (Latch-Up Prevention)

| Tap Type | Layer | Connection | Purpose |
|---|---|---|---|
| 🔵 P-Tap | `ptap` | VSS | Fixes substrate potential |
| 🔴 N-Tap | `ntap` | VDD | Fixes N-well potential |

These ensure proper body biasing and prevent latch-up conditions.

---

## ✅ DRC (Design Rule Check)

- DRC was performed using Magic’s SKY130 rule deck:

```tcl
% drc check
No errors found.
No errors found.
No errors found.
No errors found.
```

**Result: DRC = 0 errors.** The layout passes all SKY130A design rules. The DRC status indicator in the Magic toolbar confirmed `DRC= 0`.

> See: `DRC_Check.png` - DRC manager showing zero violations with the `drcmgr` window open.

---

## 🧪 SPICE Extraction

- After DRC sign-off, the layout was extracted to a SPICE netlist using Magic's extraction flow.

```tcl
% extract
% ext2spice
exttospice finished.
```

### 📦 Generated Files

* 📄 `Inverter.ext` - Intermediate extracted netlist with parasitic components
* ⚡ `Inverter.spice` - Final SPICE netlist used for post-layout simulation

> 📌 **Reference:** `Spice_Extraction.png` - Magic tkcon showing successful extraction process.

---

## 💾 GDS File Generation

- The final layout was exported as a **GDSII (stream format)** file for tape-out submission using Magic commands.

```tcl
% gds readonly true
% gds rescale false
% gds write Inverter.gds
  Generating output for cell Inverter
```

### ⚙️ Command Breakdown

| Command                  | Purpose                                                             |
| ------------------------ | ------------------------------------------------------------------- |
| `gds readonly true`      | Marks GDS as read-only to prevent accidental modifications          |
| `gds rescale false`      | Disables automatic unit rescaling to preserve exact layout geometry |
| `gds write Inverter.gds` | Writes the final binary GDSII file                                  |

### 🔍 Verification Note

- The generated `Inverter.gds` file was verified using a hex/binary viewer in VS Code.
The **cell name "Inverter"** was confirmed in the GDS stream header, ensuring correct export.

> 📌 **References:**
>
> * `GDS_File_generation.png` - Commands
> * `GDS_File.png` - raw GDS file in my directory 
> * `GDS_View.png` - GDS Raw visualization in VS Code

---

## 🖼️ Layout Images

| Image                        | Description                                |
| ---------------------------- | ------------------------------------------  |
| 📌 `VDD_Connection.png`      | VDD Metal 2 rail to N-Well tap connection  |
| 📌 `VSS_Connection.png`      | VDD Metal 2 rail to P-Well tap connection  |
| 📌 `VOUT_Connection.png`     | VOUT Metal 1 to Li drain routing           |
| 📌 `GATE_Connection.png`     | VIN Metal 1 to polysilicon gate connection |
| 📌 `DRC_Check.png`           | DRC manager showing zero violations        |
| 📌 `Spice_Extraction.png`    | SPICE extraction output in tkcon           |
| 📌 `GDS_File_generation.png` | GDS write command output                   |
| 📌 `GDS_File.png`            | GDS file in my directory                   |
| 📌 `GDS_View.png`            | Final GDS Raw visualization                |

---

## 📁 File Outputs

```
Inverter/
├── 🧩 Inverter.mag        # Magic layout database
├── 🧪 Inverter.ext        # Extracted parasitic netlist
├── ⚡ Inverter.spice      # Post-layout SPICE simulation file
└── 💾 Inverter.gds        # Final GDSII tape-out file
```

---

## 📊 Summary

- CMOS inverter was designed and implemented using Magic VLSI with the SkyWater SKY130A PDK, followed by successful DRC verification, SPICE extraction, and post-layout simulation readiness. The final layout GDSII file was generated successfully.


| Check              | Status     |
| ------------------ | ---------- |
| 🧩 Layout Complete |  ✅        |
| 🧪 DRC Clean       |  ✅        |
| ⚡ SPICE Extraction | ✅       |
| 💾 GDS Export      |  ✅        |

---

