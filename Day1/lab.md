# 💻 Day 1 Lab Commands (RTL Design & Synthesis)  

## 🔹 Git Clone Workshop Repo

```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

## 🔹 Simulation with Icarus Verilog (iverilog) + GTKWave
Compile Design + Testbench
```bash
iverilog good_mux.v tb_good_mux.v
```

Run Simulation
```bash
./a.out
```

View Waveform in GTKWave
```bash
gtkwave tb_good_mux.vcd
```

Open Design & TB in gvim (side-by-side)
```bash
gvim tb_good_mux.v -o good_mux.v
```


## 🔹 Synthesis using Yosys
Invoke Yosys
```bash
yosys
```

Read Standard Cell Library
```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Read Verilog Design
```bash
read_verilog good_mux.v
```

Synthesize the Design
```bash
synth -top good_mux
```

Generate Netlist
```bash
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Write Netlist to File
```bash
write_verilog good_mux_netlist.v
```

Visualize Logic
```bash
show
```


