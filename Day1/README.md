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

![Simulator Flow](https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day1/images/simulator_working.png?raw=true)


