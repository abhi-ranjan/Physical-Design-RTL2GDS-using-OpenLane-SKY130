# Physical Design RTL2GDS using OpenLane/SKY130
The following repository consists of knowledge gained and steps followed while doing the Advanced Physical Design Using [OpenLANE/SKY130](https://openlane.readthedocs.io/en/latest/) workshop. The [workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) focuses on the complete ASIC flow approach from RTL2GDS using open soucrce EDA tools such as OpenLANE/SKY130. RISC-V architechture is followed for designing the the core of [PICORV32A](https://github.com/YosysHQ/picorv32).
# Table of Content
  * [About RTL to GDSII Flow] (#About RTL to GDSII Flow)
  * OpenLANE
  * SKYWater130 PDK
  * List of tools used
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
