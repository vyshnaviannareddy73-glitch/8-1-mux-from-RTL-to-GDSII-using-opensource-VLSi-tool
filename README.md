<div align="center">

# 8-to-1 Multiplexer (MUX) - Complete RTL-to-GDSII ASIC Flow 🚀
### A Silicon Journey: From Behavioral Verilog to Sky130 Manufacturing-Ready Layout

[![OpenLane](https://img.shields.io/badge/OpenLane-Automated%20Flow-blue.svg)](https://github.com/The-OpenROAD-Project/OpenLane)
[![PDK](https://img.shields.io/badge/PDK-Sky130-red.svg)](https://github.com/google/skywater-pdk)
[![Language](https://img.shields.io/badge/Language-Verilog-blueviolet.svg)](#)
[![Status](https://img.shields.io/badge/Status-DRC%20%26%20LVS%20Clean-success.svg)](#)

*Documenting the complete physical design realization of an 8-to-1 Multiplexer using the open-source OpenLane toolchain and SkyWater 130nm standard cell library.*

<img src="mux ss/klayout.png" alt="Final KLayout GDS Layout" width="800px">

---

**[Explore the Visual Journey](#-the-rtl-to-gdsii-visual-journey) • [Reproduce the Flow](#-how-to-reproduce) • [Repository Structure](#-repository-structure)**

</div>

---

## 💡 Project Overview & Physical Constraints

An **8-to-1 Multiplexer (MUX)** serves as a fundamental digital switch that routes one of eight input signals to a single output line based on a 3-bit selection control. While architecturally straightforward, executing its physical implementation through an ASIC backend exposes critical design trade-offs regarding delay optimization, routing congestion of multiple input buses, and area allocation.

This project drives a behavioral Verilog model of an 8-to-1 MUX completely through the automated **OpenLane** flow to achieve a tapeout-ready macro layout targeting the **SkyWater 130nm (sky130A)** open PDK.

---

## 🛠️ Tools & Technology Stack

| Flow Stage | Open-Source Tool / PDK | Function |
| :--- | :--- | :--- |
| **Process Node** | SkyWater 130nm (`sky130A`) | Target silicon manufacturing technology |
| **Functional Verification** | Icarus Verilog (`iverilog`) & GTKWave | RTL simulation and waveform debugging |
| **Logic Synthesis** | Yosys & abc | Gate-level netlist generation & tech-mapping |
| **Floorplan & Placement** | OpenROAD | Core/die initialization, PDN, and cell placement |
| **Clock Tree & Routing** | OpenROAD (TritonRoute) | Global and detailed interconnect routing |
| **Physical Verification** | Magic, KLayout & Netgen | Signoff DRC checks, GDS viewing, and LVS netlist comparison |

---

## 📖 The RTL-to-GDSII Visual Journey

Follow the automated physical design pipeline execution step-by-step with verified visual checkpoints from our runtime workspace:

### 1️⃣ RTL Design & Functional Verification
The behavioral logic of the 8-to-1 MUX was validated using a comprehensive testbench. Waveform inspection confirms flawless input selection and signal propagation to the output `Y` across varied test vectors for the selection lines `S[2:0]` with zero delay overhead in behavioral simulation.

<p align="center">
  <img src="mux ss/waveforms.png" width="90%" alt="GTKWave Verification Waveforms">
</p>

### 2️⃣ Logic Synthesis (Yosys)
The Verilog source description is translated into structural gates. Cells are cleanly mapped to the specific standard cells of the `sky130_fd_sc_hd` (high density) library. Based on the synthesis static analysis reports, the design achieved the following footprint and power metrics:

*   **Total Cell Count:** 20 cells
*   **Total Chip Area:** 183.926 µm²
*   **Total Power Consumption:** 1.45e-05 W (14.5 µW)
    *   *Switching Power:* 9.28e-06 W (64.1%)
    *   *Internal Power:* 5.20e-06 W (35.9%)
    *   *Leakage Power:* 7.71e-11 W (~0.0%)

<p align="center">
  <img src="mux ss/area.png" width="49%" alt="Synthesis Area Report">
  <img src="mux ss/power.png" width="49%" alt="Synthesis Power Report">
</p>

### 3️⃣ Floorplanning & Power Delivery Network (PDN)
The core boundary is initialized and I/O pad/pin groups are arranged along the periphery. The PDN macro generation drops vertical and horizontal power stripes (`VDD`/`GND`) to guarantee clean rail distribution across the logic matrix.

> 💡 **Pro-Tip for Interactive Workspace Viewing:**
> To explore the layout database interactively in 2D/3D using OpenROAD GUI:
> 1. Start the container: `make mount`
> 2. Open the interface: `openroad -gui`
> 3. Source the layout database file: `read_db runs/<run_name>/results/floorplan/<file_name>.odb`

<p align="center">
  <img src="mux ss/floorplan.png" width="80%" alt="OpenROAD Floorplan Window">
</p>

### 4️⃣ Global & Detailed Placement
Standard cells are legally localized into the core rows. The placement tool evaluates cell congestion and minimizes initial wire length to prevent localized routing density bottlenecks caused by the dense inputs of the MUX.

<p align="center">
  <img src="mux ss/placement.png" width="80%" alt="Global Placement Overview">
</p>

### 5️⃣ Interconnect Routing
Signal routing is successfully resolved over multi-layer metal grids. Global routing budgets the paths, while detailed routing maps out the exact tracks, preserving minimum manufacturing spaces and clearing antenna rule hazards.

<p align="center">
  <img src="mux ss/routing.png" width="80%" alt="Routed Netlist OpenROAD GUI View">
</p>

### 6️⃣ Physical Signoff & Manufacturing Verification (DRC/LVS)
The finished layout was exported to Magic and KLayout for strict physical layout checking. The design achieved flawless verification closure, yielding clean **0 DRC** anomalies, completely coherent **LVS match** alignment, and **0 Antenna** violations.

<p align="center">
  <img src="mux ss/drc.png" width="90%" alt="Manufacturability Summary Report Log">
</p>

---

## 📂 Repository Structure

```text
├── mux ss/              # Verified visual logs, waves, and layout screenshots
├── src/                 # Behavioral Verilog files (*.v) and simulation testbenches
├── config.json          # OpenLane floorplan constraints and design configurations
├── mux8to1.gds          # Exported binary stream format for foundry fabrication
└── README.md            # You are here!
```
## 🚀 How to Reproduce

Rebuild this physical layout blueprint on your local environment:

## Prerequisites:

1.Linux OS (Ubuntu environment recommended)
2.Docker engine installed and configured
3.OpenLane workspace clone with configured Sky130 PDK

### Step 1: Execute Functional Verification

Verify the behavioral netlist prior to logic translation:

Compile design and testbench modules

```
iverilog -o tb_mux8to1 src/mux8to1.v src/tb_mux8to1.v
```
Execute simulation runtime to output VCD dump
```
vvp tb_mux8to1
```
Open wave structures visually
```
gtkwave mux8to1.vcd
```
### Step 2: Run the Physical Design Pipeline

Move this design directory inside your native OpenLane installation route under <OpenLane_Root>/designs/.

1. Mount the interactive OpenLane environment container.
```
make mount
```
2. Run the automated layout generation script
```
./flow.tcl -design mux8to1
```
## 🤝 Acknowledgments

1.Google / SkyWater Foundation: For lowering the barrier of entry by open-sourcing the 130nm PDK.

2.The OpenROAD Project: For developing robust, fully automated EDA placement, routing, and physical abstraction engines.
