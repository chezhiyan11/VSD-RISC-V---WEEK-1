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

## 📚 Index
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

#### 🖼️ Functional Representation -  Verilog Code
```bash
// a21110: 2-input AND into first input of 4-input OR.
module a21110 (
    X = ((A1 & A2) | B1 | C1 | D1)
);

// Verilog simulation functional model.
*/

`timescale 1ns / 1ps
default_nettype none

`celldefine
module sky130_fd_sc_hd__a21110 (
    X,
    A1,
    A2,
    B1,
    C1,
    D1
);
```

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


<p align="center">
  <img src="https://img.shields.io/badge/Concepts-Synthesis,_Hierarchy/Flat-green" />
</p>


## 🏗️ 2. Hierarchical vs Flat Synthesis

Synthesis transforms the **Register Transfer Level (RTL) Verilog description** into a **gate-level netlist** using the standard cell library.  
The resulting netlist represents the design in terms of actual logic gates available in the chosen technology.

---

### 📌 A. Synthesis Steps (Yosys Example)

The synthesis process typically follows these steps:

1. Start Yosys  
2. Read the Liberty timing files  
3. Read the Verilog design files (can include multiple modules)  
4. Synthesize the design  
5. Generate the netlist  
6. Visualize the synthesized design  

---

#### 🖥️ Example Commands

```yosys
# Start Yosys
yosys

# Read Liberty timing file
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read top-level and submodule Verilog files
read_verilog top_module.v and_gate.v or_gate.v

# Synthesize the top-level module
synth -top top_module

# Generate gate-level netlist
write_verilog top_module_netlist.v

# Visualize the hierarchical design
show top_module
```


---

## 🏗️ B. Hierarchical Synthesis

Hierarchical synthesis allows complex designs to be broken into **smaller, manageable modules**, which are then instantiated in a **top-level module**. This approach improves **readability, reusability**, and simplifies **debugging**.

### 🔹 Multiple Modules Example

Consider a design with two simple modules:

**Module 1:** AND Gate
```verilog
module and_gate (
    input A, B,
    output Y
);
    assign Y = A & B;
endmodule
```

**Module 2:** OR Gate
```verilog
module or_gate (
    input A, B,
    output Y
);
    assign Y = A | B;
endmodule
```

**Top-Level Module: Instantiates both AND and OR gates**
```verilog
module top_module (
    input X1, X2, X3, X4,
    output Y_and, Y_or
);
    // Instantiate AND gate
    and_gate u1 (.A(X1), .B(X2), .Y(Y_and));

    // Instantiate OR gate
    or_gate u2 (.A(X3), .B(X4), .Y(Y_or));
endmodule
```
 - Hierarchical design like this allows modular verification and synthesis of large designs efficiently.
---

### 🖼️ Hierarchical Block Representation
<p align="center">
  <!-- Replace 'path_to_your_image.png' with your actual image path -->
  <img src="path_to_your_image.png" alt="Hierarchical Schematic" width="600"/>
</p>

---

### 🔹 Synthesis of Hierarchical Design

Using Yosys, the hierarchical top-level module is synthesized into a gate-level netlist. The tool automatically resolves the hierarchy and generates the corresponding netlist for all instantiated sub-modules.

🖥️ Yosys Commands

```bash
# 1. Start Yosys
yosys

# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 3. Read Verilog design files
read_verilog multiple_modules.v

# 4. Synthesize the design
synth -top multiple_modules

# 5. Generate gate-level netlist
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 6. Visualize the synthesized design
show multiple_modules
```

---

### 🖼️ Multi Module Netlist
<p align="center">
  <!-- Replace 'path_to_your_image.png' with your actual image path -->
  <img src="path_to_your_image.png" alt="Hierarchical Schematic" width="600"/>
</p>

• In visualization, the design shows distinct sub-modules (e.g., Sub-mod 1 and Sub-mod 2) connected

---

**Writing Hierarichal Netlist***
```bash 
# 7. Write Netlist
write_verilog -noattr multiple_modules-hier.v

# 6. gvim viewer
!gvim multiple_modules-hier.v
```
<p align="center">
  <!-- Replace 'path_to_your_image.png' with your actual image path -->
  <img src="path_to_your_image.png" alt="Hierarchical Schematic" width="600"/>
</p>


• It is clearly visible that the Hierarchy are preserved. 
---


### CMOS Visualization
<p align="center">
  <!-- Replace 'path_to_your_image.png' with your actual image path -->
  <img src="path_to_your_image.png" alt="Hierarchical Schematic" width="600"/>
</p>

• Stacked Pmos is always bad since the Pmos have *poor mobility*

---

### 🏗️ C. Flat Synthesis

