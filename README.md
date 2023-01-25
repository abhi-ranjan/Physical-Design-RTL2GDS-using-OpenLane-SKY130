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
        - [About OpenLANE and Strive Chipsets](#about-openlane-and-strive-chipsets)
    - [Getting familier to open-source EDA tools](#getting-familier-to-open-source-eda-tools)
        
    
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
   
   The flow starts from the HDL code i.e.RTL model and ends with GDSII file. The major implimenation steps are:
   * Synthesis - During synthesis the HDL design is translated into circuits,  which are made up of components present in the standard cell library. The resultant circuit is described in HDL and its referred as gate level netlist which is functional equivalent of RTL code. The library building block of cell have regular layouts, cell layout is enclosed by fixed height (rectanglar in shape) whereas the width is variable an is discrete i.e., integer multiple of unit call side width.
   
   * Floor Planning - In Floor planning the chip area is being planned which in turn creates a robust Power distribution to power the circuits. The die is partitioned into different building blocks or components, also the I?O pads are distributed. During macro floor planning macro dimensions, it's pin locations and row definations i.e, rows and routing plan.
   * Power Planning - The power network is constructed typically for a chip was it has to power multiple VDD and ground pins. The power pin are connected to all component through rings and multiple horizontal and vertical strips. Sach parallel structure is meant to reduce the resistance.
   * Placements - 
   
### About OpenLANE and Strive Chipsets

## Getting familier to open-source EDA tools
