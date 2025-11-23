# 🕒 Week 8 – Post-Layout STA & Timing Graphs Across PVT Corners  
## VSDBabySoC — Post-Route Timing Closure Using OpenSTA

### VSD Hardware Design Program

---

## 📚 Contents
- [Objective](#objective)
- [Why This Task Is Important](#why-this-task-is-important)
- [Task Flow](#task-flow)
  - [1. Load Post-Route Design into OpenSTA](#1-load-post-route-design-into-opensta)
  - [2. Run STA Across PVT Corners](#2-run-sta-across-pvt-corners)
  - [3. Generate Timing Reports & Graphs](#3-generate-timing-reports--graphs)
  - [4. Compare Week 3 vs Week 8 Timing](#4-compare-week-3-vs-week-8-timing)
  - [5. Interpret Results](#5-interpret-results)


---

# 🎯 `Objective`
To perform Post-Layout Static Timing Analysis (STA) using the SPEF extracted after routing in Week 7, analyze timing across multiple PVT corners, and compare the results with Week 3 post-synthesis timing.

- Post-route **gate-level netlist**
- **SPEF parasitics**
- **PVT corner libraries**
- **SDC timing constraints**

This analysis measures how routing affects timing and validates the design for tape-out.

---

## 🔍 `Why This Task Is Important`

This is the final stage of the complete ASIC flow:

`RTL → Synthesis → Floorplan → Placement → CTS → Routing → SPEF → Post-Layout STA`

Post-route STA reveals:

- Impact of **RC parasitics (from SPEF)** on timing
- Variation in timing across **PVT corners**
- Difference between **pre-route vs post-route timing**
- Whether the SoC is **ready for fabrication**

---

# 🚀 `Task Flow`

---

## 1️⃣ `Load Post-Route Design into OpenSTA`

Load the required data:

- Liberty files (`TT`, `SS`, `FF`) 
   - avsddac.lib
   - avsdpll.lib
   - sky130_fd_sc_hd__tt_025C_1v80.lib 
- Post_route_netlist 
   - vsdbabysoc_post_route_netlist.v
- SPEF 
   - vsdbabysoc.spef
- SDC 
   - vsdbabysoc_post_cts.sdc
  

### TCL Script

<details> <summary><strong>sta_across_pvt_route.tcl</strong></summary>
# (The above file are available in my system's /usr/local/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lib directory)

 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
 set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
 set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
 set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
 set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
 set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
 set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
 set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
 set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
 set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
 set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
 set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
 set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

 read_liberty /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/lib/avsdpll.lib
 read_liberty /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/lib/avsddac.lib
 
 set PDK_ROOT "/usr/local/share/pdk"
 
 for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {
 read_liberty $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/lib/$list_of_lib_files($i) 
 read_verilog /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/vsdbabysoc_post_route_netlist.v
 link_design vsdbabysoc
 current_design
 read_sdc /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/vsdbabysoc_post_cts.sdc
 read_spef /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/vsdbabysoc.spef
 check_setup -verbose
 report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} > /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/min_max_$list_of_lib_files($i).txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_max_slack.txt
 report_worst_slack -max -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_max_slack.txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_min_slack.txt
 report_worst_slack -min -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_worst_min_slack.txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_tns.txt
 report_tns -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_tns.txt

 exec echo "$list_of_lib_files($i)" >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_wns.txt
 report_wns -digits {4} >> /home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/output/sta_wns.txt
 }
</details>

## 2️⃣ `Run Post-Route STA Across PVT Corners`

Run multi-corner timing using the provided TCL script.

```shell
 % source/home/veeraragavan/VSD_Soc_TapeOut_Program/week8/Post_route_STA/src/sta_across_pvt_route.tcl
```

![Alt Text](Screenshots/R_sta1.png)

After running the STA script, you can navigate to the `output` directory to see all the generated timing reports. This includes detailed path delay reports for each library corner (`min_max_*.txt`), worst setup and hold slack summaries (`sta_worst_max_slack.txt and sta_worst_min_slack.txt`), as well as total negative slack (sta_tns.txt) and worst negative slack (`sta_wns.txt`). These files provide a complete overview of the BabySoC design’s timing performance after routing.

![Alt Text](Screenshots/R_sta2.png)

## 3️⃣ `Generate Timing Reports & Graphs`

Here is a tabulated view of the key timing results generated by the STA script.

| PVT Corner (Lib File)  | **WNS (Setup)**            | **TNS (Setup)**               | **WHS (Hold)**  | **THS (Hold)**  |
| ---------------------- | -------------------------- | ----------------------------- | --------------- | ----------------|
| **tt_025C_1v80**       | 0.0000                     | 0.0000                        | 0.3102          | 0.0000          |
| **ff_100C_1v65**       | 0.0000                     | 0.0000                        | 0.2503          | 0.0000          |
| **ff_100C_1v95**       | 0.0000                     | 0.0000                        | 0.1983          | 0.0000          |
| **ff_n40C_1v56**       | 0.0000                     | 0.0000                        | 0.2901          | 0.0000          |
| **ff_n40C_1v65**       | 0.0000                     | 0.0000                        | 0.2559          | 0.0000          |
| **ff_n40C_1v76**       | 0.0000                     | 0.0000                        | 0.2264          | 0.0000          |
| **ss_100C_1v40**       | **–3.6516** *(VIOLATION)*  | **–678.1725** *(VIOLATION)*   | 0.8942          | 0.0000          |
| **ss_100C_1v60**       | 0.0000                     | 0.0000                        | 0.6345          | 0.0000          |
| **ss_n40C_1v28**       | **–32.8690** *(VIOLATION)* | **–15110.6885** *(VIOLATION)* | 1.7456          | 0.0000          |
| **ss_n40C_1v35**       | **–18.1312** *(VIOLATION)* | **–7144.3579** *(VIOLATION)*  | 1.3038          | 0.0000          |
| **ss_n40C_1v40**       | **–12.1683** *(VIOLATION)* | **–3859.9375** *(VIOLATION)*  | 1.0915          | 0.0000          |
| **ss_n40C_1v44**       | **–8.7039** *(VIOLATION)*  | **–2289.1768** *(VIOLATION)*  | 0.9627          | 0.0000          |
| **ss_n40C_1v76**       | 0.0000                     | 0.0000                        | 0.4868          | 0.0000          |



### 📈 `Comparison Graphs`

Here is a graph showing the comparison of `worst-case hold slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/WHS.png)

Here is a graph showing the comparison of `worst-case setup slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/WSS.png)

Here is a graph showing the comparison of `WNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/WNS.png)

Here is a graph showing the comparison of `TNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](Screenshots/TNS.png)


## 4️⃣ `Week 3 vs Week 8 Timing Comparison`

The table below compares **Post-Synthesis Timing (Week 3)** with **Post-Route Timing (Week 8)** across all PVT corners using SPEF-annotated STA.

## 📊 Timing Summary Across All PVT Corners (Post-Route STA)


| PVT Corner        | WNS(Week3) | WNS(Week8) | TNS(Week3)  | TNS(Week8)  | WHS(Week3)  | WHS(Week8) | THS(Week3) | THS(Week8) |
|-------------------|-----------:|-----------:|------------:|------------:|------------:|-----------:|-----------:|-----------:|
| tt_025C_1v80      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.3096      | 0.3102     | 0.0000     | 0.0000     |
| ff_100C_1v65      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2491      | 0.2503     | 0.0000     | 0.0000     |
| ff_100C_1v95      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.1960      | 0.1983     | 0.0000     | 0.0000     |
| ff_n40C_1v56      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2915      | 0.2901     | 0.0000     | 0.0000     |
| ff_n40C_1v65      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2551      | 0.2559     | 0.0000     | 0.0000     |
| ff_n40C_1v76      | 0.0000     | 0.0000     | 0.0000      | 0.0000      | 0.2243      | 0.2264     | 0.0000     | 0.0000     |
| ss_100C_1v40      | -13.0402   | -3.6516    | -7518.3794  | -678.1725   | 0.9053      | 0.8942     | 0.0000     | 0.0000     |
| ss_100C_1v60      | -6.2777    | 0.0000     | -2911.6208  | 0.0000      | 0.6420      | 0.6345     | 0.0000     | 0.0000     |
| ss_n40C_1v28      | -52.9031   | -32.8690   | -36775.3008 | -15110.6885 | 1.8296      | 1.7456     | 0.0000     | 0.0000     |
| ss_n40C_1v35      | -33.1984   | -18.1312   | -23279.6816 | -7144.3579  | 1.3475      | 1.3038     | 0.0000     | 0.0000     |
| ss_n40C_1v40      | -24.6564   | -12.1683   | -17173.1113 | -3859.9375  | 1.1249      | 1.0915     | 0.0000     | 0.0000     |
| ss_n40C_1v44      | -19.9610   | -8.7039    | -13603.9434 | -2289.1768  | 0.9909      | 0.9627     | 0.0000     | 0.0000     |
| ss_n40C_1v76      | -3.9606    | 0.0000     | -1905.4320  | 0.0000      | 0.5038      | 0.4868     | 0.0000     | 0.0000     |


## 5️⃣ `Interpret Results`

### ⭐ Post-Synthesis vs Post-Route Timing Analysis (VSDBabySoC)
### Key Differences: Post-Synthesis vs Post-Route STA
| Aspect                        | Post-Synthesis Timing Analysis              | Post-Route Timing Analysis                                                |
| ----------------------------- | ------------------------------------------- | ------------------------------------------------------------------------- |
| **Clock Network**             | Ideal clock, **0 skew**, no insertion delay | Real propagated clock tree with **0.75–0.78 ns** latency and skew         |
| **Interconnect Modeling**     | Fanout-based delay, no parasitics           | Real RC parasitics from SPEF (resistance, capacitance, coupling)          |
| **Min (Hold) Path Delay**     | Data arrival: **0.2749 ns**                 | Data arrival: **1.054 ns** (slower due to routing parasitics)             |
| **Min Path Slack**            | **+0.3096 ns**                              | **+0.3102 ns** (similar slack but different actual delays)                |
| **Max (Setup) Path Delay**    | Data arrival: **9.7554 ns**                 | Data arrival: **5.6038 ns** (routing created a *different* critical path) |
| **Max Path Slack**            | **+1.1060 ns**                              | **+6.0345 ns** (post-route path is far faster / shorter)                  |
| **Critical Path Behavior**    | Driven by fanout-heavy cell (~242 loads)    | More localized path after placement and buffering                         |
| **Clock Latency Considered?** | No (ideal)                                  | Yes — propagated clock delay (~0.78 ns)                                   |
| **Accuracy vs Silicon**       | ~70–80% correlation                         | ~95–98% correlation                                                       |
| **Path Type**                 | Logical placement only                      | Physical placement + routing topology                                     |




