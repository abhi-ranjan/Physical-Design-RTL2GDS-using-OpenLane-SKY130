# Advance Physical Design RTL2GDS using OpenLane/SKY130
The following repository consists of knowledge gained and steps followed while doing the Advanced Physical Design Using [OpenLANE/SKY130](https://openlane.readthedocs.io/en/latest/) workshop. The [workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) focuses on the complete ASIC flow approach from RTL2GDS using open soucrce EDA tools such as OpenLANE/SKY130. RISC-V architechture is followed for designing the the core of [PICORV32A](https://github.com/YosysHQ/picorv32).
# Table of Content
  * [About RTL to GDSII Flow](#about-rtl-to-gdsii-flow)
  * [SKYWater130 PDK](#skywater130-pdk)
  * [OpenLANE](#openlane)
  * [Tools Used](#tools-used)
  * [AIM](#aim)
  * [Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#day-1---inception-of-open-source-eda-openlane-and-sky130-pdk)
    - [How to talk to computers](#how-to-talk-to-computers)
      - [IC Terminologies](#ic-terminologies)
      - [Introduction to RISC-V](#introduction-to-risc-v)
        * [RISC-V Characterstics](#risc-v-characterstics)
      - [Software to Hardware](#software-to-hardware)
        * [What happens when we run a program?](#what-happens-when-we-run-a-program)
        * [How does an application run on a computer?](#how-does-an-application-run-on-a-computer)
    - [SoC design and OpenLane](#soc-design-and-openlane)
       - [Introduction to Digital Design](#introduction-to-digital-design)
         * [What is a PDK?](what-is-a-pdk?)
         * [Environment Setup](#environment-setup)
       - [Simplified RTL to GDSII Flow](#simplified-rtl-to-gdsii-flow)
       - [About OpenLANE](#about-openlane)
    - [Getting familier to open-source EDA tools](#getting-familier-to-open-source-eda-tools)
       - [Contents of the OpenLANE Directory](#contents-of-the-openlane-directory)
       - [LAB Day 1](#lab-day-1)
       - [TASK 1: Finding the d flip flop ratio](#TASK-1-finding-the-d-flip-flop-ratio)
* [Day 2 - Good floorplan vs bad floorplan and introduction to library cells](#day-1---good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)
    - [Chip Floor planning](#chip-floor-planning)
       - [Utilization factor and aspect ratio](#utilization-factor-and-aspect-ratio)
       - [Concept of pre-placed cells](#concept-of-pre---placed-cells)
       - [De-coupling capacitors](#de---coupling-capacitors)
       - [Power planning](#power-planning)
       - [Pin Placement and logical cell placement blockage](#pin-placement-and-logical-cell-placement-blockage)
       - [Placement and routing](#placement-and-routing)
       - [LAB Day 2](#lab-day-2)
       - [TASK 2: Calculating area](#task-2-calculating-area)
* [Day 3 - Design library cell using Magic Layout and ngspice characterization](#day-3---design-library-cell-using-magic-layout-and-ngspice-characterization)
   - [LAB Day 3](#lab-day-3)
   - [Labs for CMOS inverter ngspice simulations](#labs-for-cmos-inverter-ngspice-simulations)
       - [Creating SPICE deck](#creating-spice-deck)
       - [Analysing the inverter](#analysing-the-inverter)
       - [LAB SETUP](#lab-setup)
   - [Inception of Layout Â CMOS fabrication process (16 mask process)](#inception-of-layout-Â-CMOS-fabrication-process-(16-mask-process))
   - [LAB DAY 3 (PART 2)](#labb-day-3(part-2))
   - [LAB DAY 3 (PART 3)](#labb-day-3(part-3))
       - [TASK 3: calculating delays and fall time](#task-3-calculating-delays-and-fall-time)
* [DAY 4 Pre-layout timing analysis and importance of good clock tree](#day-4-pre---layout-timing-analysis-and-importance-of-good-clock-tree)
   - [Pre-layout timing analysis and importance of good clock tree](#pre-layout-timing-analysis-and-importance-of-good-clock-tree)
# About RTL to GDSII Flow
RTL (Register tranfer level) to GDSII (Graphic Data Stream) flow consists of the complete set of steps required to create a file which could be sent for tapeout. The RTL code is synthesized and optimised. After sysnthesis of the code, PnR, floor and power planning is done while keeping in check the timing constraints. At the end GDSII file is written out.
The complete flow consists of following steps:
* Writting RTL
* Synthesis
* STA (Static Timing Analysis)
* DFT (Design for Testability) 
* Floorplanning
* Placement
* CTS (Clock Tree Synthesis)
* Routing
* GDSII Streaming

# SKYWater130 PDK
It is a Open source PDK (Process Design Kit) which is released by the collabration of Google and SkyWater Technologies foundary. Currently this technology has a target node of 130 nm. It is open to everyone and can be accessed at [SkyWater Open Source PDK](https://github.com/google/skywater-pdk). This PDK is extremely flexible as it provides many optional featurs as standard features. Hence it povide designers with wide range of design choice. 

# OpenLANE
It is an open-source VLSI flow created using open source tools. Basically it is collection of various scripts which invoke and execute these tools in right sequence, modifies inputs and outputs and gives an organised results.

# Tools Used
 | Tool | Used for |
 | ----- | ----- |
 | [Yosys](https://github.com/YosysHQ/yosys) | Synthesis of RTL Design |
  | ABC | Mapping of Netlist |
  | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA) | Static Timing Analysis |
  | [OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD) | Floorplanning, Placement, CTS, Optimization, Routing |
  | [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) | Detailed Routing |
  | [Magic VLSI](http://opencircuitdesign.com/magic/) | Layout Tool |
  | [NGSPICE](https://github.com/imr/ngspice) | SPICE Extraction and Simulation |
  | SPEF_EXTRACTOR | Generation of SPEF file from DEF file |

# AIM - The main objective of the ASIC Design flow is to take the design from RTL to GDSII format.

# Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK
  ## How to talk to computers
  ### IC Terminologies
  In the complete flow to this RTL2GDS physical designing there are lot of terminologies one comes across. Some of these terms are described below.
  * Package - ICs are basically presents as packages. These packages are materials which contains the semiconductor device. These packages protect the device from damage. these are of various kind. An example of QFN-48 (Quad Falt No-Leads) with 48 pins is taken here.
  
  ![package](https://user-images.githubusercontent.com/69652104/214497117-07e825ff-26dc-4a42-ac74-d103bdcaa6d5.png)

  * Chip - It sits in the centre of the package. The chip is connected to the package pins using **wire bond**. Inside the chip we have various components such as pad, core, interconnects, etc.
  * Pads - These are the itermediate structure through which the internal signals from the core of IC is connected to the external pins of the chip. These pads are organised as Pad Frame. There are different kind of pads for input, output, power supply and ground.
  * Core - It is the place where all the logic units (gates, muxs, etc) are presnet inside the chip. These are able to execute the set of instructions given to the chip and produce an output.
  * Die - It is the block which consists of semiconducting material and it can be used to build certain functional cuircuit which can be further sent for fabrication. It is the entire size of the chip.
  ![Die](https://user-images.githubusercontent.com/69652104/214504383-203376f6-4efe-40f3-a713-644c64ed1bbc.png)

  ### Introduction to RISC-V
  **[RISC-V](https://riscv.org/technical/specifications/)** is an open instruction set architechture rooted on **reduced instruction set computer principles**. It is an open source ISA used for processor design. 
  #### RISC-V Characterstics
  * It uses one clock cycle per instruction.
  * It follows the th RISC Princples. 
  * It has both 32-bit and 64-bit varients. It also support floating point instruction.
  * It avoids micro-architechture or technology dependent features.
  * It accelerates the time for design to reach the market as it uses open-source IP.
  
  ### Software to Hardware 
  The flow shows how the high level language (at software end) gets converted to machine language (at hardware end) and then gets executed on the package.
  #### What happens when we run a program?
  Suppose a C program needs to run on a hardware. So we nned to pass this C program to the hardware. So firstly the C program is compiled into assembly language (RISC-V assembly language program). Now this assmebly language is converted into the machine language program (basically 1's and 0's). Now this 1's and 0's are understanable by the hardware.

#### How does an application run on a computer?
1. The application software enters the system software (major component of it are OS, Compiler and Assembler). 
 * The OS handles I/O operations, memories and many low level functions.
 * then the program passes to Compiler which changes the program to Assembly language (compiled into instructions depends upon the hardware).
 * Now the instruction set goes to Assembler. Assembler converts the instruction set to machine language (binary numbers). 
2. The system software converts the apllication software into binary language. 
3. Now these binary numbers enter our chip layout and according the function is performed.

![image](https://user-images.githubusercontent.com/69652104/214534459-5706003b-cec0-4cf9-b8a9-73463cfb434e.png)

## SoC design and OpenLane
### Introduction to Digital design
For designing Digital ASIC ICs we require following components and some of it's opensource resources are also mentioned. 
 * RTL models (old IP's) {github.com, librecores.org, etc}
 * EDA tool {OpenROAD, OpenLANE, etc}
 * PDK Data {SKYWater 130} 
 
 In the workshop every component is used from sources which are open soucre. The following image gives an idea about each component as an open source resource. 

![image](https://user-images.githubusercontent.com/69652104/214576102-b48d6010-44a6-466a-b9cb-62adcb012c5e.png)

 
#### What is a PDK? 
PDK stands for Process Design Kit, it is provided by foundaries and it consists of library or set of building blocks which are used to build ICs. Each component in the library is seperate building bolck and ae made following certain foundary rules.

PDKs acts as an inteface between the FABs and the designeers. PDKs have collection of files whcih are used to model a fabrication process for the EDA tools used to design an IC. PDK consists of tecnology node information, Process Design Rules (to verify DRC, LVC, PEX, etc), device model, I/O libraries, Standard cell libraries, macros files, lef files, etc.

**Google along with SKYWater made the laters PDK opensource (130 nm node).** The PDK only need data information for successful implementation.

#### Environment Setup
The OpenLANE flow requires various open source tools as well as their supporting tools to be installed for the complete Physical design flow. Installing this tools one by one is tedious as well as one can get lost in the steps. Installation can be done easily using some set of scripts present in following repositories [VSDFlow](https://github.com/kunalg123/vsdflow) (for installing Yosys, OpenSTA, Magic, OpenTimer, netgent, etc)  and [OpenLANE Build Scripts](https://github.com/nickson-jose/openlane_build_script).

### Simplified RTL to GDSII Flow
   
![gnome-shell-screenshot-6miv9a](https://user-images.githubusercontent.com/69652104/214678043-5d9b90e2-e05b-42a4-a893-5ab572dd1ff0.png)

   The flow starts from the HDL code i.e.RTL model and ends with GDSII file. The major implimenation steps are:
   * Synthesis - During synthesis the HDL design is translated into circuits,  which are made up of components present in the standard cell library. The resultant circuit is described in HDL and its referred as gate level netlist which is functional equivalent of RTL code. The library building block of cell have regular layouts, cell layout is enclosed by fixed height (rectanglar in shape) whereas the width is variable an is discrete i.e., integer multiple of unit call side width.
                       
![image](https://user-images.githubusercontent.com/69652104/214678337-67f65f23-30e9-499d-b5d2-bcdb35758077.png)
   
   * Floor Planning - In Floor planning the chip area is being planned which in turn creates a robust Power distribution to power the circuits. The die is partitioned into different building blocks or components, also the I?O pads are distributed. During macro floor planning macro dimensions, it's pin locations and row definations i.e, rows and routing plan.
  
![image](https://user-images.githubusercontent.com/69652104/214678571-dbe1e73e-d3cb-4d71-87d3-74a53e9a503b.png)
   
   * Power Planning - The power network is constructed typically for a chip was it has to power multiple VDD and ground pins. The power pin are connected to all component through rings and multiple horizontal and vertical strips. Sach parallel structure is meant to reduce the resistance.
  
![gnome-shell-screenshot-12eny2](https://user-images.githubusercontent.com/69652104/214678807-208f9984-65ac-4df7-af16-a0399e19ee49.png)

   * Placements - For macros we place the GATE level netlist cell on vertical rows. To reduce the interconnect delay conical cells are placed very close to each other and this is also done to enable successful routing afterwards. Placement is done in two ways Global placement and detailed placement. Global placement provide optimal result and these may or may not be legal where as the detail placement is always legal.

![image](https://user-images.githubusercontent.com/69652104/214679067-e04bca1c-a4dd-4099-9feb-ab1cb5b1743f.png)

   * Clock Tree Synthesis (CTS) - Before signal routing clock routing is done so that the clock distribution is done to every sequential block. Clock distribution network delivers the clock to each of the sequential block. It is done so that there is minimum skew and latency. It usually follows a shape i.e., H-tree, X-tree, etc.

![image](https://user-images.githubusercontent.com/69652104/214679269-79bb1c1f-2f82-4857-9248-1f55c18ec41c.png)

   * Routing - The signal routing is done using metal layers. It is essential to find valid pattern of horizontal and verticle wires to implement the nets that connects the cells together. Router uses the available metal layers as defined by the PDK. For each metal layer the PDK defines the thickness, width, pitch and vias. Vias are used to connect two metal wires. SkyWater 130nm has 6 metal layers.

![image](https://user-images.githubusercontent.com/69652104/214679422-b3ec0d10-5a54-4203-b5b8-705d5245b8a2.png)

   * Verification and Sign-offs - After PnR and CTS we perform verifications, to check whether our layout is valid or not. These verifications consists of Physical verification such as DRC and LVS. Design Rules Checking (DRC) ensures that the layout follows the design rules and Layout Vs Schematic ensures that the final layout is as per the synthesised gate level netlist or not. Finally Static Timing Analysis is done (STA) to make sure that all the timing constraints are met by the circuit.
   
### About OpenLANE
[OpenLANE](https://openlane.readthedocs.io/en/latest/) is a flow which uses various open source tools for the RTL to GDSII flow. It has the striVe family of open everything SoCs (Open PDK, Open EDA, Open RTL). The various tools it uses are Yosys, OpenROAD, Magic, Netgen, SPEF_Extraction, etc.  

* It has two mode of Operations: Autonomus and Interactive
* It is tuned for SKYWater 130nm open PDK.
* OpenLANE ASIC flow is shown below. 
![image](https://user-images.githubusercontent.com/69652104/214687083-4fadeba1-1a31-4794-9c83-13f924430f3e.png)

* The flow starts with RTL Synthesis. RTL is fed to Yosys with the design constraints. Yosys translates the RTL into a logic circuit using generic components. 
* the circuit can be optimized and then mapped with standard cell library usin the tool abc. There are [abc scrript](http://people.eecs.berkeley.edu/~alanmi/abc/) to guide the optimization. OpenLANE has several abc scripts which has different synthesis statergies (least area, least power consumption, etc). The synthesis exploration utility is for statergy exploration and report generation.
* OpenLANE has design exploration utility which can be used to sweep the design configurations (16 in total) and it genrates reports which has different design matrix and also shows the number of violations in layout. It is used for regression testing and to find the best configuration of our design. 
* OpenSTA performs the Static timing analysis on the netlist which is generated during synthesis.
* Now after synthesis, the testing part starts (DFT) i.e., scan insertion, Automatic Test Pattern Generation (ATPG), Test Pattern Compaction, Fault Coverage and Fault Simulation. This step is optional.
* Nest step is Physical implementation. This part is done with the help of OpenROAD application. It performs PnR which consists of FP+PP, Placement (Global and Detailed), Optimization, CTS and routing (Global and Detailed). TritonRoute is used for detailed routing.
* Logic equivalence checking (LEC) is performed as the circuit changes due to optimization process as compare to the one generated during synthesis. This is done using Yosys tool to make sure the functionality is equivalent. 
* During insertion there is a special step that is fake antenna insertion. It is required to address the antenna rule violations. The concept of fake antenna is something like we have already considered the antenna so that on later stage we do not have any antenna violations. Hence we add fake antenna diode next to every cell input after placement. Then antenna checker is run from the Magic tool against the layout. 
* Fake antenna diode cell is created and added to standard cell library. 

/*hence a long wire is simulated it acts as an antenna but as a conductor it collects charges which can damage the transistor gates connected to the wire during fab. So the length of wire connected to transistor gate must be limited. This is done by the help of Router.*/

* The sign off include STA, DRC and LVS. It also involves interconnect RC extraxtion from the routed layout followed by STA using OpenSTA.
* Physical signoffs include DRC and LVS. DRC and LVS is performed using Magic tool. Circuit extraction is done NetGen.

## Getting familier to open-source EDA tools
### Contents of the OpenLANE Directory

The following content is specific to the workshop. There are lot of other files present in the directory too.
1. **OpenLane folder** - It contains all the tools and the file that need to be invoked during the flow.
2. **Designs** - This folder consists of all the designs requried during the flow (picorv32a is the design used in this workshop)
3. PDKs - This folder contains all the pdk related files as well as information. (open pdk, Sky130, Skywater pdk).
* open pdk consists of the scripts.
* sky130A pdk consists of the libs.ref (has files specific to process such as timing, lef-both tech and cell) and libs.tech (has all the files specific to the tool) files.
* skywater pdk consists of skywater 130 nm pdks.

**NOTE:** - Here **sky130_fd_sc_hd libs.tech** is being used.
4. config files - It bypasses any configuration that has already been done i.e., many of the switches use default value that is already present in the OpenLane flow.
**The precedence order of Openlane settings are:**
* sky130_xyz_config.tcl
* config.tcl
* Default value (already set in OpenLane)

### LAB Day 1

**Step 1:** Starting OpenLane
* Go to openlane folder.

```
cd work/tools/openlane_working_dir/openlane
```

* Then run the **docker** command.

```
docker
```

* Now run the **flow.tcl** file with interactive mode.

```
./flow.tcl -interactive
```

* Now import packages

```
package require openlane 0.9
```
![image](https://user-images.githubusercontent.com/69652104/214776839-62677619-ff6f-40f0-8ce1-d13500bb9491.png)

* Now we are good to go to execute our commands.

**NOTE** - The above commands are to be run everytime we use OpenLANE for RTL2GDSII flow.

**Step 2:** Design Preperation
* Knowing the contents of our design (picorv32a) folder.

1. src
2. sky130A_sky130_fd_sc_ns_config.tcl
3. sky130A_sky130_fd_sc_ls_config.tcl
4. sky130A_sky130_fd_sc_hs_config.tcl
5. sky130A_sky130_fd_sc_hdl_config.tcl
6. sky130A_sky130_fd_sc_hd_config.tcl
7. config.tcl

Checking our config.tcl file values by running the below command in picorv32a folder (it has clock period of 5 unit) 

```
less config.tcl
```

![image](https://user-images.githubusercontent.com/69652104/214781041-9641cea7-25be-45f5-8faa-d1fcf7792781.png)

* Creating file for our design i.e., setting up the design. It merges the cell LEF files and the technology LEF files generating merged.lef which is present in the temp folder.

```
prep -design picorv32a
```

![image](https://user-images.githubusercontent.com/69652104/214783771-fdd2623b-1b9c-4a92-acd2-633521396d50.png)

This marks the creation of new folder inside picorv32a named as runs folder which consists of new folder whose name is the date on which the command is run. The following folder has results, reports, command logs, PDK Sources, etc files.

![image](https://user-images.githubusercontent.com/69652104/214785655-161be74d-583d-4241-8c5f-581fecd4f96f.png)

**Step 3:** Running Synthesis

Yosys synthesis is run when the command for synthesis is entered. Along with it abc scripts are also run and OpenSTA is also run.

```
run_synthesis
```

After running systhesis logs, reports and results are created.

The report folder have the following files: 
1. 1-yosys_4.chk.rpt
2. 1-yosys_4.stat.rpt
3. 1-yosys_dff.stat
4. 1-yosys_pre.stat
5. 2-opensta.min_max.rpt
6. 2-opensta.rpt
7. 2-opensta.slew.rpt
8. 2-opensta.timing.rpt
9. 2-opensta_tns.rpt
10. 2-opensta_wns.rpt

Also a netlis file is created in the results --> symthesis folder named **picorv32a.synthesis.v**

### TASK 1: Finding the d flip flop ratio

Count of d flip flop (sky130_fd_sc_hd_dfxtp_2) = 1613 

![image](https://user-images.githubusercontent.com/69652104/214802678-3f3dfeb9-047d-453c-a5cc-746851bd6b7e.png)

Number of cells = 14876 

![image](https://user-images.githubusercontent.com/69652104/214803214-dc216e45-52d6-49e2-84d6-80f61c3cd41b.png)

**flop ratio = count of d flip flops / number of cells = 1613/14876 = 0.108429 (10.8429 %)**

The synthesis statisttics report is as follows: 

![image](https://user-images.githubusercontent.com/69652104/214804520-5c19f30a-3315-40b1-9f66-cb5d6c9f6189.png)

# Day 2 - Good floorplan vs bad floorplan and introduction to library cells

## Chip Floor planning
Here we try to come up with the width and height of the chip.

### Utilization factor and aspect ratio

1. Determining width and height of the core and die
 While defining the dimensions of the chip we are mostly dependent on the dimensions of the logic gates (standard cells) sitting in the netlist. <!-- Let's try to give a proper length and width to all the standard cell (say rectangular to all), so we are intreseted in the dimesions of the standard cell not the wire for core and die. Lets say the std cell have dimension of 1 uint to 1 unit for each std cell with the help of this we will try to find the area utilised by the cells. So we bring all the cells together ignoring the wires and we can calculate the total area as well as dimensions. Now we have rough idea about the area of our netlist. So now place all the logic cells in the core.so we get the utilization of our core. --> 
2. Core is the section where fundamental logic is being place whereas a die is a small semiconductor material specimen on which the fundamental circuit is fabricated and it consists of core. 
3. Once the logic is placed in the core it utilizes certain amount of core which is characterised by utilization factor (Area occupied by netlist / total area of the core). If utilization factor = 1 it means 100% utilization, hence no extra cells could be added. Therefore in a practical scenario the core utilization factor is always less than 1. Hence we generally go for 50-60% utilization. utilization factor = 0.5-0.6)
4. Another important consideration is aspec ratio ((height)/(width) of the core). If aspect ratio = 1 it means the chip is square in nature. 

### Concept of pre-placed cells

Generally the utilization factor is less than 1, hence we have some un used section of core. These unused section of the core is used for optimization and other things. In the unused section we place additional cells, used for routing, etc. 

Pre-placed cells - It is based on the concept of reusable modules or IP's. These blocks need not be implemented everytime we need to use it, these blocks are functionally implemented sometime in the past i.e., only once (for eg - memory, clock-gating, vomparator, mux,etc). These cells are called pre-placed ceels. It's needed to define the placement of these cells or IP's in a chip before routing. These have fixed places on the chip defined by the user. Since these cells are placed before placement and routing hence these are called Pre-placed cells. The pre-placed cells are being placed on a core depending on the design scenario. Automated placement and routing tools does not touch these cell positions. <!-- These a certain combinational logic (mux, freq divider, etc). These logic does such a big task that is has 100000s of gate. So there is a way by which we need not develop this circuitary everytime but we can take a piece of the circuit and implement it seperatly by dividing the circut. -->

![image](https://user-images.githubusercontent.com/69652104/214826732-7fc3691c-3c26-43a6-9a0e-98c05adcdc15.png)

### De-coupling capacitors

The pre-placed cells needs to be surrounded by De-coupling capacitors.  Whenever there is a switching in a circuit there is amount of current required because basically there is small capacitor present at each node, so (switching from 0 to 1) means the capacitor has to charge to represent logic 1 and the amount of charge is sent from the supply voltage. So, it is responsiblity of the supply voltage to supply the amount of current to the switching logic. Also while 1 to 0 switching it is the responsibility of VSS to take all the charge hence thre is discharge current. Now due to wire resistance, inductance there is a drop in the voltage across the wire during flow of current. There is a drop because these wires are vias hence it has physical dimensions hence it has res, cap, inductance etc. Now due to the drop there is VDD' at the node i.e., instead of 1V (VDD) we have 0.7V (VDD')  The 0.7 V (VDD') should be in our [noise margin range](https://www.electronics-tutorial.net/digital-logic-families/noise-margin/). So, if the VDD` lies in the noise margin range then we are safe but sometimes we can be unsafe. To ensure the safety we use decoupling caps, these are huge caps filled with charge and the equivalent voltage across the caps are same as supply voltage. Hence the required current is provided by this caps. This caps de-couples the circuit from the main supply whenever there is switching. Now we have taken care of local communication. Next we need to focus on global communication.

![image](https://user-images.githubusercontent.com/69652104/214868141-ead577c3-369f-4618-831f-b7c4bcd4c41f.png)     ![image](https://user-images.githubusercontent.com/69652104/214861008-10fbce91-0887-4b87-8ecb-a2857d4b37e4.png)

### Power planning

There a lot of macros on a chip and if each macro has it's current demand so there will be lot of de-coupling capacitor which is not feasible. Therefore, some critical blocks are decoupled using de-coupling cap but not for each element. There is always a posibility of voltage drop at certain node in the circuit. Suppose there is a 16 caps connected in parallel and all are going to switch from logic 1 to logic 0 i.e., caps are discharging and all this connected to same ground. All of these caps discharge at same time then there is a ground bounce (bump at the ground) and if the size of the bump exceeds the noise margin then it might enter into undefined state. Also when all the caps try to charge from 0 to 1 then there is a voltage droop (demanding of power at the same time) and again the noise margin concept applies here too. 
This problem happens only because there is power supply at one point, if there is power supply present in the entire perpherry then this problem is resolved. Hence solution to above problem is multiple power supply and ground. Therefore we have multiple VDD and VSS ports.

![image](https://user-images.githubusercontent.com/69652104/214900463-5e79bce2-a802-440e-86d2-7a25f999fe30.png)

### Pin Placement and logical cell placement blockage

The connectivity information betweer the gates is codded using VHDL/Verilog language and is called as the netlist.

There are pre-placed cells already present in the core. The area between the core and die are filled with the input and output ports. The I/O ports placement depends upon the cells connected to these ports as well as the pre-place cells. The clock ports are bigger in size as these are continously driven pins and these drives the complete chip. So we need the least resistance path for the clocks hence clock pins are thickers.
 
 ![image](https://user-images.githubusercontent.com/69652104/214906268-ef591691-ea25-4da1-a155-3ef1b9b1c055.png)

After pin placement it should be made sure that the remaining empty area between the core and the die is blocked. Therefore logical cell placement bloackage is done. Hence it ensures that automated placement and routing does not place anything in this area.

![image](https://user-images.githubusercontent.com/69652104/214906520-25443bef-e78a-4edf-9b71-e592b91424c2.png)

**Voila!! we are done with Floor and Power Planning.**

**NOTE: Standard cell placement happens in placement stage.**

### Placement and routing

* Netlist binding and initial place design
1. Lets suppose the shape of the gate determine the functionality of the gate but in reality each gate is a black box. Hence we take each gate and give them a physical dimensions. This is done for each of the component of the netlist. These cells are present in a library which consists the following  information and files shape and size, delays, various flavour of the cells and the timing information. Library akso orvide options with different delay and sizes. Particular cell is chosen as per our requirement.

![image](https://user-images.githubusercontent.com/69652104/214969249-20d0f22a-4c0d-4f70-b9f5-392cb42a2c2a.png)

2. Next step is to take the particular shap and sizes and then place it on the floorplan. The pre-placed cells are not disturbed. During placement logical connectivity is maintained as well as the placement is done in such a way that optimized path is formed (i.e., blocks placed close to there input and output) 

3. Till now we have kept o/p port near the output and input port near the input. Now using some estimations we will try to do optimized placement. We can try to estimate the capacitances and resistance b/w two point. The wirelength will form a resistanace which will cause unnecessary voltage drop and a capacitance which will cause a slew rate that might not be permissible for fast current switching of logic gates. Successfully transmitting the signal from one place to another without any loss is known as signal eintegrity. To maintain the signal integrity we require repeaters (kind of buffers) and these are inserted as per the wire length and capacitance and based on these cap and resistance, a waveform is genrated and the transition of the waveform should be in permisible range. But now we have loss of area. Hence where integrity is maintained there we do not place any repeaters but if integrity not maintained the we insert the buffer (repeater). We need to come to a conclusion with minimum number of repeater. Sometime we also do abutment where logic cells are placed very close to each other (almost zero delay) if it has to run at high frequency (2GHz). Crisscrossing of routes is a normal for PnR and it can be avoided by use separate metal layer (using vias) for crisscrossed path.

    Based on ideal condition of the clock (time required by clock to reach a component is 0) we will do setup timing analysis and based on this we will       check our placement condition is meeting the given specification or not.

    Placement in OpenLANE is done in two stages:

* Global Placement - It's main job is to reduce the wire length. It is generally a coarse placement. Here no leaglization happens. Here the concept of **HPWL** (Half Parameter Wirelength) reduction model. 

* Detailed Placement - legalization happens here the std. cells are placed in std cell rows. {legalization - They shoulde be exactly inside the row and the should be abutted on each other and there should be no overlap}.

The main ain of placement now is congestion, it is not the timing analysis.
The next step will be CTS. 

Placement before buffer insertion: 

![image](https://user-images.githubusercontent.com/69652104/214977372-22400c76-ebfe-4d5f-b4c9-0715352351b0.png)

Placement with buffers

![image](https://user-images.githubusercontent.com/69652104/214977547-4198edb6-b2e9-49d1-888d-4c75422d5aa9.png)

Our objective is to converge the value of Overflow (it is present below HPWL value during run_placement. If the value of overflow decreases our design will converge. Now we can see the generayed .def file in the placement folder under results using the Magic tool.

<!-- Slew is dependented upon the value of cap. The higher the value more is the charge required to charge the cap and the slew will be bad Abutment has lot of advantage STA is done to know maximum achievable frequency-->

**NOTE: ** Collection of gates in an area is called as library. 

## Cell design and characterization flows

In IC design flow a library is a place where we keep all our standard cells, buffers, decap cell, etc. The library does not only have different cells with different functionality but it also have same cell with different sizes, threshold voltage, delays, etc. 

* Examining an inverter

Cell design flow is divided into three parts: `inputs`,`design steps` and `outputs`.
1. Inputs - Inputs to design an inverter is basically the PDKs which consists of DRC & LVS rules, SPICE models, library and user-defined specs.

  - DRC & LVS Rules - These are the technology rules defined by the foundary. tech files and poly subtrate paramters (CUSTOME LAYOUT COURSE)
  
  - SPICE Models - These consists of all the parameters based on the foundary for eg: Threshold voltage, linear regions, saturation region equations with added foundry parameters. Including NMOS and PMOS parameteres (Ciruit Deisgn and Spice simulation Course)
  
  - User defined Spec = These are the specifications given by the user which is to be achieved by following the DRC and LVS rules. Maintaining Cell height (separation between power and ground rail), Cell width (depends on drive strength), supply voltage(provided by top level, keep noise margin in check), metal layer requirement (which metal layer the cell needs to work), pin location, drawn gate length, etc.
  
<!--higher the drive strength lower wire it can drive.-->

Now we have all the inputs with us (available with the library developers). Now the developer should take the input and come up with std cells that adheres to these specs and rules.

2. Design steps - It has three different steps: `circuit design`,`layout design` and `characterisation`.
  - circuit design includes the implentation of the logic and the modeling W/l ratio of NMOS and PMOS.
  - Implementation of the circuit description language (i.e., output of the circuit design) is called layout design. 
 
 ```
  Steps in layout design:
  1. Get the function implemented using CMOS.
  2. Get a PMOS and NMOS network graph out of the implemented circuit. (using Euler's method)
  3. Obtain the Euler's path, it is a path that is traced only once.
  4. Draw the stick diagram.
  5. Convert the stick diagram into layout adhering with the DRC rules given by the foundary. 
  
  The layout is generated using Magic tool .Now we have the cell width and cell height and all the cells adhere to the rules.
 ```
 We can extract the parasitics from the layout and we have characterise it with respect to timing.
 
 3. Outputs - The output of the layout desgn is GDSII. Lef defines the width and height of the cells. It also gives extracted spice netlist. 

Now we will do chararacterisation and we will generate timing, noise, power libs function. Here we will try to understande various syntax and symantic of timing.lib, power.lib and noise.lib. These syntax are important to understand the GUNA software i.e., the characterisation software because software works on these variables and these are the variables present with us in order to feed into the software. 

* Timing charaterisation 

![image](https://user-images.githubusercontent.com/69652104/215085932-a7f32902-9301-41a1-9f64-3a51c48022ef.png)

Here we first understand different threshold point of waveform itself called as timing threshold defination.

Consider the above two inverter figure and understand the graph below. red curve - input to the circuit at 2nd inverter, blue curve - output of the circuit after 2nd inverter. We have slew deniation shown in the figure below for both rising and falling edge. 
With help of all the timing threshold defination we are able to calculate our slew as well as the propagation delay.

![image](https://user-images.githubusercontent.com/69652104/215090838-a064c684-1e07-4b99-8d5c-0b47b19bbdfe.png)

Similarly we have threshold for the delays (rise and fall) as we had for slew hence we analyse the waveform for the delays. 

![image](https://user-images.githubusercontent.com/69652104/215091498-c0d76565-9b7b-41fa-9c04-6c20fe0cd85b.png)

Getting a negative propagation delay is highly unexpected. A negative propagation delay means that the output comes before the input. Hence to avoid negative propagation delay we as designer need to choose correct threshold points which eventually leads to positive delays. Propagation delay threshold is usually 50% and slew rate threshold is 20-80%.

### LAB Day 2

Continuation after synthesis.

**Step 1:** Running floorplan
* We have lot of switches with which we adjust the flow directory. These switches are used to set certain parameter in each stage of the flow. For eg: In the Floorplanning stage we have `FP_CORE_UTIL {for utilization percentage}`, `FP_ASPECT_RATIO {sets the aspect ratio}`, `FP_CORE_MARGINS {offset b/w die boundary and core boundary}`, etc. We have certain .tcl file in OpenLane which has these switchs that sets these specifications. 

```
├── README.md      

├── checkers.tcl

├── cts.tcl

├── floorplan.tcl  

├── general.tcl

├── lvs.tcl

├── placement.tcl

├── routing.tcl

└── synthesis.tcl

```
 
**floorplan.tcl contains the following default switchs**

![image](https://user-images.githubusercontent.com/69652104/214922065-9a283cce-6623-42b9-b754-94a26c2e3e1c.png)

* The command to run floorplan is:

``
run_floorplan
``

**Step 2:** Review floorplan files and steps to view floorplan

* Reviewing files

Here basically the ceated files are being checkd using the log files presen in the `log/floorpla/4-ioPlacer.log`. In case it is not there we can check it using the Magic tool.

 - for floorplan the core utilization is 50%
    
![image](https://user-images.githubusercontent.com/69652104/214932840-71d0969f-196f-4e3f-a163-141fdc0b8174.png)
 - for config.tcl file under the runs folder core utilization is 35%.
    
![image](https://user-images.githubusercontent.com/69652104/214932703-7cd63e42-bc32-4ffd-9cfa-4ca511f0e38d.png)
    
 - setting the core utilization, verticle and horizontal metal layer by add these three switchs in the config.tcl file

```
set ::env(FP_CORE_UTIL) 65
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3
```
    
* Viewing Floorplan

The def (design exchange format) file is created in the floorplan folder of the results folder under runs folder. This file has the information about the die area. This gives the co-ordinates of the die and the unit is databse unit per micron i.e. 1 micron = 1000 database units)

![image](https://user-images.githubusercontent.com/69652104/214935860-e584d395-802b-4f7c-9e8e-2e3e3e7efbee.png)

The die co-ordinates and other information can be viewed using following command invoked under the picorv32a folder.

```
cd runs/[date]/results/floorplan/picorv32a.floorplan.def
```

**NOTE: 1 micron is equivalent to 1000 database units**

### TASK 2: Calculating area
**Calculating the die area = (660685 / 1000) x (671405/1000) = 443587.2124 um <sup>2</sup>**

**Step 3:** Review floorplan layout in Magic

* Using Magic tool to view the def file 

The following command can be used to invoke magic tool as well as open the def file:

```
magic -T /home/nickson/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &f
```

To center the view, press "s" to select whole die then press "v" to center the view. Point the cursor to a cell then press "s" to select it, zoom into it by pressing 'z". Type "what" in `tkcon` to display information of selected object. These objects might be IO pin, decap cell, or well taps as shown below.

The genrated file is shown below: 

![image](https://user-images.githubusercontent.com/69652104/214947957-92a584f1-e87d-4fcb-a176-dd2f0b992401.png)

The horizontal and verticle pins:

![image](https://user-images.githubusercontent.com/69652104/214963933-767ae5f6-a79d-4460-814b-a3b2bba33853.png)

The decap cells: 

![gnome-shell-screenshot-45vknb](https://user-images.githubusercontent.com/69652104/214964586-21457bd5-c1a5-4f51-a5aa-467eaed4e7d9.png)

The diagonally equidistant tapcells: 

![image](https://user-images.githubusercontent.com/69652104/214965422-0c9ffde3-c770-4f84-b8d8-5c0c3af134ba.png)

The standard cells in the bottom left corner:

![image](https://user-images.githubusercontent.com/69652104/214965088-1ae98625-9e9c-4936-a235-017b543f6704.png)

**Step 4:** Running Placement

The following command is used to run placement.

```
run_placement
```

During place a number of tools such as RePlace tool (for global placement), Resier tool (for optimization) and OpenDP (for detailed placement) is invoked. If the value of overflow converges then the design is legal.

* Using Magic tool to see the layout of this stage.

```
magic -T /home/nickson/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

The genrated layout:

![image](https://user-images.githubusercontent.com/69652104/215020126-f1327f84-003f-4238-80ea-bd78921117b0.png)

Placement ensures that the standard cells are correctly placed. 
PDN is created during floorplan. But is Openlane there is a post floorplan, post placement and CTS is done for PDN.

# Day 3 - Design library cell using Magic Layout and ngspice characterization

## LAB Day 3 (Part 1)

## Labs for CMOS inverter ngspice simulations
Here we will be dive deep into the flow. We will take a .mag file and do post-layout simulation in ngspice. After post-characterising we will be plugging this cell into the openlane flow i.e., into picorv32a core. 

* IO Placer revision
Earlier we had equidistant placed input/output pins. Now lets say we want to change it to some othe input/output pin statergy (there are four statergies supported by IO Placer - the tool that we use for IO placement). So we can change the switch (variable) and this will change the statergy and this can be done directly by setting the variable through the terminal and then re-run the floorplan. 

```
EXAMPLE - changing PIN configuration
set ::env(FP_IO_MODE) 2;
run_floorplan

Then check the layout by launching Magic again.
```

* SPICE deck creation for CMOS inverter

Here we will do SPICE simulation and deriving the charactestic on real time MOSFETs.  

#### Creating SPICE deck

1. SPICE Deck - It is a connectivity information about a cell. It is a netlist. It has the inputs, tap points, etc.

2. We need to define the component parameter i.e., value for PMS and NMOS. For us value of W/L of PMOS M1 (0.375u/o.25u) and NMOS M2 (0.375u/0.25). Ideally PMOS should be 2 or 3 times wider than NMOS. The load cap is assumed to be 10 fF. 

3. We assume an input supply voltage value (GATE) as 2.5 V and main supply voltage (at drain) as 2.5 V. Generally the supply voltage (GATE) is multiple of length.

4. Now we need to identify the node (those two point in b/w there is a component) and name these nodes.

The SPICE Deck is written below: 

```
*** MODEL Description ***
*** NETLIST Description ***
M1 out in vdd vdd pmos W=o.375 L=0.25 *** [component name] [connectivity] [drain] [gate] [source] [substrate] [type] [dimensions W/L] ***
*** Similarly for NMOS ***
M2 out in vdd vdd nmos W=o.375 L=0.25
*** load cap connecivity and value [name] [node1] [node2] [value] ***
cload out 0 10f
*** Supply voltage [name] [node1] [node2] [value] ***
Vdd vdd 0 2.5
*** Input voltage [name] [node1] [node2] [value] ***
Vin in 0 2.5
*** Simulation Command ***
.op
.dc Vin 0 2.5 0.05 *** Sweeping gate input form 0 to 2.5 at steeps of 0.05  VTC curve***
*** describe the model file ***
.LIB "tsmc_025ummodel.mod" CMOS_MODELS
.end
```
![image](https://user-images.githubusercontent.com/69652104/215171406-497c0875-89da-400a-b95c-c01d4fe298eb.png)

First invoke the ngspice and then run the following command to simulate:

```
source [filename].cir
run
setplot 
dc1 
plot out vs in 
```

#### Analysing the inverter

* Vm (switching threshold voltage) - The point where exact transition takes place i.e., Vin = Vout. At this point both the MOS are in saturation and we have a high leakage current (direct current flowing from vdd to ground). If the pull up network is strong the VTC moves towards right (Vm' > Vm) and if pull down network is strong then VTC shifts leftwards (Vm' < Vm).

**Formula for Vm**

![image](https://user-images.githubusercontent.com/69652104/215190239-b570e8c4-41c6-4bae-a399-2f27f1114903.png)

* Propagation delay - The difference between the time when output as well as input is at 50%. ( o/p falls and i/p rises gives fall delay, o/p rises and i/p falls gives us the rise delay)

* We can furter do transient analysis.

### LAB SETUP

* We will first git clone one of the repo (it is custom made for the workshop). Here we have .mag file for INVERTER, model file for sky130nm PMOS and NMOS. We will creat a ful view cell and then we will plug it into our flow. 

The command for git clone is (run it while you are in the openlane directory):

```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

![image](https://user-images.githubusercontent.com/69652104/215198686-f1b96d3a-a348-464b-a9b7-ba996d51ca7d.png)

It will create vsdstdcelldesign design folder. 

We need to have the tech file to open the mag file. We will copy the tech file to our directory. the tech file is present in the sky130A which is inside pdks folder. 
The tech file is present at this location `work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic`.

```
cd work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/
```

To copy go to the location and then type the command given below with target location

```
cp sky130A.tech /[target location]

target location for our case - /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

```

Now invoke magic tool in the vsdstdcelldesign folder to see the mag file i.e., layout of the inverter.

Command 

```
magic -T [tech file] [.mag file]
tech file = sky130A.tech .mag file = sky130_inv.mag
```

The generated layout:

![image](https://user-images.githubusercontent.com/69652104/215205628-74e0252a-8d08-4697-b3fc-cb93d1dfc547.png)

## Inception of Layout Â CMOS fabrication process (16 mask process) 

1. Selecting a substrate 
2. Creating active region for transistor 
3. N-Well and P-Well Fabrication 
4. Formation of Gate
5. Lightly Doped Drain formation
6. Source and Drain Formation
7. Form Contacts and Interconnects
8. Higher Level Metal Formation 

## LAB DAY 3 (PART 2)

**Step 1 : Characterisation**
We try to analyse the layout part by part using the `what` command in tkcon window. 

![image](https://user-images.githubusercontent.com/69652104/215220543-9ba085f3-cb83-439f-aed3-19d0cdb946cd.png)

lef (library exchange format) - it has all the information about metal layers. It also protect the IP.

def (design exchange format)

To implement the complete CMOS inverter click [here](https://github.com/nickson-jose/vsdstdcelldesign).


Magic is interactive DRC tool. If we have DRC it will show automatically. For our design we do not have any DRC violation. We need to ensure that our final design in DRC clean. 

To know the logical function of the inverter we first extract the SPICE. Post that we will do simulation the file using ngspice.

To extract it on SPICE, type these command in the tkcon window: 
* create an .ext file - `extract all` (extracted in vsdstdcelldesign folder)
* We will use this ext file to buide our SPICE file which can be used with the ngspice tool. Doing this will extract all the parasitics  too.

![image](https://user-images.githubusercontent.com/69652104/215221055-ec9e80d7-02c2-4112-bb3d-30871cf4dffa.png)

```
ext2spice cthresh 0 rthresh 0 
ext2spice
```

* seeing whats inside the spice file. 

```
vim sky130_inv.spice
```

![image](https://user-images.githubusercontent.com/69652104/215221808-7a0cb458-9db6-488f-b3b2-039efd5c45c7.png)

So our stand cell and extracted SPICE model is now present with us.

## LAB DAY 3 (PART 3)

The above SPICE model give connectivity information of our inverter. Now for transient analysis we have to define the connections.   
* We want VGND to be connected to VSS
* We want supply voltage (VDD) to be connected form VPWR to VSS (ground).
* So we create a node 0 and give VDD = 3V. 
* Then we give pulse voltage between A and VGND (VSS). 

We need to ensure that scaling is proper. (set to grid value specified in the layout). We can check the dimension of a grid in layout by the command `box`.

Add these command to our SPICE DECK

```
.option scale = 0.1u //set scale to 0.01 u.
.include ./libs/pshort.lib
.include ./libs/nshort.lib
// comment out the .subckt line
// change name of model from pmos (sky130_fd_pr__pfet_01v8) to pshort_model and from nmos(sky130_fd_pr__nfet_01v8) to nshort_model
VDD VPWR 0 3.3V //supply
VSS VGND 0 0V //ground
Va A VGND PULSE (0V 3.3V 0ns 0.1ns 0.1ns 2ns 4ns)
// comment .end
//transient 
.tran 1n 20n
.control
run
.endc
.end
```

**error**
![image](https://user-images.githubusercontent.com/69652104/215266807-fc95ee0d-1334-47a4-9693-8862527b4e59.png)


It gave us error that subckt hence I referred to the following link for exact SPICE file [here](https://github.com/AngeloJacobo/OpenLANE-Sky130-Physical-Design-Workshop#floorplan-stage).

```
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

* .subckt sky130_inv A Y VPWR VGND
M0 Y A VGND VGND nshort_model.0 ad=1435 pd=152 as=1365 ps=148 w=35 l=23
M1 Y A VPWR VPWR pshort_model.0 ad=1443 pd=152 as=1517 ps=156 w=37 l=23
C0 A VPWR 0.08fF
C1 Y VPWR 0.08fF
C2 A Y 0.02fF
C3 Y VGND 2fF
C4 VPWR VGND 0.74fF
* .ends

* Power supply 
VDD VPWR 0 3.3V 
VSS VGND 0 0V 

* Input Signal
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

* Simulation Control
.tran 1n 20n
.control
run
.endc
.end
```

After editing we launch the ngspice to see the values:

command

```
ngspic [spice file] // our case sky130_inv.spice
```
![image](https://user-images.githubusercontent.com/69652104/215251586-946a97dc-6b1e-4e22-9943-344433153f1b.png)

We can now see the plots (inside ngspice type the command below:

```
plot y vs time a
```

The transient plot is shown below:

![image](https://user-images.githubusercontent.com/69652104/215252884-27d233a9-7183-4a44-b84d-3db002703829.png)

Characterisation involves four parameters: 
1. rise transiton  - time taken by output waveform to transit from 20% to 80% of VDD 
20% value (0.66) = 2.1829 ns

![image](https://user-images.githubusercontent.com/69652104/215253347-7c5ea08b-ecd0-4ed6-a023-f7fe99f07a75.png)

80% value (2.64) = 2.24407 ns

![image](https://user-images.githubusercontent.com/69652104/215253463-4639c6c9-efbe-4372-be69-cc688e4e052b.png)

Hence rise time = 2.24407 - 2.1829 = 0.06117 ns

### TASK 3: calculating delays and fall time
2. fall transition - time taken by output waveform to transit from 80% (2.64) to 20% (0.66) of VDD.

![image](https://user-images.githubusercontent.com/69652104/215253711-a3c26cd9-45f4-4e48-bf73-a1f01ebd31c6.png)

fall time = 0.02725 ns

3 & 4. Propagation delay - The difference between the time when output as well as input is at 50% (1.65). ( o/p falls and i/p rises gives fall delay, o/p rises and i/p falls gives us the rise delay)

* fall delay:

output falling (50%)

![image](https://user-images.githubusercontent.com/69652104/215254037-aa53576a-3113-4817-a46b-6b70b0f851e4.png)

input rising (50%) 

![image](https://user-images.githubusercontent.com/69652104/215254068-e9cdfaee-31de-4978-88c8-8d48971b7ab3.png)

Therefore delay = 8.07761 - 8.05075 = 0.02686  ns

* rise delay:

output rising (50%)

![image](https://user-images.githubusercontent.com/69652104/215254229-004dfa0a-f99f-435e-8727-b96016d9c159.png)

input falling (50%) 

![image](https://user-images.githubusercontent.com/69652104/215254264-0826646a-d647-4806-9493-395205d94c98.png)

Therefore delay = 6.15075 - 6.15 = 0.00075 ns

The above characterisation is done at 27 C.

Next objective is to use this layout of inverter to create a lef file. Using this lef in openlane and plugging this cell we will make a custom cell. We will plug this in picorv32a.

**Step 2: DRC rules analysis**

To know more about Magic and the command for DRC visit the following [link](http://opencircuitdesign.com/). Technology files have all the technology related file. It consists all information about the layer, pattern, electrical connectivity, GDS generation rule, DRC rule, all other kind of rules, etc. Tnformation about the technology files can be found [here](http://opencircuitdesign.com/magic/index.html). 

**NOTE:**
cif - caltech intermediate formate - It is used interchangably with gds in magic tech file and documentation. Read [through the website](http://opencircuitdesign.com/magic/index.html) for [DRC rules](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#rules-periphery--page-root). The basic DRC rules are called edge based rules. 

1. We will download the required DRC_test files using the command.

```
wget http://opencircuitdesign.com/open_pdks/archive/drc_test.tgz
```

Upon extraction we find that there are .mag files and sky130A.tech file.

![image](https://user-images.githubusercontent.com/69652104/215258627-c188c40b-d069-4bd2-942c-ffa0b306b45e.png)

Now we can use magic to analyse the DRC rule and fix it if it's violated.
<!--- magic is invoked here by using the following command magic -d XR --->
# DAY 4 Pre-layout timing analysis and importance of good clock tree

## LAB DAY 4 (PART 1)
## Pre-layout timing analysis and importance of good clock tree

<!---Till now we have done design setup, floorplan, placement and lastly we had mage a SPICE model and its characterisation given a .mag file--->

OpenLANE is a Place and Route flow and for placement of any cell we do not require the entire mag file information. .mag file has all the information the power, ground, logic, metal, etc. For PnR do not require all the information. Only info we need is the PR boundary, power rail, ground rail, input and output. Here lef files come into picture. lef files has only these information. It protects our IP.

* Now our objective is to extract the lef file from .mag file and then try to plug the lef file to picorv32a. (that is instead of std cell we will use our own design)

* Guidlines for std cell set making from PnR point of view.
1. The input and the output port must lie on the intersection of verticle and horizontal tracks.
2. Width of standard cell must be in odd multiple of track pitch and height should be off odd multiple of track verticle pitch.

**Step 1 requirement**
go to the directory - pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/
then do less tracks.info

tracks are used during routing. route can usually go above the track which are the layers. So, route are basically metal traces. PnR is automated process so we need to specify where do we want our route can go and this information is given by the tracks. Hence tracks are guide to route. Horizontal and verticle track pitches are mentioned. 

<!--- We can verify our 1st guideline. The ports (in and o/p) are in li1 metal layer. So we need to ensure these ports are on intersection or on li1 horizontal and verticle layer.-->

We now werify the guideline using magice. Pressing `g` make the grid visible. We will converge the grid with track value so that we can verify that our ports are actually on the intesection of horizontal and verticle li1 or not. So we try to take track file as reference and verify our file by getting grid information from tkcon window.

From track file we can get x pitch, y pitch, verticle offset and horizontal offset. Let's make a grid according to the track information.

command inside vsdstdcelldesign

```
magic -T sky130A.tech sky130_inv.mag &
```

then open the track information in the pdk -> sky130A -> libs.tech ->openlane -> sky130_fd_sc_hd/

```
less tracks.info
```

![image](https://user-images.githubusercontent.com/69652104/215277035-4ca01f3b-a951-4c6a-9b3a-eeeeff675e42.png)

Horizontal track pitch = 0.46, verticle track pitch = 0.34, horizontal offset = 0.23, verticle offset = 0.17

![image](https://user-images.githubusercontent.com/69652104/215277376-5dfccc9c-e755-4807-9ad2-30514a6af2d8.png)

We can observe horizontal and verticle crossing also we can observe the grid spacing is changed.

![image](https://user-images.githubusercontent.com/69652104/215277507-cc2ed8ab-d6e4-40ff-b903-2eb0afd9748f.png)

**Step 2nd requirement**

The width of the std cell in x direction (x pitch) should be odd multiple of the x pitch and height of the std cell y direction should be odd multiple of the y pitch. We find that the grids are as per our conditions.

**Step 3 LEF file extraction ***

Ports doesn't mean anything to magic. Port definations are required while we want to extract the lef files. After extraction ports are converted in pins. The LEF file contains the cell size, port definitions, and properties which aid the placer and router tool. With that, the ports definition, port class, and port use must be set first. The instructions to set these definitions via Magic are on the [vsdstdcelldesign repo](https://github.com/nickson-jose/vsdstdcelldesign#create-port-definition).


Once we have defined the ports. Our next step is to define the purpose of the ports. For that we do port class and port use. Refer to [vsdstdcelldesign repo](https://github.com/nickson-jose/vsdstdcelldesign#create-port-definition).

After setting the parameters we are ready to extract lef file from our mag. We give the cell a custom name - `save sky130_vsdinv.mag` (command in the tkcon tab).

![image](https://user-images.githubusercontent.com/69652104/215279291-6eefc6a0-a07a-4768-90e0-806e24213e0e.png)

Then open our new inverter mag. 

```
magic -T sky130A.tech sky130_vsdinv.mag
```

Command to create the lef file (run in tkcon window)

```
lef write [name optional]
```

The lef file consists all the information.

![image](https://user-images.githubusercontent.com/69652104/215279645-99a821cf-3f53-4c96-9fbc-b1be750fa553.png)

Setting a layer as port create a PIN in the macro. Now our lef file is ready. 
Next we will try to plug this to our design of picorv32a.

![image](https://user-images.githubusercontent.com/69652104/215279900-6c5eaed7-b133-4935-97c2-3fac3c93563d.png)

**Step 4 Introduction to timing libs and steps to include new cell in synthesis**

* First copy the newly created lef to src under picorv32a:

```
cp sky130_vsdinv.lef /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```

We need to have a library which has our cell defination for synthesis so that abc can map it. (inside vsdstdcelldesign -> libs). We have different library file for different PVT and of different speed. 

* We will require fast slow and typical for STA analysis. 

* Now our objective is that the tool should map the vsd cell during the synthesis flow. We will copy the library (from vsdstdcelldesign -> libs) files to src folder under picorv32a.


```
cp sky130_fd_sc_hd__*  /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```

* Then we need to model our config.tcl under the picorv32a. The edited config.tcl is shown below:

```
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_MIN) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_MAX) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set ::env(FP_CORE_UTIL) 65
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3


set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
source $filename
}
```

* After editing the config file run the full flow from start i.e., in terminal opened on desktop. The set of commands are given below: 

```
1. cd work/tools/openlane_working_dir/openlane
2. docker
3.  ./flow.tcl -interactive
4. package require openlane 0.9
5. prep -design picorv32a -tag [file_name (26-01_21-38)] -overwrite
```

error
![image](https://user-images.githubusercontent.com/69652104/215286127-e30f7e02-8c58-48f1-9d02-ac7cb7f6b730.png)

To resolve - Just change LIB_MIN to LIB_FASTEST and LIB_MAX to LIB_SLOWEST.

Hence the correct config.tcl file.

```
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set ::env(FP_CORE_UTIL) 65
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3


set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
source $filename
}
```

* Then again run the set of abve commands by opening a terminal on desktop.

![image](https://user-images.githubusercontent.com/69652104/215286773-fe6a87cb-d4a6-453f-b121-5bbeff180a37.png)

Now run the below command to add additional lefs.

```
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
```

![image](https://user-images.githubusercontent.com/69652104/215286954-7a530825-2b38-4b49-b57d-53ac3004f8c8.png)

* Then run synthesis

```
run_synthesis
```
After this we can find that our inverter is being used here, see the image attached below:

![image](https://user-images.githubusercontent.com/69652104/215287483-94a26a9d-deb6-44b5-a608-8812d9b3338e.png)

![image](https://user-images.githubusercontent.com/69652104/215287506-7070254b-1915-4ff5-bc34-f2d2f65f2a86.png)

## Delay Table:

Problem:

1. The capacitance or the load at the output node of each and every buffer in the complete clock tree is varying. 

2. Also if the load is varying the input transition is varying.

To avoid large skew between endpoints of a clock tree (happening due to signal arrives at different point in time):

* After splitting the buffers. 

* Buffers on the same level must have same capacitive load to ensure same timing delay or latency on the same level. It means that each buffer at the same level is having same load.

* Buffers on the same level must also be the same size (different buffer sizes -> different W/L ratio -> different resistance -> different RC constant -> different delay). It means that the buffer at same level should be of same size. 

![image](https://user-images.githubusercontent.com/69652104/215288155-f31a933b-9163-4bb5-88e4-afa280f3091b.png)


Solution:

Delay tables are the solution. Delay tables are 2D table. Delay of a component is characterised and summaries in a table.

The timing model of each cell is recorded and is summarised in delay tables, which are part of the liberty file. The output slew is the main cause of delay. Capacitive load and input slew are also factors that affect output slew. The input slew has its own transition delay table and is a function of the previous buffer's output cap load and input slew.

![image](https://user-images.githubusercontent.com/69652104/215288726-b9e8b829-3ec2-4d72-bf4f-2f83f3ed4f29.png)

## LAB DAY 4 (PART 2)

**Step 1 Lab steps to configure synthesis settings to fix slack and include vsdinv**

Let's try to fix the slack. Currently the value of slack is 

```
tns (total negative slack) = -711.59
wns (worst negative slack) = -23.89
```

Slack has to be positive always and negative slack indicates a violation in timing.
We will try to maintain a balance between the delay and the area by changing the variables such SYNTH_STRATEGY (to change the stratergy), SYNTH_BUFFERING (it adds buffer to reduce wire delays) and SYNT_SIZING

We can use the following command to know any variable (switches)

```
echo $::env ([Varible]) // our case = SYNTH_STRATEGY
// change the STRATEGY, Similarly change for buffering and sizing.
set ::env(SYNTH_STRATEGY) 1
set ::env(SYNTH_SIZING) 1
```

<!--- | SYNTH_STATERGY | SYNTH_BUFFERING | SYNTH_SIZING | SYNT_DRIVING_CELL  Area | wns | tns |
 | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
 | 0 | 1 | 0 | sky130_fd_sc_hd__inv_8 | 147712.9184 | -23.89 | -711.59 |
 | 1 | 1 | 1 | sky130_fd_sc_hd__inv_8 |             |        |         | --->
 
Next run floor plan by executing the following codes one by one:

```
init_floorplan
place_io
global_placement_or
detailed_placement
tap_decap_or
detailed_placement
gen_pdn
run_cts
```

![image](https://user-images.githubusercontent.com/69652104/215315200-d39e24d2-f4fe-4c4d-aa8d-77dd78b3e33a.png)

![image](https://user-images.githubusercontent.com/69652104/215315421-c383ede2-99d5-4bda-a659-7f79a625df3c.png)

![image](https://user-images.githubusercontent.com/69652104/215315691-074d0fb9-917b-4d94-9afc-8d678420695a.png)

Then check the file which is created. Go to the placements folder under reults and then invoke the magic tool and load the def file. The command is:

```
magic -T /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```

We can see our sky130_vsdinv file in the merged.lef file inside the tmp folder. The macro is present.

![image](https://user-images.githubusercontent.com/69652104/215317293-256e592c-225f-4465-b396-9551a415a194.png)

We can also see the sky130_vsdinv inside the layout also:

![image](https://user-images.githubusercontent.com/69652104/215317392-1749aaab-7568-4d8b-b883-d57ba4a9f942.png)

## Timing Analysis

First we take the ideal clock (clock tree is not yet build) and do the timing analysis with it. After that we will do with real clock.

###  Pre-layout timing analysis (using ideal clock) 

* SETUP TIMING ANALYSIS.
Specifications Clock frequency = 1GHz and period of 1ns.

We have a launch flop and capture flop and in between we the the combinational logic. We have ideal clock network i.e., clock tree is not yet built. Hence we do not have any buffer in the clock path. This is a typical scenario for hold time and setup time calculation. We send the 1st riseing clock to the launch flop (t=0ns) and the 2nd rising to the capture flop (t=1ns).  

![image](https://user-images.githubusercontent.com/69652104/215318169-d3f4c27c-def8-46c1-ad9b-ba62095e9ca4.png)

The equation for setup time is: 

```
Θ < T - S - SU
```

First basic insight is the setup delay should be less than the combinational delay. Then analysing the capture flop we see some delay due to the mux. due to jitter there is delay in the exact point of clock arrival and this variation is due to internal clock circuitary (PLL). 

where. 
- Θ = Combinational delay which includes clk to Q delay of launch flop and internal propagation delay of all gates between launch and capture flop
- T = Time period, also called the required time
- S = Setup time. As demonstrated below, signal must settle on the middle (input of Mux 2) before clock tansists to 1 so the delay due to Mux 1 must be considered, this delay is the setup time.
- SU = Setup uncertainty due to jitter which is temporary variation of clock period. This is due to non-idealities of PLL/clock source.

**NOTE: Things are different for hold time.**

We have, T = 1000 ps, S = 10 ps, U = 90 ps
Hence we arrive at Θ < 0.9 ns (for our case)

## LAB DAY 4 (PART 3)
##  OpenSTA for post-synth timing analysis

In cts we try to change the netlist by making clock tree.

Making the pre_sta.conf and save it in the openlane folder.

```
set_cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -max /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib
read_liberty -min /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib
read_verilog /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_21-52/results/synthesis/picorv32a.synthesis.v
link_design picorv32a
read_sdc /home/ee22mtech14005/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```

After cts new .v files start getting created. 

Creating my_base.sdc

```
set ::env(CLOCK_PORT) clk
set ::env(CLOCK_PERIOD) 5.000
# set ::env(SYNTH_DRIVING_CELL) sky130_vsdinv
set ::env(SYNTH_DRIVING_CELL) sky130_fd_sc_hd__inv8
set ::env(SYNTH_DRIVING_CELL_PIN) Y
set ::env(SYNTH_CAP_LOAD) 17.65
create clock [get_ports $::env(CLOCK_PORT)]   -name $::env(CLOCK_PORT)  -period $::env(CLOCK_PERIOD)
set IO_PCT 0.2
set input_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
set output_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
puts "\[INFO]: Setting output delay to: $output_delay_value"
puts "\[INFO]: Setting output delay to: $input_delay_value"

set clk_indx [lsearch [all_inputs [get_port $::env(CLOCK_PORT)]]
#set rst_indx [lsearch [all_inputs [get_port resetn]]
set all_inputs_wo_clk [lreplace [all_inputs] $clk_indx $clk_indx]
#set all_inputs_wo_clk_rst [lreplace $all_inputs_wo_clk $rst_indx $rst_indx]
set all_inputs_wo_clk_rst $all_inputs_wo_clk

# correct resetn
set_input_delay $input_delay_value -clock [get_clocks $::env(CLOCK_PORT)] $all_inputs_wo_clk_rst
#set_input_delay 0.0 -clock [get_clocks $::env(CLOCK_PORT)] {resetn}
set_output_delay $output_delay_value -clock [get_clocks $::env(CLOCK_PORT)] [all_outputs]

# TODO set this as parameter 
set_driving_cell -lib_cell $::env(SYNTH_DRIVING_CELL) -pin $::env(SYNTH_DRIVING_CELL_PIN) [all_inputs]
set cap_load [expr $::env(SYNTH_CAP_LOAD) / 1000.0]
puts "\[INFO\]: Setting load to: $cap_load"
set load $cap load [all_outputs]
```
