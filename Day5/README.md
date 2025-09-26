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
    ```verilog
    begin
      if <cond_1>
         // C1
      else if <cond_2>
         // C2
      else if <cond_3>
         // C3
      else
         // E
     end
    ```

---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="If_else_hardware" width="700"/>
</p>  
---
  

**Execution Flow:**  
- `<cond1>` has the **highest priority**.  
- If `<cond1>` is false, `<cond2>` is checked, and so on.  

#### 2. Dangers and Cautions – Inferred Latches  
- An **incomplete if** (missing a final `else`) can create **inferred latches** in **combinational logic**.  
- **Cause:** The synthesis tool inserts a latch to hold the previous value when no condition matches.  
- **Bad Practice:** Using incomplete `if` statements in combinational logic.
---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="Danger with if" width="700"/>
</p>  
---
---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="Infered_latch" width="700"/>
</p>  
---

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
  ```

---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="Valid_Infered_latch" width="700"/>
</p>  
---

### 🔹 B) The `case` Statement in Hardware Design  

#### 1. Basic Implementation  
- A `case` statement selects between multiple options based on a **selector signal (`sel`)**.  
- Synthesizes into a **multiplexer**, driven by `sel`.  
- Typically easier to read/write compared to long `if-else` chains.

##### Example MUX in Case statement
```verilog
// 4x1 Multiplexer using case statement
module mux4x1_case (
    input  [1:0] sel,      // 2-bit select line
    input  C1, C2, C3, C4,     // 4 data inputs
    output reg y           // output
);

always @(*) begin
    case (sel)
        2'b00: y = C1;
        2'b01: y = C2;
        2'b10: y = C3;
        2'b11: y = C4;
        default: y = 0;   // safe default to avoid latch inference
    endcase
end

endmodule
```
---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="Mux_Case" width="700"/>
</p>  
---

---

#### 2. Caveats and Solutions  

**⚠️ Incomplete Case**  
- If all possible values of `sel` are **not covered**, synthesis infers **latches**.  
- ✅ **Fix:** Always include a `default` case.

**Example Incomplete Case**
```verilog
case (sel)
  2'b00: y = C1;
  2'b01: y = C2;
endcase
```

---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="latched_mux" width="700"/>
</p>  
---
**To avoid infered latch**

```verilog
case (sel)
  2'b00: y = C1;
  2'b01: y = C2;
  2'b10: y = C3;
  default: y = 0;  // prevents latch
endcase
```
---
**⚠️ Partial Assignments**
  - If not all outputs are assigned in each branch, latches may be inferred.
  - ✅ **Fix**: Assign all outputs in all case branches.

**Example Partial Assignment**
```verilog
case (sel)
  2'b00: begin
    x = a;
    y = b;
  end
  2'b01: begin
    x = c;
  end
  default: begin
    x = 0;
    y = 0;
  end
endcase
```
---
<p align="center">
  <img src="images/day4/blocking_caveat_rtl.png" alt="missing_assignment" width="700"/>
</p>  
---



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
