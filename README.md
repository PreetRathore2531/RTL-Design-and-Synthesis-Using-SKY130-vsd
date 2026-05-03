# RTL-Design-and-Synthesis-Using-SKY130-vsd

This repository documents my hands-on learning from the **VSD RTL Design and Synthesis Workshop**, where I explored the complete digital design front-end flow from writing Verilog RTL to generating gate-level netlists using open-source tools.

---

## What This Repository Covers

- Writing RTL in Verilog
- Creating and understanding testbenches
- Simulation using Icarus Verilog
- Waveform analysis using GTKWave
- Synthesis using Yosys
- Technology mapping using SKY130 standard cell libraries
- Flip-flop coding styles and their impact

---

## Tools Used

- **Icarus Verilog (iverilog)** → Simulation  
- **GTKWave** → Waveform visualization  
- **Yosys** → Synthesis  
- **SKY130 PDK** → Standard cell library  

---

## Module 1 - Introduction to Verilog RTL Design and Synthesis

### Theory

**RTL (Register Transfer Level)**  
RTL is a crucial abstraction in digital design using hardware description languages like Verilog to define how data moves between registers and is processed through combinational logic.

---

**Simulation**  
Simulation is the process of using software tools to verify that a design written in HDL (such as Verilog) behaves correctly before fabrication. It models the logic and timing of circuits, allowing engineers to validate functionality and fix errors early, saving significant cost and time compared to physical prototyping.

---

**Synthesis**  
Synthesis is the automated process of converting high-level behavioral code (RTL written in Verilog/VHDL) into an optimized gate-level netlist mapped to a specific technology library.

### Implementation

#### Module 1 Tasks:-

1. Simulate Verilog RTL using Icarus Verilog  
2. Generate waveform using GTKWave  
3. Perform synthesis using Yosys and generate netlist  

---

### 1. Simulation and viewing Waveform

Commands to compile and run simulation:

```
iverilog good_mux.v tb_good_mux.v
./a.out
```

To view waveform:
```
gtkwave tb_good_mux.vcd
```

Screenshots - 
<img width="1280" height="799" alt="creation of vcd file for mux" src="https://github.com/user-attachments/assets/f2f7f28a-e87c-413b-b6e3-c83a785d1463" />
<img width="1280" height="795" alt="tb and mux code" src="https://github.com/user-attachments/assets/0d0bd8ec-1bb6-4861-90c7-259e89289485" />
<img width="1281" height="801" alt="gtkwave waveform for good_mux" src="https://github.com/user-attachments/assets/1c50803f-edef-42e5-b216-6122a6fe83fb" />

### 2. Logic Synthesis in Yosys

Commands to implement synthesis:

```bash
yosys

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog good_mux.v

synth -top good_mux

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

write_verilog -noattr good_mux_netlist.v

show
```

To view the generated netlist:
```
gvim good_mux_netlist.v
```

Screenshots:

<img width="1915" height="1032" alt="reading lib file and verilog file " src="https://github.com/user-attachments/assets/3d716817-614b-4902-aeac-26ee56059e5a" />
<img width="1913" height="1032" alt="logic realisation of good_mux" src="https://github.com/user-attachments/assets/6ec9922d-a690-493f-9069-f586d7f22a57" />
Netlist - 
<img width="1907" height="1033" alt="good_mux_netlist" src="https://github.com/user-attachments/assets/a35521b3-d297-4692-bfd8-a057d3aded17" />
Simplified netlist - 
<img width="1914" height="853" alt="simplified netlist" src="https://github.com/user-attachments/assets/b50f2ada-0718-420c-9661-7ed6c951f92e" />

## Module 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

### Theory

**Timing Libraries (.lib)**  
A timing library (`.lib`) contains information about standard cells such as delay, power, and area. It is used by synthesis tools to map RTL to actual hardware gates.

Each library represents a specific condition:
- Process (P)
- Voltage (V)
- Temperature (T)

Example: sky130_fd_sc_hd__tt_025C_1v80.lib


- `tt` → Typical process  
- `025C` → Temperature  
- `1v80` → Voltage  

---

**Multiple Modules (Hierarchical Design)**  

