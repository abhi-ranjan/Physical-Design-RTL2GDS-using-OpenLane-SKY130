# Advance Physical Design RTL2GDS using OpenLane/SKY130
The following repository consists of knowledge gained and steps followed while doing the Advanced Physical Design Using [OpenLANE/SKY130](https://openlane.readthedocs.io/en/latest/) workshop. The [workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) focuses on the complete ASIC flow approach from RTL2GDS using open soucrce EDA tools such as OpenLANE/SKY130. RISC-V architechture is followed for designing the the core of [PICORV32A](https://github.com/YosysHQ/picorv32).
# Table of Content
  * [About RTL to GDSII Flow](#about-rtl-to-gdsii-flow)
  * [SKYWater130 PDK](#skywater130-pdk)
  * [OpenLANE](#openlane)
  * [Tools Used](#tools-used)
  * [Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#day-1---inception-of-open-source-eda-openlane-and-sky130-pdk)
    - [How to talk to computers](#how-to-talk-to-computers)
      - [IC Terminologies](#ic-terminologies)
      - [Introduction to RISC-V](#introduction-to-risc-v)
      - [Software to Hardware](#software-to-hardware)
 
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
  **RISC-V** is an open instruction set architechture rooted on **reduced instruction set computer principles**. It is an open source ISA used for processor design. 
  #### RISC-V Characterstics
  * It uses one clock cycle per instruction.
  * It follows the th RISC Princples. 
  * It has both 32-bit and 64-bit varients. It also support floating point instruction.
  * It avoids micro-architechture or technology dependent features.
  * It accelerates the time for design to reach the market as it uses open-source IP.
  
  ### Software to Hardware 
  The flow shows how the high level language (at software end) gets converted to machine language (at hardware end) and then gets executed on the package.