In **flat synthesis**, the entire design is optimized and represented as a single, large netlist, losing the original modular structure.

- The command `yosys > flatten` is used to flatten the hierarchy.
- After flattening, the hierarchy of the sub-modules is no longer preserved; they are written as **one single netlist**.
- When visualized after flattening, although sub-modules were synthesized, they are merged, meaning **only the top level of the design is visible**.

**Writing Flat Netlist**
```bash 
# 7. Write Netlist
write_verilog -noattr multiple_modules-flat.v

# 6. gvim viewer
!gvim multiple_modules-flat.v
```

<p align="center">
  <!-- Add your flat synthesis netlist visualization here -->
  <img src="path_to_flat_synthesis_image.png" alt="Flat Synthesis Netlist" width="600"/>
</p>

- To see the visualization after Generating the Netlist
```bash 
yosys> flatten
yosys> show
```
<p align="center">
  <!-- Add your flat synthesis netlist visualization here -->
  <img src="path_to_flat_synthesis_image.png" alt="Flat Synthesis Netlist" width="600"/>
</p>

---

### 🏗️ D. Submodule Level Synthesis

In **submodule level synthesis**, only a single submodule of the design is synthesized at a time.  

- Only **one submodule** is visible after synthesis, while the rest of the hierarchy is preserved.
- **Module level synthesis** is preferred when there are **multiple instances of the same module** in a design.
- This approach follows a **divide-and-conquer methodology**, allowing designers to handle **large and complex netlists efficiently**.
- Each submodule can be synthesized, optimized, and verified individually before integrating into the top-level design.
- The Netlist generation is not required since there is no practical usage of Hardware.

🖥️ Yosys Commands

```bash
# 1. Start Yosys
yosys

# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 3. Read Verilog design files
read_verilog multiple_modules.v

# 4. Synthesize the design
synth -top submodule1

# 5. Visualize the synthesized design
show 
```
<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---

<div align="center">

  <table align="center">
    <tr>
      <td>
        <img src="https://img.shields.io/badge/DFF-Flops-red" alt="DFF/Flops" />
      </td>
      <td>
        <img src="https://img.shields.io/badge/Sequential-Logic-yellow" alt="Sequential Logic" />
      </td>
      <td>
        <img src="https://img.shields.io/badge/Combinational-Logic-blue" alt="Combinational Logic" />
      </td>
    </tr>
  </table>
</div>




## 🔁 3. Flip-Flop Coding Styles & Optimizations

#### A. Importance of Flops (DFFs)
Flip-flops (Flops) and latches are fundamental elements in **sequential logic** designs:

- Their primary role is to **isolate combinatorial circuits**, ensuring proper timing and data flow.
- Flops **prevent glitches** (unwanted transient signals) from propagating through combinational logic, maintaining signal integrity.
- They store and synchronize data between different stages of a design, acting as **timing checkpoints**.

<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---
  

#### B. Flop Control Pins
Flops include control pins to manage their behavior:

- **Reset (Rst):** Forces the output to a known state (0 or 1) when activated.
- **Set (Set):** Forces the output to the opposite known state (1 or 0) when activated.
- Both Reset and Set pins can be **synchronous** (triggered with the clock) or **asynchronous** (independent of the clock).
- Proper use of control pins ensures **predictable operation** and reliable sequential logic behavior.

#### DFF-Verilog Code
### Example: Positive-Edge Triggered D Flip-Flop with Asynchronous Reset

```verilog
module dff (
    input wire clk,    
    input wire rst,      
    input wire d,       
    output reg q        
);

always @(posedge clk, posedge asyn_reset) begin
    if (asyn_reset)
        q <= 1'b0;      
    else
        q <= d;       
end

endmodule
```

 - When there is a change in clock or change in reset, the always block executes.
 - From the Code it is Clear that upon any input signal the output is going low and hence it is a **Asynchronous Reset**.

---

## 🔹 DFF Control Types

| Control Type   | Description |
| -------------- | ----------- |
| **Asynchronous** | The control (Reset/Set) acts **irrespective of the clock**. The output changes immediately when the control signal is active. |
| **Synchronous**  | The control (Reset/Set) acts **in proportion to the clock**. The output changes **only on the active clock edge** when the control signal is active. |

---
<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---



## 🧪 4. Lab 

## 🔹 Lab 4 – DFF Design Modules

| Module Name                     | Functionality Description |
| -------------------------------- | ------------------------- |
| **dff_asyn_set.v**               | D Flip-Flop with **asynchronous set**. The output is immediately set when the Set input is high, irrespective of the clock. |
| **dff_asyn_reset.v**             | D Flip-Flop with **asynchronous reset**. The output is immediately cleared when the Reset input is high, irrespective of the clock. |
| **dff_asyn_syncres.v**     | D Flip-Flop with **asynchronous set** and **synchronous reset**. The output is set asynchronously but reset only on the active clock edge. |
 
