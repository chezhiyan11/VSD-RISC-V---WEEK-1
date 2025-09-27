# 📘 Week 1 – RTL Design & Synthesis using Open-Source Tools  

Welcome to the **Week 1 Summary** of my RTL Design & Synthesis journey.  
This week covered **simulation, synthesis, verification, and hardware constructs** using tools like **Icarus Verilog, GTKWave, and Yosys**.  

---
Below are the main topics organized **Day-wise**, each with expandable sections containing links to subtopics.  



<details>
  <summary>📘 Day 1 – RTL Design & Simulation Basics</summary>

- [🔹 Introduction to Verilog & Tools](./Day1/Intro.md)  
- [🔹 Writing & Simulating a 2:1 MUX](./Day1/mux2x1.v)  
- [🔹 GTKWave Simulation Results](./Day1/Images/mux_waveform.png)  
- [🔹 Yosys Synthesis Flow](./Day1/yosys_flow.md)  

</details>


<details>
  <summary>📘 Day 2 – Logic Synthesis & Gate-level Simulation</summary>

- [🔹 Synthesis with Yosys](./Day2/synthesis.md)  
- [🔹 Gate-level Simulation (GLS)](./Day2/gls.md)  
- [🔹 Inferred Latch Example](./Day2/inferred_latch.v)  
- [🔹 Observations & Waveforms](./Day2/Images/)  

</details>



<details>
  <summary>📘 Day 3 – Blocking vs Non-blocking, Sensitivity Lists</summary>

- [🔹 Blocking vs Non-blocking Statements](./Day3/blocking_nonblocking.v)  
- [🔹 Sensitivity List Mismatches](./Day3/sensitivity_list.v)  
- [🔹 Labs on Simulation vs Synthesis Mismatch](./Day3/labs.md)  
- [🔹 GTKWave Examples](./Day3/Images/)  

</details>



<details>
  <summary>📘 Day 4 – If-Else & Case Constructs</summary>

- [🔹 Priority Logic with If-Else](./Day4/if_else.v)  
- [🔹 Case Statement Implementation](./Day4/case_mux.v)  
- [🔹 Incomplete If & Case Issues](./Day4/incomplete_if_case.md)  
- [🔹 Lab Examples + Netlists](./Day4/Images/)  

</details>


<details>
  <summary>📘 Day 5 – Optimization in Synthesis</summary>

- [🔹 Incomplete If-Case Labs](./Day5/incomp_if.v)  
- [🔹 Overlapping Case Labs](./Day5/bad_case.v)  
- [🔹 Procedural For Loop](./Day5/mux32x1.v)  
- [🔹 Generate For Loop](./Day5/rca_generate.v)  
- [🔹 Ripple Carry Adder using Generate](./Day5/rca.v)  
- [🔹 Lab Waveforms & Netlists](./Day5/Images/)  
- [🔹 Day 5 Summary](./Day5/summary.md)  

</details>


## 📌 Quick Links  
- 🔗 [Week 1 Summary](./Week1_Summary.md)  
- 🔗 [All Images](./Images/)  
- 🔗 [References](./References.md)  

---

## 📅 Day-wise Highlights  

### 🔹 Day 1 – RTL Basics & Simulation Flow  
- Introduction to **Icarus Verilog** (simulation) and **GTKWave** (waveform visualization).  
- Wrote and simulated a **2:1 Multiplexer** in Verilog.  
- Learned the **RTL → Simulation → Waveform** flow.  

📌 *Tools:*  
| Task | Tool |
|------|------|
| Simulation | **Icarus Verilog (iverilog)** |
| Waveform Visualization | **GTKWave** |

---

### 🔹 Day 2 – Introduction to Synthesis  
- Used **Yosys** for RTL-to-Gate-level synthesis.  
- Explored **RTL Netlist** vs **Synthesized Netlist**.  
- Observed how synthesis optimizes the design.  

📌 *Key Insight:* RTL describes behavior, while **synthesized netlist maps to actual hardware gates**.

---

### 🔹 Day 3 – GLS & Synth-Sim Mismatch  
- Learned **GLS (Gate-Level Simulation)** using Yosys + Icarus.  
- Identified **simulation-synthesis mismatches** caused by bad coding practices.  
- Studied **blocking vs non-blocking** statements.  

