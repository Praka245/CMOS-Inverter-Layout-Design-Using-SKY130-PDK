# 🚀 **# 🚀 SKY130 Open-Source PDK Setup Guide**

> 💡 *A complete open-source Layout design using the SKY130 PDK*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PDK: SKY130](https://img.shields.io/badge/SKY130-v1.0-green)](https://skywater-pdk.readthedocs.io/)
[![Tools: Magic, Ngspice](https://img.shields.io/badge/Tools-Magic%20%7C%20Ngspice-blue)](https://github.com/RTimothyEdwards/magic)

---

## 🌟 **About the Project**
This repository demonstrates a **full Custom layout design** using the **SKY130 open-source PDK**, developed in collaboration between **Google and SkyWater Technology**.
Perfect for **students** interested in **VLSI, analog/digital IC design, and open-source EDA tools**.

---

## ✨ **Key Features**
| Feature | Description |
|---------|-------------|
| 📐 **130nm CMOS Process** | Industry-standard technology node |
| 🔓 **100% Open-Source** | Free to use, modify, and distribute |
| 🧩 **Full Design Flow** | Schematic → Layout → Verification → Simulation |
| ✅ **DRC & LVS Ready** | Manufacturing-ready checks included |
| ⚡ **SPICE Simulation** | Accurate analog/digital circuit analysis |
| 🔧 **Open-Source EDA** | Compatible with **Magic VLSI, Ngspice, and more** |


---

## 🔧 **Tools & Technologies**
| Tool | Purpose | Link |
|------|---------|------|
| **Magic VLSI** | Layout design & editing | [🔗 Website](http://opencircuitdesign.com/magic/) |
| **Ngspice** | Circuit simulation | [🔗 Website](http://ngspice.sourceforge.net/) |
| **SKY130 PDK** | Process Design Kit | [🔗 GitHub](https://github.com/google/skywater-pdk) |
| **XTerm** | Terminal emulator used to execute EDA tool commands and view simulation outputs | [🔗 Website](https://invisible-island.net/xterm/) |

---

## 🚀 **Quick Start Guide**

# 🔧 Installation of SKY130 PDK, Magic, Ngspice

This guide installs the three essential components required for custom Layout design:

- [**Magic VLSI**](http://opencircuitdesign.com/magic/) (Layout Editor & DRC)
- [**Ngspice**](http://ngspice.sourceforge.net/) (Circuit Simulator)
- [**OpenPDKs with SKY130**](https://github.com/google/skywater-pdk) (Process Design Kit)

---

## 📋 Prerequisites

Install the required dependencies before building the EDA tools:

```bash
sudo apt update

sudo apt install -y \
git build-essential autoconf automake libtool \
tcl-dev tk-dev flex bison libreadline-dev \
libx11-dev libxpm-dev libxext-dev \
libxrender-dev libxaw7-dev zlib1g-dev adms
```
This is usually sufficient for a SKY130 + Magic + Ngspice setup.

---

## 1️⃣ Install Magic VLSI

Magic is used for:

- Layout Design
- DRC Verification
- Layout Extraction

### Clone the Repository

```bash
cd ~
git clone git://opencircuitdesign.com/magic
```

### Build and Install

```bash
cd magic

./configure
make

sudo make install
```

### Verify the Installation

```bash
magic -version
```

---

## 2️⃣ Install Ngspice

Ngspice is used for:

- DC Analysis
- Transient Analysis
- Power Analysis
- Timing Analysis

### Clone the Repository

```bash
cd ~

git clone https://git.code.sf.net/p/ngspice/ngspice ngspice_git
```

### Build Directory

```bash
cd ngspice_git

mkdir release
```

### Generate Configuration Files

```bash
./autogen.sh --adms
```

### Configure Build

```bash
cd release

../configure \
--with-x \
--enable-xspice \
--disable-debug \
--enable-cider \
--with-readline=yes \
--enable-openmp \
--enable-adms
```

### Compile

```bash
make
```

### Install

```bash
sudo make install
```

### Verify the Installation

```bash
ngspice -v
```

---

## 3️⃣ Install OpenPDKs with SKY130

OpenPDKs provides:

- SKY130 Device Models
- DRC Rules
- LVS Rules
- SPICE Libraries
- Technology Files

### Clone the Repository

```bash
cd ~

git clone git://opencircuitdesign.com/open_pdks
```

### Configure SKY130 Installation

```bash
cd open_pdks

./configure --enable-sky130-pdk
```

### Build

```bash
sudo make
```

### Install

```bash
sudo make install
```

### Verify the Installation

```bash
ls /usr/local/share/pdk
```

Expected output:

```text
sky130A
```

---

## 📂 Default SKY130 Installation Location

```text
/usr/local/share/pdk/
  └── sky130A
        ├── libs.ref
        ├── libs.tech
        ├── libraries
        └── ...
```

---

## ✅ Verify Complete Setup

### Check Magic

```bash
magic -version
```

### Check Ngspice

```bash
ngspice -v
```

### Check SKY130 PDK

```bash
ls /usr/local/share/pdk/sky130A
```

If all three commands execute successfully, the environments has been installed correctly.

---

## 🚀 Next Step

After installation, you can begin:

1. CMOS Layout Creation using Magic
2. Design Rule Check (DRC)
3. Layout Extraction
4. SPICE Netlist Generation
5. DC Analysis
6. Transient Analysis
7. Power Analysis
8. Timing Analysis

using the SKY130 Open-Source PDK.

---
