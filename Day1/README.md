<div align="center">

![Synthesis](https://img.shields.io/badge/Yosys-Synthesis-green?style=for-the-badge&logo=opensourceinitiative)
![Icarus](https://img.shields.io/badge/Icarus-Verilog-orange?style=for-the-badge)
![GTKWave](https://img.shields.io/badge/GTKWave-Simulation-lightgrey?style=for-the-badge)

</div>

# Day 1 — RTL Design and Synthesis (Introduction)

## Objective
- Understand simulation flow with Icarus Verilog (iverilog).
- Write and simulate a 2:1 multiplexer.
- Learn the basics of Yosys synthesis.

## Tools Used

| Category      | Tool     | Purpose                                   |
|---------------|----------|-------------------------------------------|
| Simulator     | Icarus Verilog (iverilog) | Compile and run RTL + testbench simulation |
| Verification  | GTKWave  | Waveform viewer for analyzing VCD output  |
| Synthesizer   | Yosys    | Convert RTL design into a gate-level netlist |


<details>
  <summary>📌 Click here to view Day1 Mind map</summary>

  ![Day 1 Mind Map](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/Day1_mind_map.png?raw=true)

  *(This mind map summarizes the concept and lab modules of Day 1 of RTL design and synthesis.)*
</details>

## Open source Simulator iverilog
  RTL Desig is checked for aherence to the specification by simulating the design

### Design
Design is the actual Verilog code for set of Verilog codes which has the intended functionality to meet with the required specifications.

![Design block](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/Design.png?raw=true)

### Testbench
Testbench checks whether the Design obeys the required specifiaction of not by applying stimulus or test vector.

![Testbench block](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/Testbench.png?raw=true)

### iverilog based simulation flow
1. Iverilog simulates the design using a test bench, producing a Vcd file for GTKWave visualization.
2. Yosys handles logic synthesis, converting RTL to a Netlist, which Iverilog also verifies.
introduction

![Simulator Flow](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/simulator_working.png?raw=true)


## LAB
### 1. Git Cloning
<pre>
<code>
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
</code>
</pre>

![Clone_Lab_Reposiraty](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/clone_reposiraty.png?raw=truegood_shift_reg.v)

### 2. Loading the Design From Verilog_files to iverilog
<pre>
<code>
iverilog good_mux.v tb_good_mux.v
</code>
</pre>

![Compile the Design and Testbench](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/loading_design%20and%20tb%20in%20iverilog.png?raw=true)

### 3. Run Simulation & Waveform
<pre>
<code>https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/gvim_editor.png?raw=true
./a.out
gtkwave tb_good_mux.vcd
</code>
</pre>

![Synthesis Run](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/gtkwave%20analysis.png?raw=true)

![gtkwave interface](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/gtkwave%20interface.png?raw=true)


### 4. Viewing Design and TB file in gvim
<pre>
<code>
gvim tb_good_mux.v -o good_mux.v
</code>
</pre>

![gvim_editor](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/gvim_editor.png?raw=true)


## Synthesizer 
1) The Synthesizer is the tool used for converting the RTL to the netlist
2) Yosys is the Synthesizer used to convert the RTL to netlist

### Yosys
Yosys is an open-source logic synthesis tool widely used in digital hardware design. It reads Verilog code and produces a gate-level netlist, which serves as the blueprint for hardware implementation.

![Synthesizer](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/Synthesizer.png?raw=true)

### Key Capabilities of Yosys
  1) Synthesis → Translates HDL (Verilog) into logic circuits.
  2) Optimization → Reduces area or improves speed.
  3) Technology Mapping → Maps logic onto standard cells from a library.
  4) Extensibility → Allows integration of custom flows.

## Verifying the Synthesis

Verification in a Synthesis Flow
After synthesis, verification ensures that the gate-level design is functionally equivalent to the original RTL. Typical verification steps include:
  1) Equivalence Checking → Confirms RTL vs. synthesized netlist behavior.
  2) Simulation → Runs testbenches on gate-level models.
  3) Static Timing Analysis (STA) → Validates timing closure across all paths.

![Verifying the Synthesis](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/Verify%20the%20synthesis.png?raw=true)


## Gate Library Variants
A technology library (.lib) provides multiple versions of standard logic gates (AND, OR, NOT, etc.), each optimized for different design goals:
  1) Performance → High-speed cells for timing-critical paths, slower ones for low-power regions.
  2) Power → Energy-efficient cells to minimize consumption.
  3) Area → Compact cells for space-constrained designs.
  4) Drive Strength → Stronger gates to handle larger loads.
  5) Signal Integrity → Special cells for noise and reliability.
  6) During technology mapping, the synthesis tool selects the most suitable variant to balance speed, power, and area based on design constraints.

## LAB
Synthesizing the Design of good_mux [2:1 Multiplexer]

### 1. Invoke Yosys
<pre>
<code>
yosys
</code>
</pre>

![Invloking yosys](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/Invoke%20yosys.png?raw=true)

### 2. Read the Library
<pre>
<code>
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
</code>
</pre>

### 3. Read the Verilog Code
<pre>
<code>
read_verilog good_mux.v
</code>
</pre>

![Read the verilog code](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/read%20and%20write.png?raw=true)


### 4. Synthesize the Design 
<pre>
<code>
synth -top good_mux
</code>
</pre>

![synthesize the code](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/synthesize%20the%20design.png?raw=true)


### 5. Generating Netlist
<pre>
<code>
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
</code>
</pre>

![Generating Netlist](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/netlist%20generation.png?raw=true)

### 6. Logic Visualization
<pre>
<code>
show
</code>
</pre>

![Visualize design](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/logic%20visualization.png?raw=true)