Complex designs are built by dividing them into smaller modules (submodules) and connecting them in a top module.

- Each submodule performs a specific function  
- Modules communicate using wires  
- Improves readability and reuse  

---

**Synthesis Insight:**

- **Hierarchical synthesis** → preserves module structure  
- **Flat synthesis** → merges all modules into one  

---

**Flop Coding Styles**

Flip-flops are used to:
- Store data  
- Control timing  
- Avoid glitches  

Different coding styles affect:
- Area  
- Timing  
- Power  

Proper initialization is important to avoid unknown (`X`) values.

---

### Implementation

#### Module 2 Tasks:-

1. Understand timing libraries and `.lib` files  
2. Perform hierarchical and flat synthesis  
3. Analyze different flip-flop coding styles  
4. Observe optimization cases in synthesis  

---

### 1. Introduction to Timing Libraries

Commands:

```bash
gvim ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Screenshots:

sky130 library-
<img width="1911" height="1036" alt="sky130 library " src="https://github.com/user-attachments/assets/af5ad3bf-29ac-4bf9-b961-1f11e0746f01" />

various cells and Timing information-
<img width="1363" height="714" alt="various cells " src="https://github.com/user-attachments/assets/a50f9a6b-8097-4eeb-b27f-763e0877e83c" />
<img width="1915" height="1031" alt="timing info and_or" src="https://github.com/user-attachments/assets/d555eda7-410d-4650-bd1c-d941485c5e18" />

And_Or Gate cell with 5 inputs-
<img width="1365" height="726" alt="and_or gate with 5 ip" src="https://github.com/user-attachments/assets/ed26cb6f-2230-4570-b3a0-31bb19732cb8" />

Comparision between AND2_0/AND2_2/AND2_4-
<img width="1917" height="1034" alt="and20_and22_and24_area" src="https://github.com/user-attachments/assets/6342c050-6bf6-4827-873f-a7627c73add7" />

### 2. Hierarchical Synthesis

Commands:
```
yosys

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog multiple_modules.v

synth -top multiple_modules

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

write_verilog -noattr multiple_modules_hier.v

show
```

Screenshots:

Verilog code-
<img width="1915" height="1032" alt="multiple_modules" src="https://github.com/user-attachments/assets/cda16d22-635a-423f-be73-6262edfb382c" />

Synthesis-
<img width="1917" height="1079" alt="synth_multiple_gate" src="https://github.com/user-attachments/assets/96353035-4544-4faf-a926-821e0979fed3" />

Implementation diagram-
<img width="1915" height="1032" alt="multiple_modules_diagram" src="https://github.com/user-attachments/assets/37db934f-3340-45b3-ad2d-1c1b709215fb" />

### 3. Flat Synthesis

Commands:
```
flatten

write_verilog -noattr multiple_modules_flat.v

