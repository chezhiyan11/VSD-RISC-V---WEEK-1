<div align="center">
  <h1>🔎 Day 5 – Optimization in Synthesis</h1>
</div>

<div align="center">
  <table>
    <tr>
      <td><img src="https://img.shields.io/badge/Week1-Day5-darkgreen" /></td>
      <td><img src="https://img.shields.io/badge/Optimization-Synthesis-blue" /></td>
      <td><img src="https://img.shields.io/badge/Verilog-if_case_constructs-orange" /></td>
      <td><img src="https://img.shields.io/badge/Loops-for_and_generate-yellow" /></td>
      <td><img src="https://img.shields.io/badge/Tools-Icarus_Yosys_GTKWave-red" /></td>
    </tr>
  </table>
  <p>📘 <b>Day 5</b> explores <b>optimization in synthesis</b> by understanding how Verilog constructs like 
  <b>if-case</b>, <b>for loops</b>, and <b>generate</b> blocks are handled by the synthesis tool.  
  We also perform labs to see mismatches and optimizations for <b>incomplete if-case</b> and <b>overlapping case</b> conditions. ⚡</p>
</div>

---

## 📑 Index
1. 🛠️ Tools Used  
2. 🔀 If-Case Constructs  
3. 🧪 Labs on Incomplete If-Case  
4. 🧪 Labs on Incomplete Overlapping Case  
5. 🔁 For Loop & For Generate  
6. 🧪 Labs on For Loop & For Generate  
7. ✅ Summary  

---
<p align="center">
  <img src="https://img.shields.io/badge/Flow-RTL→Synthesis→Optimization-purple" />
</p>

---

## 🛠️ 1) Tools Used

| Tool      | Description | Purpose |
|-----------|-------------|---------|
| **Icarus Verilog (iverilog)** | Verilog simulator | Runs testbenches for RTL & GLS |
| **GTKWave** | Waveform viewer | Visualizes outputs |
| **Yosys** | Open-source synthesis tool | Performs logic optimization, removes redundancies |
| **Sky130 PDK** | Standard cell library | Provides cells for synthesis |

---

---
<p align="center">
  <img src="https://img.shields.io/badge/🔀%20If--Case%20Constructs-blue?style=for-the-badge" />
</p>

## 2) 🔀 If-Case Constructs  

The `if` and `case` statements are two important constructs in Verilog for **decision-making logic**. They map directly to hardware during synthesis, but incorrect usage may cause **inferred latches** and unintended behavior.

---

### 🔹 A) The `if` Statement in Hardware Design  

#### 1. Implementation as Priority Logic  
- An `if-else if-else` chain synthesizes into **priority logic**.  
- In hardware, this becomes a **chain of multiplexers**.  
- The first condition that evaluates `true` determines the output.  

**Execution Flow:**  
- `<cond1>` has the **highest priority**.  
- If `<cond1>` is false, `<cond2>` is checked, and so on.  

#### 2. Dangers and Cautions – Inferred Latches  
- An **incomplete if** (missing a final `else`) can create **inferred latches** in **combinational logic**.  
- **Cause:** The synthesis tool inserts a latch to hold the previous value when no condition matches.  
- **Bad Practice:** Using incomplete `if` statements in combinational logic.  

#### 3. Incomplete `if` in Sequential Logic (Valid Usage)  
- In **sequential logic**, incomplete `if` statements are often intentional.  
- Example: A counter uses:  
  ```verilog
  always @(posedge clk or posedge rst) begin
    if (rst)
      count <= 0;
    else if (en)
      count <= count + 1;
    // No final else: holds previous value
  end


---

<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20Incomplete%20If--Case-orange?style=for-the-badge" />
</p>

## 3) 🧪 Labs on Incomplete If-Case  

✍️ *[Add lab steps, simulation screenshots, and mismatch observations here]*  

---

<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20Overlapping%20Case-red?style=for-the-badge" />
</p>

## 4) 🧪 Labs on Incomplete Overlapping Case  

✍️ *[Add RTL simulation, GLS, netlist visualization + notes here]*  

---

<p align="center">
  <img src="https://img.shields.io/badge/🔁%20For%20Loop%20&%20For%20Generate-yellow?style=for-the-badge" />
</p>

## 5) 🔁 For Loop & For Generate  

✍️ *[Add lecture notes here]*  
- Difference between **behavioral for loop** and **generate-for loop**.  
- How Yosys expands loops into hardware.  
- Optimization by removing redundant iterations.  

---

<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20For%20Loop%20&%20Generate-green?style=for-the-badge" />
</p>

## 6) 🧪 Labs on For Loop & For Generate  

✍️ *[Add waveform screenshots + observations here]*  

---

<p align="center">
  <img src="https://img.shields.io/badge/Day5%20|%20Summary-darkgreen?style=for-the-badge" />
</p>

## ✅ Summary  

- Learned how **if-case constructs** are synthesized and optimized.  
- Observed **mismatches in incomplete/overlapping case statements**.  
- Understood difference between **`for` loop** (simulation construct) and **`generate` loop** (synthesis unrolling).  
- Labs demonstrated how synthesis tools **remove redundancies and optimize logic**.  

---

## 📚 References  

1. [Icarus Verilog](http://iverilog.icarus.com/) – Simulation.  
2. [GTKWave](http://gtkwave.sourceforge.net/) – Waveform visualization.  
3. [Yosys HQ](https://yosyshq.net/yosys/) – Synthesis & optimization.  
4. [SkyWater Sky130 PDK](https://skywater-pdk.readthedocs.io/) – Cell libraries.  
5. Sudip Misra NPTEL Course – *Industry 4.0 and IIoT (Verilog Synthesis Part)*.  

---
