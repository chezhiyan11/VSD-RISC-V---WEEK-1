
### I. Introduction to Simulation using iverilog and gtkwave

Day 1 began with the introduction of the open-source simulator **iverilog**.

1.  **Verification and Test Bench (TB):**
    *   The primary goal of simulation is **verification**, where the RTL design is checked for adherence to the required specifications.
    *   The design's intended functionality is defined by the actual Verilog code.
    *   A **Test Bench (TB)** is used to determine if the design meets the specifications by applying a stimulus (test vectors).
    *   The TB typically includes a stimulus generator and a stimulus observer but has **no primary inputs or primary outputs**.
    *   The simulator works by observing changes in the input values (e.g., from `0011` to `1100`) and then evaluating and outputting the corresponding result.

2.  **Simulation Flow:**
    *   The **design** (Verilog) is simulated by the **TB**.
    *   During simulation, a **Vcd file** is created.
    *   The waveform viewer, **gtkwave**, is used to visualize the contents of the Vcd file.
    *   Associated labs covered the introduction to the `iverilog` design test bench and using `iverilog` and `gtkwave` for viewing waveforms.

### II. Logic Synthesis and Yosys

The second major topic covered was the Introduction to **Yosys and Logic synthesis**.

1.  **RTL and Synthesis:**
    *   **RTL Design** is the behavioral representation that meets the required specification.
    *   **Synthesis** (using tools like Yosys) is the process of translating the RTL into a lower-level representation.
    *   The design is converted into **gates**, and the connections between these gates are made.

2.  **Netlist Creation:**
    *   The output of the synthesis process is a file called the **Netlist**.
    *   A Netlist represents the collection of logical modules (e.g., AND, OR) that make up the circuit.

3.  **Synthesis Verification:**
    *   To verify the synthesis, the resulting **Netlist** is simulated using `iverilog` and the results are viewed in `gtkwave`. The behavior observed during the RTL simulation should match the behavior of the synthesized Netlist.

4.  **Yosys Usage:**
    *   Yosys commands are used to perform synthesis, including: reading the Verilog design (`read_verilog`), reading standard cell libraries (`read-liberty`), running the synthesis step (`synth -top [module name]`), and writing the output Netlist file (`write_verilog`).

### III. Cell Characteristics, Delay, and Constraints

Synthesis relies on libraries of standard cells, which introduce concepts of speed, delay, power, and area trade-offs.

1.  **Standard Cells and Delay:**
    *   A library contains a collection of logical modules, offering different "flavors" (variations) of the same gate (e.g., fast, medium, or slow 2-input AND gates).
    *   The **combinational delay** in the logic path is crucial as it determines the **maximum speed of operation** of the digital logic circuit.

2.  **Fast vs. Slow Cells:**
    *   **Fast cells** achieve better performance but incur a **penalty of area and power**. They are often required to meet "setup" timing requirements.
    *   Fast cells utilize **wide transistors**, which source more current, leading to lower resistance. This results in faster charging and discharging of capacitance and thus less cell delay. Wide transistors require high power and area.
    *   **Slow cells** are required to meet "hold" timing requirements, ensuring that no hold issues occur.
    *   Slow cells utilize **narrow transistors**, which results in more delay but consumes less area and less power.

3.  **Constraints:**
    *   **Constraints** are guidance given to the synthesizer to help it select the appropriate cells (fast or slow) necessary to meet the required timing and performance specifications.

### IV. Labs using Yosys and Sky130 PDKs

The program included labs utilizing **Yosys and Sky130 PDKs (Process Design Kits)**.

*   These labs focused on using Yosys with standard cell libraries derived from the Sky130 technology.
*   Specific lab modules included parts of "Lab3 Yosys 1 good mux". The Sky130 PDKs include the standard cell library (`sky130_fd_sc_hd-tl-v0.6.lib`) which is read by Yosys during the synthesis flow.

## 🎯 Key Takeaways
   1) Simulation (iverilog + GTKWave): Verify RTL behavior.
   2) Synthesis (Yosys): Convert RTL → Netlist.
   3) Verification after Synthesis: Ensure RTL = Gate-level design.
   4) Cell Libraries: Multiple flavors balance speed, power, and area.
   5) Labs: Practical exposure with Sky130 PDKs.
