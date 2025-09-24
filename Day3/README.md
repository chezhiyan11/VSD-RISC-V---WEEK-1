<div align="center">
  <h1>⚡ Day 3 - Combinational and Sequential Optimizations</h1>
</div>

<div align="center">
  <table>
    <tr>
      <td><img src="https://img.shields.io/badge/Week1-Day3-blueviolet" /></td>
      <td><img src="https://img.shields.io/badge/Combinational-Logic_Optimization-green" /></td>
      <td><img src="https://img.shields.io/badge/Sequential-Logic_Optimization-orange" /></td>
      <td><img src="https://img.shields.io/badge/Unused_Outputs-Optimization-blue" /></td>
      <td><img src="https://img.shields.io/badge/Tools-Yosys_HandsOn-red" /></td>
    </tr>
  </table>
  <p>🎯 <b>Day 3</b> of the RISC-V SoC Tapeout program focuses on understanding and applying <b>combinational</b> and <b>sequential optimizations</b>. 🏗️  
  Through hands-on labs and case studies, we explore how synthesis tools like <b>Yosys</b> remove redundant logic, optimize timing/area, and prune unused sequential elements. ⚡</p>
</div>

---


## 📚 Index
1. 🛠️ Tools Used (Table) 
2. 🔍 Introduction to Optimizations  
3. 🧮 Combinational Logic Optimizations  
4. 🔁 Sequential Logic Optimizations  
5. 🗑️ Sequential Optimizations for Unused Outputs  
6. ✅ Summary

---

## 1. 🛠️ Tools Used

| Tool      | Description                           | Purpose |
|-----------|---------------------------------------|---------|
| **Icarus Verilog (iverilog)** | Open-source Verilog simulator | Runs RTL + Testbench, generates `.vcd` waveforms |
| **GTKWave** | Waveform viewer | Visualizes `.vcd` waveforms from simulations |
| **Yosys** | Open-source synthesis tool | Performs logic optimizations and netlist generation |
| **Sky130 PDK** | Open-source standard cell library | Provides `.lib` cells for synthesis mapping |

<p align="center">
  <img src="https://img.shields.io/badge/Flow-RTL→Sim→Optimize→Synthesis-yellow" />
</p>

---

## 🔍 2. Introduction to Optimizations

Optimizations in digital design focus on **reducing area, power, and delay** while preserving functionality.  
They can be broadly categorized into **Combinational Logic Optimizations**, **Sequential Logic Optimizations**, and **Boolean Logic Simplifications**.

---

### ⚡ Combinational Logic Optimizations
Combinational logic optimization is the process of **squeezing the logic** to obtain the most efficient design possible.

- 🎯 **Objectives:**
  - Reduce **area** (fewer gates/transistors).  
  - Lower **power consumption**.  
  - Improve **timing (delay paths)**.  

- 🔑 **Techniques:**
  1. **Constant Propagation**  
     - Direct optimization by replacing signals tied to constants (0 or 1).  
     - Example:

       <p align="center">
         <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/Constant_Propagation.png?raw=true" alt="Description of Image" width="600"/>
       </p>

       If `A = 0`, then  
       \[
       Y = $\\overline{C}$
       \]
       → No additional gate needed, It can achievd by only one inverted logic.
       - **Hardware Savings:**  
       - Original: Complex gate structure (≈ 6 MOS transistors).  
       - Simplified: Just an inverter (≈ 2 MOS transistors).  

  2. **Boolean Logic Optimization**
     - Algebraic simplification of Boolean expressions.  
     - Example methods:  
       - **K-Map Reduction** (Karnaugh Maps)  
       - **Quine McCluskey Method** (tabular approach for minimization)

      Boolean optimization focuses on **algebraic simplification** for area savings.

#### Boolean Logic Simplification Example
Ternary operator logic:  

```verilog
assign y = a ? (b ? c (c ? : (c ? a : 0)) : (!c);
```

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/Boolean_logic.png?raw=true" alt="Description of Image" width="600"/>
</p>





---

### 🔁 I. Sequential Logic Optimizations
Sequential optimizations aim at **register-level improvements** for better performance and reduced redundancy.

#### A. Basic Sequential Optimizations
- **Sequential Constant Propagation**  
  - If inputs are constant, flip-flop behavior simplifies.  
  - Example:
 
    
       <p align="center">
         <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/sequential_constant_propagartion.png?raw=true" alt="Description of Image" width="600"/>
       </p>
 

  
    A D Flip-Flop with inputs `RST`, `D`, and `Clk`  
    - If **RST = 1 → Q = 0**  
    - If **D = 0 → Q always = 0**  


