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
      * [Utilization factor and aspect ratio](#utilization-factor-and-aspect-ratio)
      
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

**flop ratio = count of d flip flops / number of cells = 1613/14876 = 0.108429 (10.8429 %)

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

**NPTE: Standard cell placement happens in placement stage.**

### LAB Day 2

Continuation after synthesis.

**Step 1:** 
* We have lot of switches with which we adjust the flow directory. These switches are used to set certain parameter in each stage of the flow. For eg: In the Floorplanning stage we have `FP_CORE_UTIL {for utilization percentage}, `FP_ASPECT_RATIO {sets the aspect ratio}, `FP_CORE_MARGINS{offset b/w die boundary and core boundary}, etc. We have certain .tcl file in OpenLane which has these switchs that sets these specifications. 
 
**floorplan.tcl contains the following default switchs**

![image](https://user-images.githubusercontent.com/69652104/214922065-9a283cce-6623-42b9-b754-94a26c2e3e1c.png)

* The command to run floorplan is:

``
run_floorplan
``