⚠️ *Observation:* Mismatches often arise from **sensitivity list issues** or **incorrect blocking statements**.  

---

### 🔹 Day 4 – Caveats in RTL Coding  
- Studied pitfalls in Verilog design like:  
  - **Blocking statement caveats**  
  - **Sensitivity list mismatches**  
  - **Latch inference issues**  
- Performed labs to verify mismatches between **RTL sim and GLS sim**.  

📌 *Learning:* Small coding mistakes → **huge functional mismatches** in synthesized hardware.  

---

### 🔹 Day 5 – Optimization in Synthesis  
- Focused on **If-Case constructs** and their caveats:  
  - Incomplete if/case → inferred latches  
  - Overlapping cases → unpredictable outputs  
  - Partial assignments → latch inference  
- Explored **for loops vs generate loops**:  
  - Procedural `for` → used inside `always` for behavior modeling  
  - `generate for` → used outside `always` for structural replication  

📌 *Example Designs:* MUX (generate), DEMUX (case & generate), Ripple Carry Adder (generate).  

---

## 📊 Important Tables  

### Comparison: If vs Case  
| Feature | `if-else` | `case` |
|---------|-----------|---------|
| Synthesized As | Priority Logic (MUX chain) | Multiplexer |
| Strength | Handles priority | Cleaner, scalable |
| Caveat | Missing else → latch | Overlapping items → undefined |

---

### Comparison: Procedural vs Generate Loops  
| Feature | Procedural `for` Loop | `generate for` Loop |
|---------|------------------------|----------------------|
| Location | Inside `always` | Outside `always` |
| Function | Evaluating expressions | Instantiating hardware |
| Concept | Describes behavior | Describes structure |

---

## 📈 Flow Diagram  

```plaintext
 Verilog Code (RTL) 
        │
        ▼
  Simulation (Icarus) ──▶ Waveform (GTKWave)
        │
        ▼
  Synthesis (Yosys) ──▶ Netlist
        │
        ▼
 Gate-Level Simulation (GLS)
```

---
## ✅ Week 1 – Final Summary  

- 📝 Learned the **complete flow**: RTL → Simulation → Synthesis → GLS.  
- 🔍 Explored **pitfalls in Verilog coding**: incomplete if/case, overlapping case, blocking vs non-blocking, sensitivity list mismatches.  
- 🧪 Hands-on **labs**: MUX, DEMUX, Ripple Carry Adder implemented & simulated.  
- 🔀 Understood difference between **behavioral modeling** (`if`, procedural loops) and **structural modeling** (`generate for`).  
- 🚀 Built confidence in using **open-source tools** (Icarus, GTKWave, Yosys).  
- ✨ **Big Takeaway:** Correct RTL coding style is as important as functionality — poor coding = incorrect hardware!

---
## 📚 References  

1. [Icarus Verilog](http://iverilog.icarus.com/) – Simulation.  
2. [GTKWave](http://gtkwave.sourceforge.net/) – Waveform visualization.  
3. [Yosys HQ](https://yosyshq.net/yosys/) – Synthesis & optimization.  
4. [SkyWater Sky130 PDK](https://skywater-pdk.readthedocs.io/) – Cell libraries.  
5. Sudip Misra NPTEL Course – *Industry 4.0 and IIoT (Verilog Synthesis Part)*.

---
---

## Acknowledgments  

I would like to sincerely acknowledge and thank:  

- **Mr. Kunal Ghosh (Founder, VSD – VLSI System Design)**  
  For his exceptional mentorship, guidance, and vision in enabling students to learn *RTL design, synthesis, and open-source VLSI flows*.  

- **VSD Team**  
  For curating the workshop content and providing continuous learning resources.  


- **Open-Source EDA Community**  
  For making powerful tools freely available, including:  
  - 🖥️ [Icarus Verilog](http://iverilog.icarus.com/)  
  - 📊 [GTKWave](http://gtkwave.sourceforge.net/)  
  - ⚙️ [Yosys](https://yosyshq.net/yosys/)  

- **Faculty & Peers from the VLSI Department**  
  For their constant encouragement, support, and collaboration during this learning journey.  

---

✨ This repository is dedicated to the spirit of **open-source hardware education** and the belief that *accessible knowledge can empower innovation in VLSI design*.  

