# 8 bit Priority Encoder (dvsd_pe) RTL2GDS flow using SKY130 pdks
*The purpose of this project is to produce clean GDS (Graphic Design System) Final Layout with all details that is used to print photomasks used in fabrication of a behavioral RTL (Register-Transfer Level) of an 8 bit Priorty Encoder, using SkyWater 130 nm PDK (Process Design Kit)*

# Table of Contents

- [Design Overview](#design-overview)
- [IP specs Provided](#ip-specs-provided)
- [Verilog behavioral design](#verilog-behavioral-design)
- [Pre-layout](#pre-layout)
	- [Simulation](#simulation)
- [OpenLane](#openlane)
	- [Installation](#installation)
	- [Running OpenLane](#running-openlane)
- [Synthesis](#synthesis)
- [Floorplanning Placement and Routing ](#floorplanning-placement-and-routing)
- [Routing](#routing)
- [Final Layout](#final-layout)
- [Post-layout](#post-layout)
	- [Simulation](#simulation)
- [Steps to reproduce and explore the design](#steps-to-reproduce-and-explore-the-design)
- [Keys to remember](#keys-to-remember)
- [Area of improvement](#area-of-improvement)
- [References](#references)
- [Acknowledgement](#acknowledgement)


### Design Overview

![Slide2](https://user-images.githubusercontent.com/80625515/130179293-b7b03b4c-2feb-40a6-814a-31af06acc3cd.PNG)

### IP specs provided

![Slide4](https://user-images.githubusercontent.com/80625515/130180121-a4e5d529-bb6d-4ed3-a265-aad5030f82f7.PNG)

### Verilog behavioral design

## OpenLane 

OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault and custom methodology scripts for design exploration and optimization.
For more information check [here](https://openlane.readthedocs.io/)

### Installation

#### Prerequisites
At a minimum:

- Docker 19.03.12+
- GNU Make
- Python 3.6+ with PIP
- Click, Pyyaml: `pip3 install pyyaml click`

```
git clone https://github.com/The-OpenROAD-Project/OpenLane.git
cd OpenLane/
make openlane
make pdk
make test # This a ~5 minute test that verifies that the flow and the pdk were properly installed
```

For detailed installation process, check [here](https://github.com/The-OpenROAD-Project/OpenLane)

### Running OpenLane

`make mount`
- Note
	- Default PDK_ROOT is $(pwd)/pdks. If you have installed the PDK at a different location, run the following before `make mount`:
	- Default IMAGE_NAME is efabless/openlane:current. If you want to use a different version, run the following before `make mount`:

The following is roughly what happens under the hood when you run `make mount` + the required exports:

```
export PDK_ROOT=<absolute path to where skywater-pdk and open_pdks will reside>
export IMAGE_NAME=<docker image name>
docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) $IMAGE_NAME
```

You can use the following example to check the overall setup:

`./flow.tcl -design spm`

To run openlane in interactive mode

`./flow.tcl -interactive`

![openlane_interactive](https://user-images.githubusercontent.com/80625515/130196432-7e20d103-ce86-4a9c-8a10-757f3bf26e0c.png)

### Synthesis

Steps to explore synthesis of the design

```
make mount
flow.tcl -design dvsd_pe -synth_explore
```

![synth_explore](https://user-images.githubusercontent.com/80625515/130225635-8bee797c-dcf3-445f-9b02-b197dd2c3b39.png)


```

- Printing statistics.

=== dvsd_pe ===

   Number of wires:                 53
   Number of wire bits:             64
   Number of public wires:           5
   Number of public wire bits:      14
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                 55
     $_ANDNOT_                      10
     $_AND_                          1
     $_DLATCH_N_                     3
     $_NAND_                         3
     $_NOR_                          4
     $_ORNOT_                        9
     $_OR_                          25


```


``` 

- Printing statistics.

=== dvsd_pe ===

   Number of wires:                 35
   Number of wire bits:             44
   Number of public wires:           5
   Number of public wire bits:      14
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                 35
     sky130_fd_sc_hd__a21oi_2        1
     sky130_fd_sc_hd__a2bb2o_2       1
     sky130_fd_sc_hd__a31o_2         1
     sky130_fd_sc_hd__buf_1          5
     sky130_fd_sc_hd__dlxtn_1        3
     sky130_fd_sc_hd__inv_2          8
     sky130_fd_sc_hd__o21ai_2        1
     sky130_fd_sc_hd__o22a_2         2
     sky130_fd_sc_hd__o22ai_2        1
     sky130_fd_sc_hd__o2bb2a_2       1
     sky130_fd_sc_hd__o41a_2         2
     sky130_fd_sc_hd__or2_2          1
     sky130_fd_sc_hd__or3_2          2
     sky130_fd_sc_hd__or4_2          4
     sky130_fd_sc_hd__or4b_2         2

   Chip area for module '\dvsd_pe': 276.515200


```


- Yosys synthesis strategies

![synth1](https://user-images.githubusercontent.com/80625515/130226001-21fe0cbe-5a63-4444-9844-08fc22b9c19d.png)

![synthesis](https://user-images.githubusercontent.com/80625515/130226013-809dac5c-563e-46e7-85b4-c83feb46587c.png)


## Pre-layout

### Simulation

  Terminal snap (To perform pre-layout simulation)
  
  ![pre_layout_sim_ter](https://user-images.githubusercontent.com/80625515/130185638-d927ef90-81d7-4642-b03f-10dfdc7c3ce1.png)

  GTKWave output waveform
  
 ![pre_layout_sim](https://user-images.githubusercontent.com/80625515/130185662-662b9542-c5c1-4584-9d7f-da6d140f4aad.png)

### Steps to reproduce Pre-layout simulation

 Open terminal in your system (preferred Ubuntu OS)

```
git clone https://github.com/Khalique13/dvsd_pe_1v8.git
cd dvsd_pe_1v8/pre_layout/
iverilog -o dvsd_pe dvsd_pe.v test_dvsd_pe.v
./dvsd_pe
gtkwave dvsd_pe.vcd
```


### Floorplanning Placement and Routing


- Placement Analysis

```
---------------------------------
total displacement          0.0 u
average displacement        0.0 u
max displacement            0.0 u
original HPWL             804.4 u
legalized HPWL            838.1 u
delta HPWL                    4 %

```

- Routing resurces analysis

```

          Routing      Original      Derated      Resource
Layer     Direction    Resources     Resources    Reduction (%)
---------------------------------------------------------------
li1        Vertical          420           168          60.00%
met1       Horizontal        560           384          31.43%
met2       Vertical          420           432          -2.86%
met3       Horizontal        280           234          16.43%
met4       Vertical          168           167          0.60%
met5       Horizontal         56            42          25.00%
---------------------------------------------------------------

```

- Complete detail routing

```

total wire length = 874 um
total wire length on LAYER li1 = 62 um
total wire length on LAYER met1 = 341 um
total wire length on LAYER met2 = 438 um
total wire length on LAYER met3 = 31 um
total wire length on LAYER met4 = 0 um
total wire length on LAYER met5 = 0 um
total number of vias = 297
up-via summary (total 297):

----------------------
 FR_MASTERSLICE      0
            li1    153
           met1    138
           met2      6
           met3      0
           met4      0
----------------------
                   297

```

- Final congestion report

```

Layer         Resource        Demand        Usage (%)    Max H / Max V / Total Overflow
---------------------------------------------------------------------------------------
li1                168            31           18.45%             0 /  0 /  0
met1               384            43           11.20%             0 /  0 /  0
met2               432            36            8.33%             0 /  0 /  0
met3               234             0            0.00%             0 /  0 /  0
met4               167             0            0.00%             0 /  0 /  0
met5                42             0            0.00%             0 /  0 /  0
---------------------------------------------------------------------------------------
Total             1427           110            7.71%             0 /  0 /  0

```

- Final Summary 

```

[INFO]: Calculating Runtime From the Start...
[INFO]: Flow completed for dvsd_pe/20-08_11-32 in 0h3m43s
[INFO]: Generating Final Summary Report...
[INFO]: Design Name: dvsd_pe
Run Directory: /openLANE_flow/designs/dvsd_pe/runs/20-08_11-32
----------------------------------------

Magic DRC Summary:
Source: /openLANE_flow/designs/dvsd_pe/runs/20-08_11-32/reports/magic//31-magic.drc
Total Magic DRC violations is 0
----------------------------------------

LVS Summary:
Source: /openLANE_flow/designs/dvsd_pe/runs/20-08_11-32/results/lvs/dvsd_pe.lvs_parsed.lef.log
LVS reports no net, device, pin, or property mismatches.
Total errors = 0
----------------------------------------

Antenna Summary:
Source: /openLANE_flow/designs/dvsd_pe/runs/20-08_11-32/reports/routing//33-antenna.rpt
Number of pins violated: 0
Number of nets violated: 0
[INFO]: check full report here: /openLANE_flow/designs/dvsd_pe/runs/20-08_11-32/reports/final_summary_report.csv
[INFO]: Saving Runtime Environment
[SUCCESS]: Flow Completed Without Fatal Errors.


```


### Final Layout



## Post-layout

### Simulation

Terminal snap (To perform post-layout simulation)

![post_lay_sim_term](https://user-images.githubusercontent.com/80625515/130189588-be201db3-9f21-4d12-9055-9806750a0675.png)

GTKWave output waveform

*`To be uploaded soon some bugs`*

### Steps to reproduce Post-layout simulation

 Open terminal in your system (preferred Ubuntu OS)

```
git clone https://github.com/Khalique13/dvsd_pe_1v8.git
cd dvsd_pe_1v8/post_layout/
iverilog -o gls gls.v primitives.v sky130_fd_sc_hd.v
./gls
gtkwave gls.vcd
```

## Steps to reproduce and explore the design

## Keys to remember

## Area of improvement

## References

- [GitLab/OpenLane workshop](https://gitlab.com/gab13c/openlane-workshop)
- [The OpenROAD Project/OpenLane](https://github.com/The-OpenROAD-Project/OpenLane)
- Ahmed Ghazy and Mohamed Shalan, "OpenLane: The Open-Source Digital ASIC Implementation Flow", Article No.21, Workshop on Open-Source EDA Technology (WOSET), 2020. [Paper](https://github.com/woset-workshop/woset-workshop.github.io/blob/master/PDFs/2020/a21.pdf)

## Acknowledgement

- [Kunal Ghosh](https://github.com/kunalg123), Founder, VSD Corp. Pvt. Ltd
- [Mohammad Khalique Khan](https://github.com/Khalique13), Bachelor of Technology in Electronics & Communication Engineering, Aliah University

