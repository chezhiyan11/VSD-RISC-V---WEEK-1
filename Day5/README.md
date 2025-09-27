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



## 🛠️ 1) Tools Used

| Tool      | Description | Purpose |
|-----------|-------------|---------|
| **Icarus Verilog (iverilog)** | Verilog simulator | Runs testbenches for RTL & GLS |
| **GTKWave** | Waveform viewer | Visualizes outputs |
| **Yosys** | Open-source synthesis tool | Performs logic optimization, removes redundancies |
| **Sky130 PDK** | Standard cell library | Provides cells for synthesis |



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


<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/if_else_hardware.png?raw=true" alt="If_else_hardware" width="700"/>
</p>  

  

**Execution Flow:**  
- `<cond1>` has the **highest priority**.  
- If `<cond1>` is false, `<cond2>` is checked, and so on.
---

#### 2. Dangers and Cautions – Inferred Latches  
- An **incomplete if** (missing a final `else`) can create **inferred latches** in **combinational logic**.  
- **Cause:** The synthesis tool inserts a latch to hold the previous value when no condition matches.  
- **Bad Practice:** Using incomplete `if` statements in combinational logic.

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/danger_with_if.png?raw=true" alt="Danger with if" width="700"/>
</p>  

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/Infered_latch.png?raw=true" alt="Infered_latch" width="700"/>
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

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/Valid_infered_latch.png?raw=true" alt="Valid_Infered_latch" width="700"/>
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

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/mux_case.png?raw=true" alt="Mux_Case" width="700"/>
</p>  

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

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/latched_mux.png?raw=true" alt="latched_mux" width="700"/>
</p>  


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
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/partial_ass.png?raw=true" alt="missing_assignment" width="700"/>
</p>  

---
### ⚠️ Caveat 3: Overlapping Cases  

#### 🔹 Comparison with `if-else if`
- An **`if-else if-else`** structure operates as **priority logic**.  
- If conditions overlap, only the first true condition (highest priority) will execute → predictable outcome.  

#### 🔹 Behavior of `case`
- A `case` statement is generally synthesized as a **multiplexer**, where the selector value directly chooses one input.  
- Unlike `if-else`, a `case` statement does **not** have built-in priority between overlapping items.  

#### 🔹 Example of Overlap
If a selector value appears **more than once**, ambiguity arises:  

```verilog
always @(*) begin
    case (sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        2'b10: y = d;  // ❌ Overlap: ambiguous
        default: y = 0;
    endcase
end
```
---
<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20Incomplete%20If--Case-orange?style=for-the-badge" />
</p>


## 3) 🧪 Labs on Incomplete If-Case  

### ⛏️ Commands — simulate (Icarus), view (GTKWave) and synthesize (Yosys)

### A — incom_if (Incomplete if → inferred latch)

#### Design Code:
```bash
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```

**RTL simulation with Icarus Verilog**
```bash
# compile RTL + testbench into an executable
iverilog incomp_if.v tb_incomp_if.v
./a.out
gtkwave tb_incom_if.vcd
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/gtk_ncomp_if.png?raw=true" alt="gtk_incomp_if" width="700"/>
</p>  



**Yosys [Simulator]**
```inside yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if.v
synth -top incomp_if
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/dlatch_infered%20in%20mux.png?raw=true" alt="Dlatch in Mux" width="700"/>
</p>  

- Here it is clearly visible that Dlatch is infered in the Mux.

<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/incomp_if_netlist.png?raw=true" alt="incomp_if_netlist" width="700"/>
</p>  

---
#### B — incom_if2 (Incomplete if2 → inferred latch)

#### Design Code:
```bash
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;

end
endmodule
```

**RTL simulation with Icarus Verilog**
```bash
# compile RTL + testbench into an executable
iverilog incomp_if2.v tb_incomp_if2.v
./a.out
gtkwave tb_incomp_if2.vcd
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/gtk_incomp_if2.png?raw=true" alt="gtk_incomp_if" width="700"/>
</p>  


**Yosys [Simulator]**
```inside yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if2.v
synth -top incomp_if2
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/incomp_if2_netlist.png?raw=true" alt="incomp_if_netlist" width="700"/>
</p>  

---
<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20Overlapping%20Case-red?style=for-the-badge" />
</p>

## 4) 🧪 Labs on Incomplete Overlapping Case  

#### A — incomp_case [Incomplete Case]

#### Design Code:
```bash
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```

**RTL simulation with Icarus Verilog**
```bash
# compile RTL + testbench into an executable
iverilog incomp_case.v tb_incomp_case.v
./a.out
gtkwave tb_incom_case.vcd
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/gtk_incomp_case.png?raw=true" alt="gtk_incomp_case" width="700"/>
</p>  


**Yosys [Simulator]**
```inside yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_case.v
synth -top incomp_case
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/incomp_case_netlist.png?raw=true" alt="incomp_case_netlist" width="700"/>
</p>  

---
#### B — comp_case [Complete Case]

#### Design Code:
```bash
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule
```

**RTL simulation with Icarus Verilog**
```bash
# compile RTL + testbench into an executable
iverilog comp_case.v tb_comp_case.v
./a.out
gtkwave tb_comp_case.vcd
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/gtk_comp_case.png?raw=true" alt="gtk_comp_case" width="700"/>
</p>  


**Yosys [Simulator]**
```inside yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog comp_case.v
synth -top comp_case
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/comp_case_netlist.png?raw=true" alt="comp_case_netlist" width="700"/>
</p> 
---

#### C — bad_case [Bad Case]

#### Design Code:
```bash
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
		//2'b11: y = i3;
	endcase
end

endmodule
```