---

## 🔹 A. Analysis of dff_asyn_set.v

### Module Overview
The `dff_asyn_set.v` module implements a **D Flip-Flop with asynchronous set** functionality.

- **Asynchronous Set:** The output Q is immediately set to 1 when the `Set` input is high, regardless of the clock signal.
- **Clock Behavior:** When `Set` is low, the flip-flop operates normally with the rising edge of the clock controlling the D → Q transfer.
- **Outputs:**  
  - `Q` → Main output  
  - `Q_bar` → Complement of Q

### Behavioral Description
- When `Set = 1` → `Q = 1` immediately  
- When `Set = 0` → `Q` follows the D input at the rising edge of the clock

### Example Verilog Code Snippet

```verilog
module dff_asyn_set (
    input D,
    input Clk,
    input Set,
    output reg Q,
    output Q_bar
);
assign Q_bar = ~Q;

always @(posedge Clk or posedge Set) begin
    if (Set)
        Q <= 1'b1; // Asynchronous set
    else
        Q <= D;    // Normal D Flip-Flop operation
end
endmodule
```

## 🖥️ Waveform Analysis Using GTKWave

After simulating the `dff_asyn_set.v` module, we can visualize the behavior of the flip-flop using **GTKWave**.  
This helps in verifying the asynchronous set functionality and the D → Q transfer on the rising edge of the clock.

### Typical Waveform Analysis
- Observe **Set = 1** → Q immediately goes high  
- Observe **Set = 0** → Q follows D at the rising edge of Clk  
- Q_bar always shows the complement of Q  

### Example: Viewing the Waveform
```bash
# Run GTKWave on the generated VCD file
gtkwave tb_dff_asyn_set.vcd
```
<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---

## 🔹 B. Analysis of dff_asyn_reset.v

### Module Overview
The `dff_asyn_reset.v` module implements a **D Flip-Flop with asynchronous reset** functionality.

- **Asynchronous Reset:** The output Q is immediately reset to 0 when the `Reset` input is high, regardless of the clock signal.
- **Clock Behavior:** When `Reset` is low, the flip-flop operates normally with the rising edge of the clock controlling the D → Q transfer.
- **Outputs:**  
  - `Q` → Main output  
  - `Q_bar` → Complement of Q

### Behavioral Description
- When `Reset = 1` → `Q = 0` immediately  
- When `Reset = 0` → `Q` follows the D input at the rising edge of the clock

### Example Verilog Code Snippet

```verilog
module dff_asyn_reset (
    input D,
    input Clk,
    input Reset,
    output reg Q,
    output Q_bar
);
assign Q_bar = ~Q;

always @(posedge Clk or posedge Reset) begin
    if (Reset)
        Q <= 1'b0; // Asynchronous reset
    else
        Q <= D;    // Normal D Flip-Flop operation
end
endmodule
```

## 🖥️ Waveform Analysis Using GTKWave

After simulating the `dff_asyn_reset.v` module, we can visualize the behavior of the flip-flop using **GTKWave**.  
This helps in verifying the asynchronous reset functionality and the D → Q transfer on the rising edge of the clock.

### Typical Waveform Analysis
- Observe **Reset = 1** → Q immediately goes low  
- Observe **Reset = 0** → Q follows D at the rising edge of Clk  
- Q_bar always shows the complement of Q  

### Example: Viewing the Waveform
```bash
# Run GTKWave on the generated VCD file
gtkwave tb_dff_asyn_reset.vcd
```

<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---

## 🔹 C. Analysis of dff_asyn_syncres_reset.v

### Module Overview
The `dff_asyn_syncres_reset.v` module implements a **D Flip-Flop with asynchronous and synchronous reset** functionality.

- **Asynchronous Reset:** The output Q is immediately reset to 0 when the `Async_Reset` input is high, regardless of the clock signal.
- **Synchronous Reset:** The output Q is reset to 0 on the rising edge of the clock when the `Sync_Reset` input is high.
- **Clock Behavior:** When both reset signals are low, the flip-flop operates normally with the rising edge of the clock controlling the D → Q transfer.
- **Outputs:**  
  - `Q` → Main output  
  - `Q_bar` → Complement of Q

### Behavioral Description
- When `Async_Reset = 1` → `Q = 0` immediately  
- When `Sync_Reset = 1` at rising edge of Clk → `Q = 0`  
- When both resets = 0 → `Q` follows the D input at the rising edge of the clock  

