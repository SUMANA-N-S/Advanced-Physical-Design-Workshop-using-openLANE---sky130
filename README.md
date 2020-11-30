# Advanced-Physical-Design-Workshop-using-openLANE-sky130

   The 5-day lab-based workshop gives konwledge of hierarchical physical design flow on how to build a chip from the scratch using opensource tools. This course gives an understanding of all the steps in RTL to GDSII flow.

## Introduction to Chip Design.

   Integrated circuit (IC) also called microelectronic circuit, microchip or chip, is an assembly of electronic components fabricated as a single unit, in which miniaturized active devices (e.g., transistors and diodes) and passive devices (e.g., capacitors and resistors) and their interconnections are built up on a thin substrate of semiconductor material (typically silicon). The resulting circuit is thus a small monolithic “chip,” which may be as small as a few square centimetres or only a few square millimetre.
   To protect chips from corrosion, physical damage and to allow for mounting of the electrical contacts connecting them to the PCB chips are pun into protectice packages. Each package contains several chips Foundry IPs Macros etc as shown in Fig.1. 
   
   
   Types of packaging:
   - Pin-grid array: These are for socketing.
   - Quad flat pack: A lead-frame package of the leadless variety.
   - Quad flat no-lead: A tiny package, the size of a chip, used for surface mounting.
                 
   A single chip is the hardware part of a huge package or a system, communication between the system hardware, software and application software is very important to achieve required output (Fig.2). User input is collected through application software in the form of application program. system software consists of operating system compiler and assembler. System software converts application program into machine language program, this machine language program is given as input to the hardware.
Fig.1 Example of a Package.

   A Chip consists of several components as listed below
   - Pads - Through which signals can pass inside or outside the chip.
   - Core - where all the digital logic is present.
   - Die - size of the entire cfip.


        
## Physical Design
   In integrated circuit design, physical design is a step in the standard design cycle which follows after the circuit design. At this step, circuit representations of the components (devices and interconnects) of the design are converted into geometric representations of shapes which, when manufactured in the corresponding layers of materials, will ensure the required functioning of the components. This geometric representation is called integrated circuit layout. This step is usually split into several sub-steps which include both design and verification and validation of the layout.
        
   Integrated Circuit (IC) design is split up into Front-end Design using HDLs and Back-end Design or Physical Design. The inputs to physical design are (i) a netlist, (ii) library information on the basic devices in the design, and (iii) a technology file containing the manufacturing constraints. Physical design is usually concluded by Layout Post Processing, in which amendments and additions to the chip layout are performed. This is followed by the Fabrication or Manufacturing Process where designs are transferred onto silicon dies which are then packaged into ICs. 

## OpenLANE Flow

   OpenLANE is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, SPEF-Extractor and custom methodology scripts for design exploration and optimization.
   
Fig.2 Openlane Flow (RTL to GDSII)

In Linux operating system open a terminal and check for the required pdks, tcl scripts and configuration files under the working directory.


Process Design Kit(PDKs) contains device models, digital standard cell libraries, process design rules, input-output libraries and other important collection of files used for fabrication.


>./flow.tcl -interactive   //command to invoke openlane


> package require openlane 0.9 //adding packages to openlane

Design preperation step with a custom folder name(sns_run) to store all the results.

> prep -design -tag sns_run -overwrite


### Synthesis:

   Physical design is based on a netlist which is the end result of the synthesis process. Synthesis converts the RTL design usually coded in VHDL or Verilog HDL to gate-level descriptions which the next set of tools can read/understand. This netlist contains information on the cells used, their interconnections, area used, and other details.
   
   In openlane flow 3 tools sre used for synthesis:
   - yosis : for RTL synthesis
   - ABC : for technology mapping
   - OpenSTA : to perform static timing analysis on resulting netlist.
   
   After design preperation review files in picorv32a directory before perfoeming synthesis.
   
   config.tcl file under picorv32a directory
   
   > run_synthesis  //openlane command to run synthesis
   
   
   To find flop ratio get value of number of flip-flops and number of cells from synthesis report and calculate the ratio of flip-flops to cells.
   
   ### Floorplan:
   
   Floorplanning is the process of identifying structures that should be placed close together, and allocating space for them in such a manner as to meet the sometimes conflicting goals of available space (cost of the chip), required performance, and the desire to have everything close to everything else.

Based on the area of the design and the hierarchy, a suitable floorplan is decided upon. Floorplanning takes into account the macros used in the design, memory, other IP cores and their placement needs, the routing possibilities, and also the area of the entire design. Floorplanning also determines the IO structure and aspect ratio of the design. A bad floorplan will lead to wastage of die area and routing congestion. 

In openlane tools used for floorplan:
- init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
- ioplacer - Places the macro input and output ports
- pdn - Generates the power distribution network
- tapcell - Inserts welltap and decap cells in the floorplan


> run_floorplan  //command to run floorplan


To view the floorplan in magic :
Magic is the Layout tool in openlane which performs DRC checks



### Placement 

Placement uses RC values from Virtual Route (VR) to calculate timing. VR is the shortest Manhattan distance between two pins. VR RCs are more accurate than Wire Load Model RCs.

