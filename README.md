# 🧠 Post-Layout STA & PVT Timing Analysis — Week 8

<div align="center">

![RISC-V](https://img.shields.io/badge/RISC--V-SoC%20Tapeout-blue?style=for-the-badge&logo=riscv)
![VSD](https://img.shields.io/badge/VSD-Program-orange?style=for-the-badge)
![Sky130](https://img.shields.io/badge/SkyWater-130nm-green?style=for-the-badge)
![OpenSTA](https://img.shields.io/badge/OpenSTA-Timing%20Analysis-purple?style=for-the-badge)
![India](https://img.shields.io/badge/Made%20in-India-saffron?style=for-the-badge)

</div>

<div align="center">

⏱️ SPEF Annotation → 📐 PVT Timing → 📊 Slack Analysis → 🔬 Graphs → ✅ Timing Closure

</div>

---

## 📅 Week 8 — Post-Layout STA & Corner-Based Timing Closure

This week focused on performing **Post-Layout Static Timing Analysis (STA)** for the fully routed VSDBabySoC design.  
Using the **SPEF file generated in Week 7**, we analyzed how parasitic resistance and capacitance affect timing across different **PVT corners**.  
This is the most realistic timing evaluation before tape-out.

---

## 🧪 Task 1 — Post-Route Design Loading

### 📘 Objective
Load the complete routed design into OpenSTA along with SPEF parasitics and liberty files for all corners.

### 🧰 Summary
- Loaded **post-route Verilog netlist**, **SPEF**, **SDC**, and **PVT Liberty files**.
- SPEF provided real extracted RC values from routed metal layers.
- Timing was updated using these parasitic delays.

---

## 🧪 Task 2 — PVT Corner Timing Analysis

### 📘 Objective
Perform setup and hold analysis across **TT, SS, FF, SF, and FS** corners.

### 📝 What Was Done
- Generated **setup (max delay)** and **hold (min delay)** reports for each corner.
- Visualized **timing dependency graphs**, showing gate and net contributions.
- Observed delay variation due to voltage, temperature, and transistor process changes.

### 📊 Observations
- **SS (Slow-Slow)** corner gave the worst-case setup slack.
- **FF (Fast-Fast)** corner gave the worst-case hold slack.
- Parasitics caused noticeable delay increase compared to Week 3 analysis.

---

## 🧪 Task 3 — Week 3 vs Week 8 Timing Comparison

### 📘 Objective
Compare **post-synthesis (Week 3)** timing with **post-route (Week 8)** timing.

### 📝 What Changes Were Noticed
- Routing parasitics increased net delays → **setup slack reduced**.
- Higher capacitance loads changed slews → **hold slack updated**.
- Some paths that were clean in Week 3 became borderline after routing.

---

## 🧪 Task 4 — Understanding Post-Route Timing Behavior

### 📘 Objective
Interpret how SPEF parasitics and routing impact real timing numbers.

### 📝 Key Learnings
- Routing introduces **wire resistance + capacitance**, increasing delay.  
- Coupling between wires also affects delay (cross-talk effects).  
- Net delay becomes more dominant compared to cell delay.  
- PVT corners drastically shift timing margins:  
  - SS → worst for setup  
  - FF → worst for hold  
---

## ✅ Key Takeaways

- Learned full **post-layout STA flow** using OpenSTA.  
- Understood how **PVT corners influence timing** in real silicon.  
- Realized the huge difference between **ideal vs parasitic-aware timing**.  
- Successfully compared **Week 3 vs Week 8** timing performance.  
- Completed end-to-end **Timing Sign-off preparation** for tape-out.

---

## 🙏 Acknowledgment

<div align="center">

### 🏆 Program Leadership & Support

Thanks to **Kunal Ghosh** and the **VSD Team** for continuous support throughout Week 8.

</div>

---

## 📈 Weekly Progress Tracker

![Week 1](https://img.shields.io/badge/Week%201-RTL%20Foundations-success?style=flat-square)
![Week 2](https://img.shields.io/badge/Week%202-SoC%20Design%20Flow-success?style=flat-square)
![Week 3](https://img.shields.io/badge/Week%203-STA-success?style=flat-square)
![Week 4](https://img.shields.io/badge/Week%204-CMOS%20Design-success?style=flat-square)
![Week 5](https://img.shields.io/badge/Week%205-Floorplan%20%26%20Placement-success?style=flat-square)
![Week 6](https://img.shields.io/badge/Week%206-Physical%20Design-success?style=flat-square)
![Week 7](https://img.shields.io/badge/Week%207-Routing%20%26%20SPEF-success?style=flat-square)
![Week 8](https://img.shields.io/badge/Week%208-Post%20Layout%20STA-brightgreen?style=flat-square)

### 🚀 Onward…

Next: **Final Tape-out Sign-Off: DRC → LVS → GDS → Submission** 🚀

---

**🔗 Program Links**

[![VSD Website](https://img.shields.io/badge/VSD-Official%20Website-blue?style=flat-square)](https://vsdiat.vlsisystemdesign.com/)  
[![Sky130](https://img.shields.io/badge/Open%20PDK-Sky130-green?style=flat-square)](https://github.com/google/skywater-pdk)  
[![Efabless](https://img.shields.io/badge/Efabless-Platform-orange?style=flat-square)](https://efabless.com/)

**👨‍💻 Participant:** [VEERARAGAVAN7](https://github.com/VEERARAGAVAN7)