### Example Verilog Code Snippet

```verilog
module dff_asyn_syncres_reset (
    input D,
    input Clk,
    input Async_Reset,
    input Sync_Reset,
    output reg Q,
    output Q_bar
);
assign Q_bar = ~Q;

always @(posedge Clk or posedge Async_Reset) begin
    if (Async_Reset)
        Q <= 1'b0;               // Asynchronous reset
    else if (Sync_Reset)
        Q <= 1'b0;               // Synchronous reset
    else
        Q <= D;                  // Normal D Flip-Flop operation
end
endmodule
```

## 🖥️ Waveform Analysis Using GTKWave

After simulating the `dff_asyn_syncres_reset.v` module, we can visualize the behavior of the flip-flop using **GTKWave**.  
This helps in verifying both the asynchronous and synchronous reset functionality along with the D → Q transfer on the rising edge of the clock.

### Typical Waveform Analysis
- Observe **Asynchronous Reset = 1** → Q immediately goes low  
- Observe **Synchronous Reset = 1** at rising edge of Clk → Q is reset to 0  
- Observe Reset = 0 → Q follows D at the rising edge of Clk  
- Q_bar always shows the complement of Q  

### Example: Viewing the Waveform
```bash
# Run GTKWave on the generated VCD file
gtkwave tb_dff_asyn_syncres_reset.vcd
```

<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---

## ⚙️ 5. Optimization in Synthesis

### Optimization Concept and Example
During synthesis, optimizations aim to reduce **area, power, and delay** while maintaining correct functionality.  
The synthesis tool can detect redundant logic or simplify operations.

**Example: Mux Optimization**  
- Mux-2: A[2:0] → Mux[3:0] B  
- Observation: Some operations may not require additional hardware, so the synthesizer optimizes automatically.

<p align="center">
  <!-- Add your submodule synthesis visualization here -->
  <img src="path_to_submodule_image.png" alt="Submodule Synthesis Netlist" width="600"/>
</p>

---


## ✅ 6. Summary

### I. Timing Libraries (.libs)
- Standard cell behavior is defined using **.lib files** (e.g., `sky130_fd_sc_hd__tt_025C_1V80.lib`).
- Key operating parameters: **Process (P), Voltage (V), Temperature (T)**.
- Cells characterized for **power, area, delay**, with multiple flavors (slow/medium/fast) for design trade-offs.

### II. Synthesis Methods
**Hierarchical Synthesis:**  
- Retains module structure; ideal for multiple instances of same module.  
- Steps: `yosys` → `read_liberty` → `read_verilog` → `synth -top` → `abc -liberty` → `show` → `write_verilog`.

**Flat Synthesis:**  
- Collapses hierarchy; entire design is one netlist.  
- Command: `Yosys > flatten` → output netlist contains only top-level view.

**Submodule-Level Synthesis:**  
- Only one submodule is synthesized at a time; useful in divide-and-conquer for large designs.

### III. Sequential Logic – Flip-Flops
- **D Flip-Flops (DFFs)** isolate combinational logic and prevent glitches.  
- **Control Pins:** Reset, Set (synchronous or asynchronous).  
  - **Asynchronous:** Acts immediately, ignores clock.  
  - **Synchronous:** Acts on active clock edge.

### IV. Optimization
- Uses **ABC tool** for logic minimization and mapping to standard cells.  
- Commands: `Yosys > abc -liberty ...`  
- Example: Mux-2 optimization may remove unnecessary hardware for simple logic (`Y = 2'b01`).

> ⚡ Key Takeaway: Timing libraries define silicon behavior; synthesis converts RTL → netlist (hierarchical/flat); flip-flops control sequential logic; optimization improves area, power, and performance.


---

## 📚 References

1. Kunal Ghosh, **Sky130 RTL Design and Synthesis Workshop**, GitHub Repository: [https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop](https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop)  
2. Yosys Open SYnthesis Suite, [http://www.clifford.at/yosys/](http://www.clifford.at/yosys/)  
3. Icarus Verilog, [http://iverilog.icarus.com/](http://iverilog.icarus.com/)  
4. GTKWave Waveform Viewer, [http://gtkwave.sourceforge.net/](http://gtkwave.sourceforge.net/)  
5. SkyWater PDK (Sky130), [https://skywater-pdk.readthedocs.io/en/latest/](https://skywater-pdk.readthedocs.io/en/latest/)  
6. J. Bhasker, **A Verilog HDL Primer**, Star Galaxy Publishing, 1999.  
7. ASIC Design Tutorials: Timing, Synthesis, and Optimization Concepts – Course Notes, VSD SoC Tapeout Program.