- **Set Behavior Example**  
  - With an asynchronous `Set` input, `Q` immediately goes high irrespective of `Clk`.

#### B. Advanced Sequential Optimizations *(Not covered in lab but industry-relevant)*  
1. **State Optimization**  
   - Removes unused states in FSMs to reduce sequential complexity.  

2. **Retiming**  
   - Moving registers across combinational blocks to improve frequency/timing.  
   - Example:
  
     <p align="center">
       <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/Retimign.png?raw=true" alt="Description of Image" width="600"/>
     </p>
  
     
     - Path delay = 5ns → Frequency = 200MHz  
     - After retiming = 4ns → Frequency = 250MHz  

3. **Sequential Logic Cloning (Physical Aware)**  
   - Duplicating drivers to handle **high fan-out nets** or routing constraints.  
   - Example:
  
     <p align="center">
       <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/cloning.png?raw=true" alt="Description of Image" width="600"/>
     </p>
       
     - Signal `A` cloned into two drivers separately driving `B` and `C`.

---

<p align="center">
  <img src="https://img.shields.io/badge/Combinational-Optimization-blueviolet?style=for-the-badge" alt="Combinational Optimization Badge"/>
</p>


---

## 🔹 2) Combinational Logic Optimizations

Combinational logic optimizations focus on **reducing redundant gates, propagating constants, and simplifying Boolean expressions** in the RTL.  
Below are hands-on experiments (`opt_check1` → `opt_check4` and `multiple_module_opt`) demonstrating Yosys optimizations.

---

🖥️ Yosys Commands

```bash
# 1. Start Yosys
yosys

# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 3. Read Verilog design files
read_verilog opt_check.v

# 4. Synthesize the design
synth -top opt_check

# 5. Cleaning the Optimized Design
opt_clean -purge

# 6. Generate gate-level netlist
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 7. Visualize the synthesized design
show 
```

### ⚡ A. opt_check1 – Constant Propagation
In this example, logic with constant inputs is simplified by the synthesis tool.

#### Example Verilog Code
```verilog
module opt_check1 (input a, output y);
  assign y = a?b:0;
endmodule
```
---
#### Gvim Design Code Visualization

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/opt_check.png?raw=true" alt="Description of Image" width="600"/>
  </p>

---
#### Design Visualization

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/opt_check_netlist.png?raw=true" alt="Description of Image" width="600"/>
  </p>

---

### ⚡ B. opt_check2 – Constant Propagation
In this example, logic with constant inputs is simplified by the synthesis tool.

#### Example Verilog Code
```verilog
module opt_check1 (input a, input b, output y);
  assign y = a&1:b; 
endmodule

```
---
#### Design Visualization

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/opt_check2_netlist.png?raw=true" alt="Description of Image" width="600"/>
  </p>

---


### ⚡ C. opt_check3 – Constant Propagation
In this example, logic with constant inputs is simplified by the synthesis tool.

#### Example Verilog Code
```verilog
module opt_check1 (input a, input b, input c, output y);
  assign y = a?(c?b:0):0;
endmodule

```
---
#### Design Visualization

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/opt_check3%5C.png?raw=true" alt="Description of Image" width="600"/>
  </p>

---

### ⚡ D. opt_check4 – Constant Propagation
In this example, logic with constant inputs is simplified by the synthesis tool.

#### Example Verilog Code
```verilog
module opt_check1 (input a, input b, input c, output y);
  assign y = a?(b?(a&c):c):(!c);
endmodule

```
---
#### Design Visualization

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/opt_check4.png?raw=true" alt="Description of Image" width="600"/>
  </p>

---


### ⚡ E. multiple_module_opt – Constant Propagation
In this example, logic with constant inputs is simplified by the synthesis tool.

#### Example Verilog Code
```verilog
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 


endmodule

```
---
#### Design Visualization Multip Module Hierarchical

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/multiple_module_opt.png?raw=true" alt="Description of Image" width="600"/>
  </p>

---

#### Design Visualization Multip Module Flattened

  <p align="center">
    <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day3/Images/multiple_module_opt(flatten).png?raw=true" alt="Description of Image" width="600"/>
  </p>

---



