<div align="center">
  <h1>⚡ Day 2 - Timing Libraries, Hierarchical vs Flat Synthesis & Flop Coding Styles</h1>
</div>

<div align="center">
  <table>
    <tr>
      <td><img src="https://img.shields.io/badge/Week1-Day2-blueviolet" /></td>
      <td><img src="https://img.shields.io/badge/.lib-Files-green" /></td>
      <td><img src="https://img.shields.io/badge/Synthesis-Hier_vs_Flat-orange" /></td>
      <td><img src="https://img.shields.io/badge/Flops-Coding_Styles-blue" /></td>
      <td><img src="https://img.shields.io/badge/Tools-Yosys_HandsOn-red" /></td>
    </tr>
  </table>
  <p>🎯 <b>Day 2</b> of the RISC-V SoC Tapeout program focuses on exploring <b>timing libraries</b>, comparing <b>hierarchical vs flat synthesis</b>, and experimenting with <b>flip-flop coding styles and optimizations</b>. 🏗️  
  Through structured labs and lectures, we dive deeper into how technology libraries interact with synthesis and how RTL coding choices affect the resulting gate-level netlists. ⚡</p>
</div>

---

## 📚 Table of Contents
1. ⏱️ Introduction to Timing `.lib` Files  
2. 🏗️ Hierarchical vs Flat Synthesis  
3. 🔁 Flip-Flop Coding Styles & Optimizations  
4. 🧪 Lab Activities & Experiments  
5. ✅ Summary  

---

## ⏱️ 1. Timing `.lib` Files

Timing libraries (`.lib`) describe **standard cell characteristics** including:  
- Propagation delay  
- Setup & hold times  
- Power & area trade-offs  

### Lecture Blocks
- **12. Lab4 – Introduction to .lib (Part 1)**  
- **13. Lab4 – Introduction to .lib (Part 2)**  
- **14. Lab4 – Introduction to .lib (Part 3)**  

<p align="center">
  <img src="https://img.shields.io/badge/Concepts-Cell_Delays,_Setup/Hold-green" />
</p>

---

## 🏗️ 2. Hierarchical vs Flat Synthesis

- **Hierarchical Synthesis**  
  - Preserves module boundaries  
  - Easier debugging and modularity  
- **Flat Synthesis**  
  - Flattens design for global optimization  
  - Better PPA (Power, Performance, Area) trade-offs but harder to analyze  

### Lecture Blocks
- **15. Lab5 – Hier vs Flat Synthesis (Part 1)**  
- **16. Lab5 – Hier vs Flat Synthesis (Part 2)**  

<p align="center">
  <img src="https://img.shields.io/badge/Tradeoff-Optimization_vs_Modularity-orange" />
</p>

---

## 🔁 3. Flip-Flop Coding Styles & Optimizations

Efficient **RTL flop coding** ensures:  
- Correct synthesis mapping  
- Avoiding simulation–synthesis mismatches  
- Cleaner optimization by tools  

### Lecture Blocks
- **17. Flop Coding Styles (Part 1)**  
- **18. Flop Coding Styles (Part 2)**  
- **19. Lab – Flop Synthesis & Simulation (Part 1)**  
- **20. Lab – Flop Synthesis & Simulation (Part 2)**  
- **21. Interesting Optimizations (Part 1)**  
- **22. Interesting Optimizations (Part 2)**  

<p align="center">
  <img src="https://img.shields.io/badge/Focus-Reset_Handling,_Sensitivity_List-blue" />
</p>

---

## 🧪 4. Lab Activities & Experiments

- Analyzing `.lib` timing data with Yosys  
- Running synthesis in both **hierarchical & flat modes**  
- Experimenting with **different DFF coding styles**  
- Observing optimizations during synthesis  

<p align="center">
  <img src="https://img.shields.io/badge/Practice-Hands_On_Labs-red" />
</p>

---

## ✅ 5. Summary

- Understood how `.lib` timing libraries bridge **RTL and technology cells**.  
- Compared **hierarchical** vs **flat synthesis flows** and their trade-offs.  
- Learned different **flop coding styles** and their synthesis impact.  
- Explored **synthesis optimizations** through Yosys labs.  

---

<div align="center">
  <h2>🎉 End of Day 2 ✨</h2>
</div>