Placement is performed in four optimization phases:
- Pre-placement optimization
- In placement optimization
- Post Placement Optimization (PPO) before clock tree synthesis (CTS)
- PPO after CTS.
- Pre-placement Optimization optimizes the netlist before placement, HFNs (High Fanout Nets) are collapsed. It can also downsize the cells.
- In-placement optimization re-optimizes the logic based on VR. This can perform cell sizing, cell moving, cell bypassing, net splitting, gate duplication, buffer insertion, area recovery. Optimization performs iteration of setup fixing, incremental timing and congestion driven placement.
- Post placement optimization before CTS performs netlist optimization with ideal clocks. It can fix setup, hold, max trans/cap violations. It can do placement optimization based on global routing. It re does HFN synthesis.
- Post placement optimization after CTS optimizes timing with propagated clock. It tries to preserve clock skew.

Openlane tools for placement

- RePLace - Performs global placement
- Resizer - Performs optional optimizations on the design
- OpenPhySyn - Performs timing optimizations on the design
- OpenDP - Perfroms detailed placement to legalize the globally placed components


> run_placement  //openlane command to run placement

To view the design post placement:

 To change the input-output pins alignment set the variable FP_IO_MODE.
 
 > set ::env(FP_IO_MODE) 1
 
 The variable value of 1 performs euidistant placement of pins. If the variable is set to higher value pin placement changes.
 
#### Cell cloning form Github:

A standard cell available in github can be cloned and included into openlane standard cell library. A cell once cloned can be used n nunber of times ar per design requirements.

Example of cloning an inverter cell from github into openlane standard cell library.

in the open lane directory
> git clone URL of the README.md of required cell from github

vsdstdcelldesign - New directory created after cloning





After cloning a cell copy the tech file from pdks into vsdstdcelldesign

To view the layout of inverter in magic prompt

> magic -T sky130.tech sky130_inv.mag &

In magic tkcon terminal to extract sky130_inv into sky130_inv.ext
 > extract all
 
 Extracting with parasitics to spice file
 > ext2spice cthresh 0 rthresh 0
 
 #### Creating SPICE deck using sky130 tech file:
 
 To characterise inverter using sky130 model files 
 
 > ngspice sky130_inv.spice
 
 To see the transient analysis plot 
 
 > plot y vs time a 
 where y is the output and a is the input pin
 
 
 
As there are spikes in the output wave form change the value of capacitance load in sky130_inv.spice


Characterizing:
- Rise transition : time taken by the output to transit from 20% of the maximum value to 80% of the maximum value
- Fall transition : time taken by the output to transit from 80% of the maximum value to 20% of the maximum value
- Fall cell delay : difference between the time when output has fallen to 50% of the maximum value while input is rised to 50% of the maximum value
- Rise cell delay : difference between the time when output has rised to 50% of the maximum value while input is falled to 50% of the maximum value

#### Extraction of LEF file:

In magic layout activating the tracks to ensure that A and Y pins are placed on the intersection of tracks.

In tkcon to activate grids
> grid 0.46um 0.34um 0.23um 0.17um

Giving custom name to the standard cell :
   In tkcon terminal 
> save sky130_vsdinv.mag
> magic -T sky130_tech sky130_vsdinv.mag &

To create a lef file for vsd_inv - in tkcon 
> lef write 

For synthesis a library which has the cell defnition is necessary so copy this lef file into picoprv32a src directory

Edit the config file to include the variables required for synthesis of std cell inverter.


In openlane flow after design preperation add the lef files
> set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
>add_lefs -src $lefs

> run_synthesis
 
In synthesis report observe the chip area, wns-maximum slack and tns-total negative slack

As the slack is violated change the synthesis strategy, buffering, sizing  to reduce the slack violations and run synthesis again.

After synthesis with reduced slack violations run placement 
> run_placement


To configure OpenSTA for post syntheis timing analysis:

create a configuration file pre_sta.conf and edit to include units, min and max values, libraries and verilog files

Further reduce the slack violations by optimizing the fanout, replacing the nets with high slew and delay and upsizing the buffers and run synthesis, floorplan and placement.

### Clock Tree Sinthesis

The goal of clock tree synthesis (CTS) is to minimize skew and insertion delay. After CTS hold slack should improve. Clock tree begins at .sdc defined clock source and ends at stop pins of flop. In clock tree optimization (CTO) clock can be shielded so that noise is not coupled to other signals. But shielding increases area by 12 to 15%. Since the clock signal is global in nature the same metal layer used for power routing is used for clock also. CTO is achieved by buffer sizing, gate sizing, buffer relocation, level adjustment and High Fanout Nets synthesis. We try to improve setup slack in pre-placement, in placement and post placement optimization before CTS stages while neglecting hold slack. In post placement optimization after CTS hold slack is improved. As a result of CTS lot of buffers are added.
openlane tool - TritonCTS - Synthesizes the clock distribution network (the clock tree)
> run_cts

Invoke the openroad from openlane
> openroad

To do timing analysis create a db from lef and def files. db once created can be read multiple times.
> read_lef loacation of merged.lef
> read_def location of picorv32a_cts.def
> write_db
After CTS slack is increased. To reduce slack violation edit the variables for clock buffers, replace the buffers.

### Routing:

There are two types of routing in the physical design process, global routing and detailed routing. Global routing allocates routing resources that are used for connections. It also does track assignment for a particular net.

Detailed routing does the actual connections. Different constraints that are to be taken care during the routing are DRC, wire length, timing etc. 
Openlane tools for routing:
- FastRoute - Performs global routing to generate a guide file for the detailed router
- TritonRoute - Performs detailed routing
- SPEF-Extractor - Performs SPEF extraction

> run_routing





















 
 


 











   
   
   
   
   
   
   
   



