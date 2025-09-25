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

## 🛠️ 1) Tools Used

| Tool      | Description | Purpose |
|-----------|-------------|---------|
| **Icarus Verilog (iverilog)** | Open-source Verilog simulator | Runs RTL & GLS testbenches, generates `.vcd` |
| **GTKWave** | Waveform viewer | Visualize simulation outputs |
| **Yosys** | Open-source synthesis tool | Generates gate-level netlists, runs optimizations |
| **Sky130 PDK** | Standard cell library | Provides functional and timing-aware netlists |

<p align="center">
  <img src="https://img.shields.io/badge/Flow-RTL→Synthesis→GLS→Waveforms-purple" />
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