show
```

Screenshots:
<img width="1913" height="1033" alt="flatten_multiple_module_diagram" src="https://github.com/user-attachments/assets/62449927-8a0b-43e1-bf0a-32bd3cfe47a9" />

Comparision between hierarchical and Flat synthesis:
<img width="1919" height="1029" alt="flat_vs_hier_netlist" src="https://github.com/user-attachments/assets/11ae5126-6ba5-4ed9-9422-fe989515fc04" />

### 4. Submodule Level Synthesis

Commands:
```
synth -top sub_module1

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```

Screenshots:
<img width="1917" height="1029" alt="sub_module1_diagram" src="https://github.com/user-attachments/assets/e895dc9a-048b-4a03-b773-f6ab8821933b" />

### 5. Various Flop Coding Styles

This section explores different flip-flop coding styles in Verilog and how they affect simulation and synthesis.

---

### 5.1 D Flip-Flop with Asynchronous Reset

**Verilog Code:**
<img width="1918" height="1041" alt="asyncdff_code" src="https://github.com/user-attachments/assets/4940d91d-64ca-4432-a16b-6c4849bcee40" />

**Waveform:**
<img width="1913" height="1031" alt="async_dff_sim" src="https://github.com/user-attachments/assets/e55ccf93-43d6-4289-805d-202d9c2031ef" />
<img width="1909" height="1007" alt="asyncres_inaction" src="https://github.com/user-attachments/assets/6dd0f554-e26a-4079-bf4f-5b5d77a224a6" />

**Synthesis Diagram:**
<img width="1913" height="1074" alt="dff_asyncres_synth_diagram" src="https://github.com/user-attachments/assets/1af27335-9c3d-47eb-81ab-4a34428a77ee" />

---

### 5.2 D Flip-Flop with Asynchronous Set

**Verilog Code:**
<img width="1913" height="1035" alt="asyncset_dff_code" src="https://github.com/user-attachments/assets/d59e687c-ea1b-499d-97ba-7ec4b2383bb7" />

**Waveform:**
<img width="1915" height="1028" alt="async_set_dff_inaction_2" src="https://github.com/user-attachments/assets/87ada90b-03d5-42c8-8d0c-854e2416a756" />

**Synthesis Diagram:**
<img width="1913" height="1079" alt="dff_asyncset_sunth_diagram" src="https://github.com/user-attachments/assets/6b0e17eb-d34e-429b-8955-d984988fd486" />

---

### 5.3 D Flip-Flop with Synchronous Reset

**Verilog Code:**
<img width="1913" height="1024" alt="sync_dff_code" src="https://github.com/user-attachments/assets/c827ff85-2298-409f-b8c4-2185bc2b4a60" />

**Waveform:**
<img width="1912" height="1026" alt="syncres_dff_sim" src="https://github.com/user-attachments/assets/7fd14784-b7ba-4e79-acf0-ef4de39c5c59" />
<img width="1916" height="1033" alt="sync_dff_res_inaction" src="https://github.com/user-attachments/assets/4ba2691b-c408-4547-ba08-7212faca66af" />

Showing that reset has high priority than input-
<img width="1915" height="1033" alt="syncresdff_reshigh_priority" src="https://github.com/user-attachments/assets/8cc8c221-393f-41f2-9292-a4d388e1f82c" />

**Synthesis Diagram:**
<img width="1915" height="1079" alt="dff_syncres_synth_diagram" src="https://github.com/user-attachments/assets/7e89982e-e2b0-4645-8c48-4b6535887984" />

---

### 5.4 Observations

- Asynchronous reset/set works independent of clock  
- Synchronous reset depends on clock edge  
- Different coding styles result in different standard cells after synthesis  
- Proper coding style is important for timing and optimization  

---

### 5.5 Technology Mapping (DFF Mapping)

<img width="1913" height="1051" alt="dfflibmap_for_flops" src="https://github.com/user-attachments/assets/4a44ab64-354b-4af4-945f-e6412c81796b" />

---

### 6. Optimisations

### 6.0 Theory - mul2 vs mult8

**mul2 Design:**
- A simple combinational design that multiplies an input by 2
- a[2:0] * 2 => y[3:0]
- Usually implemented using a left shift operation (`y = a << 1`)  
- Very efficient — minimal logic required  
- Synthesizes into simple wiring or minimal gates  

---

**mult8 Design:**
- A slightly more complex design that multiplies an input by 8  
- Can also be implemented using shift (`y = a << 3`)  
- When written using multiple modules or intermediate signals, synthesis tools optimize it heavily  
- Demonstrates how redundant logic can be removed during synthesis  

---

### 6.1 mul2 & mult8 snapshots

mul2-
<img width="1910" height="1079" alt="mul2_synth_diagram" src="https://github.com/user-attachments/assets/cb4bba9c-8673-444a-831a-155f4e759489" />
<img width="1919" height="1079" alt="mul2_netlist" src="https://github.com/user-attachments/assets/37599d5a-1d45-43d7-ae09-21aa949d5836" />
<img width="1911" height="1079" alt="no_gate_no_wire_mul2" src="https://github.com/user-attachments/assets/d3ac17f8-eac6-4bb7-9b0a-340e2f85189a" />

mult8-
<img width="1916" height="1079" alt="mult8_synth_diagram" src="https://github.com/user-attachments/assets/3c57be9f-c676-4d74-97d8-08693ec0694d" />
<img width="1915" height="1079" alt="mult8_netlist" src="https://github.com/user-attachments/assets/819daad5-ab6f-4091-b462-948ca1f58cca" />

