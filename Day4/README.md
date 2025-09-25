<div align="center">
  <h1>🔎 Day 4 – GLS, Synthesis-Simulation Mismatch & Blocking/Non-Blocking Statements</h1>
</div>

<div align="center">
  <table>
    <tr>
      <td><img src="https://img.shields.io/badge/Week1-Day4-darkgreen" /></td>
      <td><img src="https://img.shields.io/badge/GLS-Gate_Level_Simulation-blue" /></td>
      <td><img src="https://img.shields.io/badge/Mismatch-Synth_vs_Sim-red" /></td>
      <td><img src="https://img.shields.io/badge/Verilog-Blocking_vs_NonBlocking-orange" /></td>
      <td><img src="https://img.shields.io/badge/Tools-Icarus_Yosys_GTKWave-yellow" /></td>
    </tr>
  </table>
  <p>📘 <b>Day 4</b> covers <b>Gate Level Simulation (GLS)</b>, identifying <b>Synthesis-Simulation mismatches</b>, and exploring the crucial difference between <b>Blocking (=)</b> and <b>Non-Blocking (<=)</b> statements in Verilog.  
  Through concepts and labs, we learn why simulation may diverge from synthesis and how to avoid common pitfalls. ⚡</p>
</div>

---

## 📑 Index
1. 🛠️ Tools Used  
2. 🔗 GLS Concepts & Flow using Icarus Verilog  
3. ⚠️ Synthesis-Simulation Mismatch  
4. 🔀 Blocking vs Non-Blocking Statements  
5. ⚡ Caveats with Blocking Statements  
6. 🧪 Labs on GLS & Synth-Sim Mismatch  
7. 🧪 Labs on Blocking Statement Mismatch  
8. ✅ Summary  

---
<p align="center">
  <img src="https://img.shields.io/badge/Flow-RTL→Synthesis→GLS→Waveforms-purple" />
</p>

---

## 🛠️ 1) Tools Used

| Tool      | Description | Purpose |
|-----------|-------------|---------|
| **Icarus Verilog (iverilog)** | Open-source Verilog simulator | Runs RTL & GLS testbenches, generates `.vcd` |
| **GTKWave** | Waveform viewer | Visualize simulation outputs |
| **Yosys** | Open-source synthesis tool | Generates gate-level netlists, runs optimizations |
| **Sky130 PDK** | Standard cell library | Provides functional and timing-aware netlists |



---
<p align="center">
  <img src="https://img.shields.io/badge/🔗%20GLS%20Concepts%20&%20Flow-icarus--verilog-blue?style=for-the-badge" />
</p>

---

## 🔗 2) GLS Concepts & Flow

**GLS** is the process of simulating the **gate-level netlist** after synthesis.  

🔹 **Why GLS?**  
- Ensures RTL functionality is preserved post-synthesis.  
- Validates **timing** (with delays/SDF).  
- Confirms **DFT/test logic** like scan chains.  

🔹 **When to run GLS?**  
- After synthesis (RTL → netlist).  
- Before physical design (catch bugs early).  

🔹 **Types:**  
- *Functional GLS* → unit/zero-delay simulation.  
- *Timing GLS* → with SDF timing back-annotation.

  <p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/GLS_Iverilog_block.png?raw=true" alt="description" width="800"/>
</p>



---
<p align="center">
  <img src="https://img.shields.io/badge/⚠️%20Synthesis--Simulation%20Mismatch-critical?style=for-the-badge" />
</p>
---

## ⚠️ 3) Synthesis-Simulation Mismatch

Occurs when RTL simulation ≠ Netlist simulation.  

**Causes of Mismatch:**
1. ❌ Missing **sensitivity list**  
   - `always @(sel)` → wrong behavior  
   - ✅ Fix: `always @(*)`  
2. ❌ Improper **Blocking vs Non-Blocking usage**  
3. ❌ Non-standard Verilog coding practices  

👉 Example: Missing sensitivity list in a **MUX** may work in RTL but fail in GLS.

---
<p align="center">
  <img src="https://img.shields.io/badge/🔀%20Blocking%20vs%20Non--Blocking%20Statements-orange?style=for-the-badge" />
</p>
---

## 🔀 4) Blocking vs Non-Blocking Statements