**RTL simulation with Icarus Verilog**
```bash
# compile RTL + testbench into an executable
iverilog bad_case.v tb_bad_case.v
./a.out
gtkwave tb_bad_case.vcd
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/gtk_bad_case.png?raw=true" alt="gtk_bad_case" width="700"/>
</p>  


**Yosys [Simulator]**
```inside yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_case.v
synth -top bad_case
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/bad_case_netlist.png?raw=true" alt="bad_case_netlist" width="700"/>
</p> 

---

#### D — partial_case_assign [Partial Case Assignmentgedit ]

#### Design Code:
```bash
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			   y = i2;
			  end
	endcase
end
endmodule
```


**Yosys [Simulator]**
```inside yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog partial_case_assign.v
synth -top partial_case_assign
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center">
  <img src="https://github.com/chezhiyan11/VSD-RISC-V---WEEK-1/blob/main/Day5/Images/partial_case_assign_netlist.png?raw=true" alt="partial_case_assign" width="700"/>
</p> 

---

<p align="center">
  <img src="https://img.shields.io/badge/🔁%20For%20Loop%20&%20For%20Generate-yellow?style=for-the-badge" />
</p>

## 5) 🔁 For Loop & For Generate  

In hardware design, **looping constructs** play a key role in describing both **behavior** and **structure**.  
There are two main types:

---

### 1️⃣ Procedural `for` Loop (Inside `always` Blocks)

👉 Used **inside `always` blocks**.  
👉 Describes **behavioral logic evaluation** (not hardware instantiation).  
👉 Great for **MUX/DEMUX logic** or **iterative assignments**.

#### ✅ Example 1: 32×1 Multiplexer (using `for`)
```verilog
module mux32x1 (
    input  wire [31:0] inp,
    input  wire [4:0]  sel,
    output reg         Y
);
integer i;
always @(*) begin
    Y = 0;
    for (i = 0; i < 32; i = i + 1) begin
        if (sel == i)
            Y = inp[i];
    end
end
endmodule
```
### 2️⃣ `generate for` Loop (Structural Replication)

👉 Used **outside `always` blocks**.  
👉 Describes **structural hardware instantiation**.  
👉 Great for **arrays of gates, adders, or replicated modules**.  

---

#### ✅ Example 1: AND Gate Array
```verilog
module and_array #(parameter N=4)(
    input  wire [N-1:0] A, B,
    output wire [N-1:0] Y
);
genvar i;
generate
    for (i = 0; i < N; i = i + 1) begin : AND_LOOP
        and g (Y[i], A[i], B[i]);
    end
endgenerate
endmodule
```

#### ✅ Example 2: Ripple Carry Adder (using for generate)

```verilog
module rca #(parameter N=4)(
    input  wire [N-1:0] A, B,
    input  wire Cin,
    output wire [N-1:0] Sum,
    output wire Cout
);
wire [N:0] c;
assign c[0] = Cin;
assign Cout = c[N];

genvar i;
generate
    for (i = 0; i < N; i = i + 1) begin : RCA_LOOP
        full_adder FA (
            .a(A[i]), .b(B[i]), .cin(c[i]),
            .sum(Sum[i]), .cout(c[i+1])
        );
    end
endgenerate
endmodule
```

### 📊 Summary of Differences

| **Feature**          | **Procedural `for` Loop**              | **`generate for` Loop**                 |
|-----------------------|-----------------------------------------|-----------------------------------------|
| **Location**          | Inside an `always` block                | Outside an `always` block                |
| **Primary Function**  | Evaluating expressions / logic behavior | Instantiating / replicating hardware     |
| **Concept**           | Describes *behavior*                    | Describes *physical structure*           |


---

<p align="center">
  <img src="https://img.shields.io/badge/🧪%20Labs%20on%20For%20Loop%20&%20Generate-green?style=for-the-badge" />
</p>

## 6) 🧪 Labs on For Loop & For Generate  

In this lab, we explore **loop-based Verilog constructs** (`for` loop inside `always` and `for-generate` outside `always`) by simulating different designs and visualizing their outputs in GTKWave.  

---

## 1️⃣ MUX using `for-generate`  

### A) 📜 Design Code  
```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule
```
**Explanation:**
The MUX is designed using the generate for construct which structurally replicates MUX logic. Each input line is connected to the output depending on the select line, demonstrating how generate loops can instantiate multiple hardware blocks systematically.

## B) 📈 Simulation Waveform
<p align="center">
  <img src=" " alt="mux_gen" width="700"/>
</p> 

---

## 2️⃣ DEMUX using case Statement

### A) 📜 Design Code  
```verilog
module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule

```
**Explanation:**
This DEMUX is implemented using a case statement inside an always block. The procedural logic assigns the input to one of the output lines based on the select value. This demonstrates behavioral modeling using procedural constructs.

## B) 📈 Simulation Waveform
<p align="center">
  <img src=" " alt="demux_case" width="700"/>
</p> 

---
## 3️⃣ DEMUX using for-generate

### A) 📜 Design Code  
```verilog

module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule


```
**Explanation:**
The DEMUX is implemented using the generate for loop, where each output line is instantiated structurally. This allows replication of hardware logic efficiently compared to case-based modeling.

## B) 📈 Simulation Waveform
<p align="center">
  <img src=" " alt="demux_case" width="700"/>
</p> 

---
## 4️⃣ Ripple Carry Adder (RCA) using for-generate

### A) 📜 Design Code  
```verilog
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```
**Explanation:**
The Ripple Carry Adder is built by structurally replicating Full Adder (FA) modules using a for-generate loop. Each FA block takes a carry-in and produces a carry-out, which is fed into the next stage. This highlights how generate loops help create scalable arithmetic circuits.

## B) 📈 Simulation Waveform
<p align="center">
  <img src=" " alt="demux_gen" width="700"/>
</p> 

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
