<div align="center">
  <h1>⚡ Day 2 - Timing Libraries, Hierarchical vs Flat Synthesis & Flop Coding Styles</h1>
</div>

<div align="center">
  <table>
    <tr>
      <td><img src="https://img.shields.io/badge/Week1-Day2-blueviolet" /></td>
      <td><img src="https://img.shields.io/badge/.lib-Files-green" /></td>
      <td><img src="https://img.shields.io/badge/Synthesis-Hier_vs_Flat-orange" /></td>
      <td><img src="https://img.shields.io/badge/Flops-Coding_Styles-blue" /></td>
      <td><img src="https://img.shields.io/badge/Tools-Yosys_HandsOn-red" /></td>
    </tr>
  </table>
  <p>🎯 <b>Day 2</b> of the RISC-V SoC Tapeout program focuses on exploring <b>timing libraries</b>, comparing <b>hierarchical vs flat synthesis</b>, and experimenting with <b>flip-flop coding styles and optimizations</b>. 🏗️  
  Through structured labs and lectures, we dive deeper into how technology libraries interact with synthesis and how RTL coding choices affect the resulting gate-level netlists. ⚡</p>
</div>

---

## 🛠️ Tools Used

| Tool      | Description                           | Purpose |
|-----------|---------------------------------------|---------|
| **Icarus Verilog (iverilog)** | Open-source Verilog simulator | Compiles & runs Verilog RTL + Testbench, generates `.vcd` waveforms |
| **GTKWave** | Waveform viewer | Visualizes `.vcd` files generated from simulations |
| **Yosys** | Open-source synthesis tool | Converts RTL to gate-level netlist using `.lib` standard cells |

<p align="center">
  <img src="https://img.shields.io/badge/Flow-RTL→Sim→Waveforms→Synthesis-yellow" />
</p>

---

## 📚 Table of Contents
1. ⏱️ Introduction to Timing `.lib` Files  
2. 🏗️ Hierarchical vs Flat Synthesis  
3. 🔁 Flip-Flop Coding Styles & Optimizations  
4. 🧪 Lab Activities & Experiments  
5. 🛠️ Tools Used (Table)  
6. ✅ Summary  

---

## ⏱️ 1. Introduction to Timing Libraries (.libs)

Timing libraries, often referred to as **.lib files**, are essential in digital design. They define the **electrical, timing, power, and functional behavior** of standard cells used during synthesis and verification.

---

### 📌 A. Library Parameters and Operating Conditions
The performance of a design depends on three key parameters:

1. **Voltage (V):** Specifies the operating voltage of the circuit.  
2. **Process (P):** Represents the fabrication process corner. Variations occur due to inherent manufacturing differences.  
3. **Temperature (T):** Defines the operating temperature range.  

These three together — **Voltage, Process, and Temperature (PVT)** — determine how silicon behaves under different conditions.

---

#### 🔍 Example Library File
Highlighted example:  
**`sky130_fd_sc_hd__tt_025C_1v80.lib`**  

- `tt` → Typical process corner  
- `025C` → Temperature = 25 °C  
- `1v80` → Operating voltage = 1.80 V  

---

#### 📊 Example Operating Conditions

| Parameter      | Value              |
|----------------|--------------------|
| Technology     | CMOS               |
| Delay Model    | Table Lookup       |
| Time Units     | 1 ns               |
| Voltage        | 1.80 V             |
| Temperature    | 25 °C              |

---

### 📌 B. Cell Characterization and Flavors
The `.lib` file contains information about different **standard cells**.  
Each cell has:  
- **Cell Functionality** – Logic behavior (also available in its Verilog model)  
- **Timing Characteristics** – Delay, setup, hold, and transition information  
- **Power Characteristics** – Leakage and dynamic power  

---

#### 📂 Example `.lib` Snippet
```liberty
cell ("sky130_fd_sc_hd__a2111o_1") {
  leakage_power() {
    value : 0.001794500;
    when : "!A1 & !A2 & !B1 & !C1 & D1";
  }
}
```
To understand the functionality of the cell, we can open and see equivalent verilog model in the "gvim" itself.
```bash
:sp ../my_lib/verilog_model/sky130_fd_sc_hd__a2111o.behavioral.v
```
---

#### 🖼️ Functional Representation 

<p align="center">
  <!-- Add your cell schematic/function image here -->
  <img src="path_to_your_image.png" alt="Cell Functional Representation" width="500"/>
</p>

---

#### ✨ Boolean Function

**X = ((A1 & A2) | B1 | C1 | D1)**  

Here, four different signals are combined. Based on low/high conditions, **2⁵ = 32 input combinations** exist.  
For each combination, the `.lib` defines parameters such as **delay, power, and transition**.

---

#### 📊 Example: Different Flavors of AND Gate Cells

Standard cells are available in multiple "flavors" to balance **area, power, delay, and performance**:

| Cell Flavor | Area (µm²)     | Speed      | Power Consumption | Delay Characteristics |
|-------------|----------------|------------|-------------------|------------------------|
| **AND2_0**  | 6.25 × 10⁸     | Very Slow  | Very Low          | High Delay             |
| **AND2_2**  | 7.50 × 10⁸     | Medium     | Moderate          | Medium Delay           |
| **AND2_4**  | 8.75 × 10⁸     | Very Fast  | High              | Very Low Delay         |

---



---

## 🏗️ 2. Hierarchical vs Flat Synthesis

- **Hierarchical Synthesis**  
  - Preserves module boundaries  
  - Easier debugging and modularity  
- **Flat Synthesis**  
  - Flattens design for global optimization  
  - Better PPA (Power, Performance, Area) trade-offs but harder to analyze  

### Lecture Blocks
- **15. Lab5 – Hier vs Flat Synthesis (Part 1)**  
- **16. Lab5 – Hier vs Flat Synthesis (Part 2)**  

---

## 🔁 3. Flip-Flop Coding Styles & Optimizations

Efficient **RTL flop coding** ensures:  
- Correct synthesis mapping  
- Avoiding simulation–synthesis mismatches  
- Cleaner optimization by tools  

### Lecture Blocks
- **17. Flop Coding Styles (Part 1)**  
- **18. Flop Coding Styles (Part 2)**  
- **19. Lab – Flop Synthesis & Simulation (Part 1)**  
- **20. Lab – Flop Synthesis & Simulation (Part 2)**  
- **21. Interesting Optimizations (Part 1)**  
- **22. Interesting Optimizations (Part 2)**  

---

## 🧪 4. Lab Activities & Experiments

- Analyzing `.lib` timing data with Yosys  
- Running synthesis in both **hierarchical & flat modes**  
- Experimenting with **different DFF coding styles**  
- Observing optimizations during synthesis  

---

## ✅ 5. Summary

- Understood how `.lib` timing libraries bridge **RTL and technology cells**.  
- Compared **hierarchical** vs **flat synthesis flows** and their trade-offs.  
- Learned different **flop coding styles** and their synthesis impact.  
- Explored **synthesis optimizations** through Yosys labs.  

---

<div align="center">
  <h2>🎉 End of Day 2 ✨</h2>
</div>