| Type | Symbol | Execution | Usage |
|------|--------|-----------|-------|
| **Blocking** | `=` | Executes sequentially (line by line) | Prefer for **combinational** logic |
| **Non-Blocking** | `<=` | Evaluates RHS in parallel, updates LHS after | Must use for **sequential** logic (FFs) |

**Example:**  
```verilog
// Blocking
q0 = d;
q  = q0;   // Uses updated value immediately

// Non-Blocking
q0 <= d;
q  <= q0;  // Updates together on clk edge
```

---
<p align="center">
  <img src="https://img.shields.io/badge/⚡%20Caveats%20with%20Blocking%20Statements-yellow?style=for-the-badge" />
</p>

---

## ⚡ 5) Caveats with Blocking Statements  

Blocking assignments (`=`) in Verilog are executed **immediately in sequential order** within a procedural block. While they are useful for combinational logic, their misuse inside sequential logic (`always @(posedge clk)`) can lead to **simulation vs synthesis mismatches**.  

### ⚠️ Why is it a Caveat?  

- In **RTL Simulation**, statements are executed line by line.  
- In **hardware (synthesis)**, logic executes in **parallel**, not in order.  
- This difference can cause **incorrect behavior** when blocking assignments are used improperly.  

---

### 🔹 Example: Problematic Blocking Code  

```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @(*) begin
    d = x & c;   // uses "old" value of x
    x = a | b;   // updates x afterwards
  end
endmodule
```
---
<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20Blocking%20Statement%20Mismatch-lightgrey?style=for-the-badge" />
</p>

---

## 6) 🧪 Labs on GLS & Synth-Sim Mismatch

We perform **RTL Simulation**, **Netlist Visualization**, and **GLS Simulation** for two digital designs:  

### A) Ternary Operator MUX  

- **RTL Simulation**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/gtkwave_ternary_op_mux.png?raw=true" alt="RTL Simulation - Ternary MUX" width="600"/>
</p>  

- **Netlist Visualization**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/ternary_op_mux_netlist.png?raw=true" alt="Netlist - Ternary MUX" width="600"/>
</p>  

- **GLS Simulation**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/gls_ternary_op.png?raw=true" alt="GLS Simulation - Ternary MUX" width="600"/>
</p>  

---

### B) Bad MUX (with sensitivity list issue)  

- **RTL Simulation**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/gtkwave_bad_mux.png?raw=true" alt="RTL Simulation - Bad MUX" width="600"/>
</p>  

- **Netlist Visualization**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/bad_mux_netlist.png?raw=true" alt="Netlist - Bad MUX" width="600"/>
</p>  

- **GLS Simulation**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/gls_bad_mux.png?raw=true" alt="GLS Simulation - Bad MUX" width="600"/>
</p>  

⚠️ **Observation:**  
- The `bad_mux` design shows a **mismatch** between **RTL simulation** and **GLS simulation**.  
- Cause: **Missing sensitivity list** + incorrect **blocking assignment (`<=`) in combinational logic**.  
- Correction: Use `always @(*)` with **blocking assignments (`=`)** for combinational logic.  

---
<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20Blocking%20Statement%20Mismatch-lightgrey?style=for-the-badge" />
</p>
---

## 7) 🧪 Labs on Blocking Statement Mismatch 

We now analyze the impact of **blocking statements** in sequential logic.  

### A) Blocking Caveat Design  

- **RTL Simulation**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/gtkwave_blocking_caveat.png?raw=true" alt="RTL Simulation - Blocking Caveat" width="600"/>
</p>  

- **Netlist Visualization**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/netlist_blocking_caveat.png?raw=true" alt="Netlist - Blocking Caveat" width="600"/>
</p>  

- **GLS Simulation**  
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day4/Images/gls_blocking_caveat.png?raw=true" alt="GLS Simulation - Blocking Caveat" width="600"/>
</p>  

⚠️ **Observation:**  
- There is a **clear mismatch** between the **RTL simulation** and **GLS simulation**.  
- Reason: Improper use of **blocking assignments (`=`)** inside sequential logic leads to order-dependent behavior in simulation, but synthesis interprets the hardware as parallel.  

✅ **Fix:** Use **non-blocking assignments (`<=`)** inside `always @(posedge clk)` for sequential logic to avoid mismatch.  

---

