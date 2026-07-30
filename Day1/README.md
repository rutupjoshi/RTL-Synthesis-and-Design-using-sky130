# DAY 1 :Introduction to Iverilog ,Design , Test bench & Yosys.
Today we learn about digital design, Iverilog (simulator) , testbench ,the synthesizer Yosys.

---

## contents

1. [Definitions of  Simulator, Design, and Testbench?](#1-definitions-of-simulator-design-and-testbench)
2. [Iverilog](#2-iverilog)
3. [Lab: Simulation of a 2-to-1 Multiplexer](#3-lab-simulation-of-a-2-to-1-multiplexer)
4. [Verilog Code Analysis](#4-verilog-code-analysis)
5. [Introduction to Yosys & Gate Libraries](#5-introduction-to-yosys--gate-libraries)
6. [Synthesis Lab with Yosys of MUX](#6-synthesis-lab-with-yosys-of-mux)

---

## 1. Definitions of  Simulator, Design, and Testbench?

### Simulator
A simulator is a device, system, or software program that mimics real-world activities, environments, or operations.
It is a software tool used to model and test the behavior of circuits before building them in real life.

### Design
Design is the actual verilog code or set of verilog codes which has the intended functionality to meet with the required specifications.

### Testbench
Testbench works on how the design actually meets the functionality of the stated specifications.
It is the setup to apply stimulus to the design to check its functionality.

---

## 2. Iverilog
Icarus Verilog is a free open source compiler and simulation tool used by hardware engineers to design & test digital circuits.
This produces a .vcd file as output.

---

## 3. Lab: Simulation of a 2-to-1 Multiplexer:
Here we simulate 2-to-1 multiplexer using Iverilog.

### Step - 1 : Cloning the files:

```shell
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

### Step - 2 : Install the required files:

```shell
sudo apt install iverilog
sudo apt install gtkwave
```

### Step - 3 : Simulation of design :
```shell
iverilog -o simv good_mux.v tb_good_mux.v
```

### Step - 4: Run the simuation :
```shell
vvp simv
```

### Step - 5: View the wave:
```shell
gtkwave tb_good_mux.vcd
```
The Output:
<img width="1920" height="1080" alt="good _ mux  v gtkwave WAVEFORM" src="https://github.com/user-attachments/assets/29c364ef-9291-4e82-84f0-ce7c6a975b3e" />

---

## 4. Verilog Code Analysis
The code for 2:1 mux is :
```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```
The inputs: i0,i1,sel.
The output: y.
If `sel` is 1, `y` gets `i1`; if `sel` is 0, `y` gets `i0`.

---

## 5. Introduction to Yosys & Gate Libraries
Yosys synthesis is an open-source framework that converts register-transfer level (RTL) designs written in Verilog or SystemVerilog into a gate-level netlist. The key steps in this process include reading design files, running coarse and fine optimizations, and mapping technology cells. 

**Basic Synthesis Flow Steps**
**Read Files:** Load your RTL source files (read_verilog) and standard cell library (read_liberty).
**Elaboration:** Check design hierarchy and resolve module parameters (hierarchy).
**Process Conversion:** Translate behavioral statements into multiplexers and registers (proc).
**Optimization:** Run general optimization passes to simplify logic (opt).
**Technology Mapping:** Map internal cells to target gate libraries using internal tools or external logic optimizers like ABC (techmap, abc).
**Write Output:** Export the final netlist to a gate-level Verilog or BLIF file (write_verilog).

###  Why Do Libraries Have Different Gate "Flavors"?

A `.lib` file contains many versions of each gate (like AND, OR, NOT) with different properties:

- **Performance:** Faster gates for critical paths, slower for power savings
- **Power:** Some gates use less energy
- **Area:** Smaller gates for compact chips
- **Drive Strength:** Stronger gates to drive more load
- **Signal Integrity:** Specialized gates for noise/performance
- **Mapping:** Synthesis tools pick the best flavor for your needs

### 6.Synthesis Lab with Yosys 
Lets synthesize the good_mux with yosys:

###  Step-by-Step Yosys Flow

1. **Start Yosys**
    ```shell
    yosys
    ```

2. **Read the liberty library**
    ```shell
    read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

3. **Read the Verilog code**
    ```shell
    read_verilog good_mux.v
    ```

4. **Synthesize the design**
    ```shell
    synth -top good_mux
    ```

5. **Technology mapping**
    ```shell
    abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

6. **Visualize the gate-level netlist**
    ```shell
    show
    ```
    

<img width="1920" height="1080" alt="good_mux synthesis  NETLIST op  yoysys" src="https://github.com/user-attachments/assets/135b7ec6-1376-46e2-bcfd-fd5b1141376e" />




