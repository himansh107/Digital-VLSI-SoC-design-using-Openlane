# Openlane_workshop
## Contents

<a href="#header-1">Day 1 Labs : Open Lane directory structure in detail</a>


## Day1 theory

### Simplified RTL to GDS flow

The rtl to gds flow consists of the steps of desiging a application specific integrated circuit. It starts with a Register transfer level (RTL) file and ends at a Graphical Data System file (GDS). RTL is the description of the logic circuit using verilog.  </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/ee7c60e5-6766-4b3e-be24-59f11d45231a)

</p>

### 1. Synthesis

The first major step in a typical ASIC flow is the synthesis. Converts RTL to a circuit out of components from the standard cell library (SCL).</p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/99f12828-fde6-45db-8d47-e8429d2d1610) </p>

The library building blocks or the cells have regular layouts. Typically, the cell layout is enclosed by fixed height rectangle. The cell width is variable or discrete. </p>
Each cell comes over different models/views. Utilized by different EDA tools.  </p>

1. Liberty view - includes electrical models for the cells such as delay models and power models. 
2. HDL - behavioural views of the cells. 
3. spice - or CDL views of the cells
4. Layout view
       - Abstract view - lib view </p>
       - Detailed view - GDS view </p>
       
### 2. Floor and power planning

**Floor planning** – Partitioning the chip die between different system building blocks and place the I/O pads. </br>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/6532adb5-3e7a-41fb-94ed-76af256c16e1) </p>

Macro Floor-Planning:  dimensions, pin locations, rows definition <br>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/5aca66b1-2106-43c6-85e6-acc5cf66b701) </p>


**Power planning** – Typically a chip is powered by multiple VDD and GND pins. The power pins are connected to all components through rings and vertical and horizontal metal straps. 

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/21d63f2f-f8e5-4192-a142-ae850ca7aaf1) </br>

Such parallel structures are meant to reduce the resistance and hence the IR drop and to address the electro migration problem. Typically, the power distribution network uses the upper metal layers as they are thicker than lower metal layers. Hence, they have less resistance. </p>

### 3. Placement 

Done in two steps
	1. Global placement
	2. Detailed placement
  
  </p>
Global placement, optimal positions for all cells. Such positions are not necessarily legal so cells may overlap or may go off roads. In detailed placement the positions obtained from global placement are minimally altered to be legal. </br>

After placement comes routing, but before routing the signals we need to route the clock. </br> 


### 4. Clock distribution network 

To deliver the clock to all sequential elements (eg FF) with minimum skew and in good shape. Clock skew means the arrival of the clock at different components at different times. Usually a tree like structure (H, X) in which the clock source are the roots and the clocked elements are the leaves.

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/8b5925c1-63b2-499d-86c7-1f4b8c269bfa)

### 5. Signal Routing

Implement the interconnect using the available metal layers. Sky130 pdk defines 6 routing layers. For each (metal) layer the pdk defines the thickness, the pitch and the minimum width, also it defines the vias that can be used to connect different wire segments on different layers together. The lowest layer is called local interconnect. The following 5 layers over the local interconnect are aluminium layers. </p>

**Most routers a grid router**. They construct the routing grid out of the metal layer tracks. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9d616d1e-9415-4cb4-ad8e-bfa2dce2a01c)
 </br>
**figure** - routing based on grid lines </p>

Routing grid is huge therefore a divide and conquer technique is applied. <br>

-	Global routing – generates routing guides
-	Detailed routing – uses the routing guides to implement the actual wiring

### 6. Sign off (Verification)

1. Design rule checking (DRC) – final layouts honour all design rules
2. Layout vs Schematic (LVS) - The layout vs schematic makes sure that the final layout matches the gate level netlist the we started with. 
3. Timing verification - Static timing analysis

### Open lan ASIC flow


![image](https://github.com/himansh107/openlane_workshop/assets/75253218/7a6ca2b1-a252-4940-9a03-9672a7170c8e) </p>

Open lane consists of a chain of different open-source tools for different steps of the ASIC design flow.  </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/0960f31f-810c-4d2d-9391-f83de3fdc9db) </p>

**Synthesis exploration utility** can be used to generate reports that shows how the design delay and area is affected by the synthesis strategy and based on this exploration we can pick the best strategy to continue with. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/dd89fd82-7019-40f0-b1e7-2b6b25e285d8) </p>


**Design exploration utility**

The Design exploration utility can be used to sweep design configuration. We have 16 of them. To generates a report like this. In this report eventually it shows different design metrics. There are more than 35 design metrics. It shows also the number of violations generated, after generating the final layout. </p>
This is very useful to find the best configuration for open lane for any given design. It recommended to explore the design first and use the obtained best configurations going forward. </p>

**After synthesis is DFT**. We can enable this step. This is optional. </p>

Open-source tool for DFT – Fault </p>

**After this comes the Physical design** </p>
Entire physical design is done by OpenROAD. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/c08d34f1-03e7-4df9-935f-b0842bad4164)
</p>

**Dealing with antenna effects in layout made by OpenROAD**

Antenna effects can be dealt by adding a fake antenna diode next to every cell input after placement. Open lane guys created a fake antenna diode cell and added it to the standard cell library. This cell is not a real diode but matched the footprint of the library antenna diode. </p>
We add a thick antenna diode next to every cell input after placement. After detailed routing we run antenna checker (magic) against the layout. If the checker reports a violation on the cell input pin, replace the fake diode cell with a real one. </p>


![image](https://github.com/himansh107/openlane_workshop/assets/75253218/869c268d-c23b-431c-b821-aa134860af10) </p>

**Global router tool of openlane is called Fast route.** </p>

An openLANE update introduced a new tool, ARC that can be used to check for antenna rules violation during global routing. </p>

**Sign off step :**
| Step | Tool|
| :---:  | :-: |
|STA|OpenSTA|
|DRC|Magic VLSI|
|LVS|Netgen|
</p>
In LVS we do layout vs schematic to check whether they are equivalent. The spice netlist for layout is extracted using Magic and then netgen performs comparison between this and the Verilog netlist. 
</p>

<h2 id="header-1">Day 1 Labs : Open Lane directory structure in detail</h2>

The sky130A folder in the openlane directory consists of two folders </p>
1. libs.ref – contains all process specific files eg the timing, the lef, the cell lef
2. libs.tech – are those files, specific to the tool
</p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9486d525-babe-4f34-99f2-b4ccf7ba9337)

We would be working with sky130_fd_sc_hd <br>
Sky130 – pdk name <br>
fd – foundary <br>
sc -standard cell <br>
hd – high density <br>

**techlef** – tech lef files are the files which contain the layer information. 
Similarly, there are other files like mag files, lef files, the lib files. If we go in the lib directory, we find library files for all corners. The same is shown below. 

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e9363f6e-46e2-4ef1-b996-3d4cecef1b2a)

tt – typical <br>
ss- slow slow <br>
ff – fast fast <p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/52fb91f1-bdbe-4209-9d95-1c41547eebd0) </br>

Thus, the lib file name uses PVT corners as labels. <p>

**Difference between file extensions of lef and techlef files** <p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/6105d3c7-f0c6-43b0-9e8d-8a339502ba80)

**Setup commands to run open lane**

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/b8f643d2-3fda-4de4-8a43-15991468e6ab)

we use the **-interactive** switch with flow.tcl so that the ASIC flow is in our control. Open lane is programmed to execute the entire flow at once. Therefore, we use the -interactive meaning the user would ‘interact’ to execute the flow. </p>

All the designs that are being run by open lane, it extracts from this design folder. Here is where you can also build in your design. 
There are close to 30-40 designs already built in to open lane. Here is where you can also build in your design.  </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e41d7a8f-cca2-4099-95d1-77d2127ad6dd) </p>

The priority order in which open lane considers values from
1. sky130A_sky130_fd_sc_hd_config.tcl 
2. config.tcl
3. default value already set in open lane

- config.tcl bypasses any configurations that has been already done in openlane.
- sky130A_sky130_fd_sc_hd_config.tcl bypasses any configurations that has been set by config.tcl
- so, sky130A_sky130_fd_sc_hd_config.tcl is the highest priority one.

**Example**

 <img src ="https://github.com/himansh107/openlane_workshop/assets/75253218/54ed2052-5323-46b9-942d-17c42befef30" width="700"/> </p>

   config.tcl has clock period value as ‘‘5.000’’ this will override the default values in openlane. </p>

   <img src ="https://github.com/himansh107/openlane_workshop/assets/75253218/68baba2a-d81d-4ce7-abfc-bade7952dd53" width="500"/> </p>

While sky130A_sky130_fd_sc_hd_config.tcl has clock period value as “24.73”. this will override the value set by config.tcl </p>

**We will go to Synthesis step** 

Before starting synthesis, we need to prepare the file system or the design data setup stage.</p>
design data setup stage -> design preparation stage. </p>

Command to prepare design : 

```bash
prep -design picorv32a
```
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e9fe4fa6-5fdf-4d2a-97ca-d679d0b19939) 


One of the preparation steps is - merging LEFs. This step is merges the tech lefs and lefs into one lef file. </p>
After the preparation is complete. We may check the picorv32a folder. A ‘runs’ folder has been created in that. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/99863358-1d8e-4081-bd3d-7d5f222fa0c3)

</p>

This shows that preparation is done. If we go into the runs folder, an additional folder named as the current date (on which the preparation was done) is present. This folder has the reports and results. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e123cc41-2bc5-43c2-8194-11febb251673) </p>

The reports and results folder will be empty for now, as we haven’t performed any step. </p>
Contents of config.tcl file created. It stores all default values. 

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9bb74744-3ccd-4c2d-a783-4820339b814b) </p>

One good thing about open lane is that you can make changes on the fly. So as and when you make change and you run that step. The data will be changed accordingly in config.tcl file. </p>

After all this background we ran the **run_synthesis** step. This will run the synthesis using the **yosys** and **abc** </p>

**Synthesis successful** </p>

![synthesis successful](https://github.com/himansh107/openlane_workshop/assets/75253218/4f0ae59e-b393-4a6c-8b83-5272417bf9f7) </p>


File generated as a result after synthesis step </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/660da98d-6abe-484c-9f1f-8dfc3b721778) </p>

File generated as a report after synthesis step </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9ce73b0e-a059-4cd4-9e84-b2c480688808) </p>

D flipflops = 1613 </br>
D ff/total cells = 1613/14876 = 10.84% </p>

## Day 2 labs : Floorplan & Placement

How logic designs are prepared for floorplan (pre placed cells lecture) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/6bfc7032-935e-49a4-83b6-8a0b6b14d876) </p>

implementation – making the standard cell/IP <br>
instantiation – using the IP as an instance </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/4a54edea-c056-4f43-800b-48e5f6d01836) </p>

Things taught before preplaced cell – utilization factor, aspect ratio. </br>
During preplaced cells – de coupling capacitors

**De coupling capacitors** </p>

Due to distance between power supply and logic block. There is a drop in voltage levels reaching the logic block. Charged capacitors are added around the block to compensate for this. These charged capacitors are called, De coupling capacitors. De coupling capacitors are capacitors which help provide enough VDD and GND for operation of the logic block.  </p>
 
After pre placed cells – power planning – vdd and gnd rails</p>
Then Pin placement 	</p>
After explanation of pin placement. Configuration files were explained.  </p>
The tcl files storing the default configurations for each step are stored in the configuration folder of open lane directory. </p>
Below is the snap showing description of two variables in the README.md file. The README.md file is a guide to knowing meanings of variables. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/49b9c3e2-f982-49cc-beb9-7dbd846cf560) </p>

Default values of environment variables for floorplan step (below) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/d90cb706-4fdf-41e9-9a13-159a287e6c37) </p>

**Steps to run floorplan** </p>

```bash
run_floorplan
```

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/055af5eb-e479-44d4-97cd-1726c6d9ed5c) </p>

successful floorplan execution </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/348e9685-c2de-41b9-9d40-59bed17ae216) </p>

**Reviewing floorplan files** </p>

We will check the config.tcl files of every location to check which values got precedence </p>

Openlane default configuration for floorplan. Note the core utilization is 50% </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/c6fcc1a3-ab48-4420-95e5-02412844fb32) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9655b9e4-23c8-4fee-a6cc-abbeab479633) </p>

contents of sky130A_sky130_fd_sc_hd_config.tcl </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/c53f5498-4723-46aa-9cd0-9c88ae487ff2) </p>

config.tcl file from runs folder. Not the core utilization value – 35% , clock_period – 24.73 </p>
These values are directly taken from sky130A_sky130_fd_sc_hd_config.tcl overriding the config files from picorv32a folder and the openlane defaults. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/a673b335-6af3-4998-97b3-2d5ef5ee69e5) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/636a8e08-56f2-4292-af60-72723b3ae18c) </p>

Thus, precedence of sky130A_sky130_fd_sc_hd_config.tcl is justified </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/60a41222-5c8d-45b5-9ea8-8e6c096e4723) </p>

Reviewing the magic floorplan in magic </p>

Command - 

```bash
magic -T <pdk tech file location> lef read ../../tmp/merged.lef read def picorv32a.floorplan.def & </p>
```

**Full floorplan** </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/db81bc5f-1680-4342-a639-8b85f30d6e24) </p>

## Library binding and placement

In a broader sense, library consists of cells, shapes and size of cells, various flavours of the cells and timing information. </p>
Library is a collection of standard cells and their timing information (delay). </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/94743332-a45a-44f0-9e3b-28fb32e0ae96) </p>

Library can be of two types. 1 type may consist of shapes and sizes of standard cells, while the other type may consist the delay information of these cells. Library has got various flavours of any cell. </p>

**Placement**

Till now, we have the well-defined floorplan with all the pin placement done, we have the power supply grid setup. We have the gate level netlist and the physical view of the logic gates in the netlist. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/eae4bb48-a58e-4bb7-a731-4090670b1101) </p>

Now we need to place these cells. The placement of the cells is done in a similar fashion as in the netlist. </p>
Buf = buffer = repeaters. Repeaters are added to improve signal quality. </p>

**Optimized placement**

This is the stage where we estimate wire length and capacitance and based on that, insert repeaters.</p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/bc58ba22-5d27-4cee-8573-776680c0e8db) </p>

We will do a quick setup timing analysis based on an ideal clock. Based on the numbers we get on the setup timing analysis, we’ll come to know whether the placement we have done or not, is reasonable. </p>


### Labs for placement step

Placement happens in two stages – </p>
1. Global placement - coarse placement. Objective is to reduce wire length. (half parameter wire length. (HPW)).</p>
2. Detailed placement – The standard cells have to be placed inside the standard cell rows. They have to be exactly inside the rows. They should be abutted with each other. There should be no overlaps. </p>

```bash
run_placement
```

Objective is to converge the value in OVFL (overflow) variable. </p>

Placement result </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/0a6cb304-c555-4988-a4ad-a5c63c17e9ab) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/c9217521-3456-4453-bc75-433f6ecb033e) </p>
 


## Day 3 Theory & Labs : Cell design flow 

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/ea493362-6017-4181-abbc-90a9d56a752b) </p>

Characterization flow:
1. Read in the model file
2. Read the extracted spice netlist
3. Define/Recognize the behaviour of the buffer 
4. Read the subckt of the inverter
5. Attach the necessary power supplies
6. Apply the stimulus
7. Provide output capacitance (variable/varied in a range)
8. Provide the necessary simulation command
Feed all the 1-8 steps to GUNA software


**Changing pin configuration in floorplan**
Previously the pins in the floorplan were placed equidistant to each other. We can change this pattern by changing the switches in the floorplan.tcl file of the configurations folder of openlane 
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/bd791ccb-38ef-4409-8b2f-574114ed71f8) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/90d40ab2-5fbb-4edd-81fd-a3d43f5c83bc) </p>



This the hungarian pin configuration </p>


**CMOS theory taught**
-	Switching threshold
-	W/L ratio
-	dc sweep
-	transient analysis
Git clone repository 
-	16 mask CMOS processs
-	**LEF files** – LEF files are the files containing just the connectivity information of the layout for eg. Pins information and perimeter boundary. In commercial tools, lef is also called frame view. LEF also serves as the function of protecting the IP. When we buy an IP from the vendor, it is their product. They won’t want their IP to be visible to the world. Using LEF they protect it. (This was cell LEF)
-	**Technology LEF** – It contains information about available metal layer, via information, DRCs of particular technology used by placer and router etc. 


**CMOS layout extraction and spice simulation**

Layout from clone github repo

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/42f41279-bedb-44e0-badb-962bb854fc5b) </p>

Extracting the spice file from layout </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/85968173-e96f-4a18-a8e3-3fa2de3cf0de) </p>

Modified spice file </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/58fc7b43-9a73-4e74-aedd-10c3af0cb106) </p>

Spice simulation </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/38a709e8-2573-484d-9ed4-7e21689b2db8)
 </p>

  ![simulation output](https://github.com/himansh107/openlane_workshop/assets/75253218/943a4c68-22bc-4fd5-adb2-63d40523fef6) </p>

**Output rise time** = (time taken by output to rise to 80%) – (time taken by output to rise 20%) = 2.16874 ns – 2.1199 ns = 0.04974ns = **49.74 ps** </p>

**Output fall time** = (time taken by output to fall by 80%) - (time taken by output to fall by 20%)
= 4.06638 – 4.03971 = 0.0266ns = **26.6ps** </p>

**Propagation delay for rising outpu**t = (time taken by output to rise to 50%) – (time taken by input to fall to 50%) = 2.18653 – 2.15 = 0.03653ns = 36.53ps </p>

**Propagation delay for falling output** = (time taken by output to fall to 50%) – (time taken by input to rise to 50%) = 4.05326 - 4.05003 = 0.00323 = 3.23ps </p>


## Day 3 MAGIC tutorial 

**drc_tests** </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/f8f25443-c87a-4054-bebd-bd2d6701b99f) </p>


Metal3 to metal3 spacing is minimum – 0.3um </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/0f901e91-1135-462b-8bde-ec46e2e06c78)


![image](https://github.com/himansh107/openlane_workshop/assets/75253218/db3e34dc-adc2-446d-8756-6b0faae7ac34) </p>


Checked box – these are the contact cuts. They don’t actually exist in the draw layout view but they represent the mask layer for via 2 that would end up in the output GDS, when we generate GDS from the layout.</p> 
The view we see in the layout window is called a feedback entry and it sticks around for as long as you want and you can dismiss it with the command feedback clear or feed clear for short </p>
feed clear command </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/0607aa42-fe53-477f-87e6-bccce02bbc61) </p>


**Lab for poly.9 drc**

drc rule in sky130 pdk </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/277c0bf9-0015-4d4f-bce4-ace0086ba225) </p>


The poly to polyres distance should be 0.48um. but the magic layout doesn’t show a drc despite being less than 0.48um. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e4ba3586-f8e2-4eb1-938d-7a958b8fbe73) </p>



To solve this, we have to edit the tech file </p>
Adding lines in tech file </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/af7f85d9-c583-41a4-90c6-def9cfdba925) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/4b286604-840d-441b-9a3b-aa508481527c) </p>

Reloading the tech file </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/09ca8021-9cc2-4af3-8be4-14ddca5a9cb0) </p>

drc checking again </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/373b1286-d789-413e-868e-bbe9ffd6233f) </p>





**Lab for npoly res to diff drc** 		

the npoly res doesn’t show drc error, while the ppolyres and xpolyres shows drc error.</p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9b281e9a-bbe8-4667-9773-d9e82407c262) </p>

Modifying the tech file for drc </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e7b46e72-41f2-408b-bacc-ac9cde623c86) </p>

drc showing properly. (npolyres to diff spacing < 0.48um) </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/1ade281e-e8ca-49c1-93e2-dec697db9707) </p>


**Lab challenge exercise to describe DRC error as geometrical construct**

drc rule in tech file </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/26d9be42-b613-427c-8940-1d3451f964ac) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/96696e09-a391-41ef-b9e0-efef88704380) </p>

drc rule in sky130pdk </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/94ef3d02-8fca-4ce9-bf06-5781aa61974d) </p>

DRC rule violation because min enclosure of nwell hole by deep nwell outside is < 1.030um </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/da99af35-2cea-48b9-a65e-62cea20178a3) </p>

using cif commands to see missing nwell part </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/a804294b-4570-4dd1-b7c6-2947beb0fd3d) </p>

**Lab challenge to find missing or incorrect rules and fix them**

nwell should have a n substrate contact inside it (nwell.4). The below nwell layout should flag a drc error but it is not. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/38178778-bfea-4083-bf39-dcb2be842b18) </p>

(nwell.4) drc rule on sky130pdk website </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/21e3aab8-46b9-4fcb-b6a4-233758df5e88) </p>

**Modifying the tech file**

Adding cifmaxwidth rule for n-well missing tap </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/a9b9be20-7a95-4e1b-8036-c84bb10b44d3) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/293732e9-261e-4cf3-a436-3811452f8692) </p>

reloading the tech file, setting drc style to drc(full) and doing drc check again </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/94f5ce2b-d447-420e-b8f1-7484a401f115) </p>


## Day 4 labs

ROUTES are basically metal traces. Traces of li, metal1, metal2 </p>
Where you want you routes to go? </p>
There are certain guidelines to follow while making standard cells: </p>

- The ports of the layout have to be on the intersection of the horizontal and vertical tracks so that the router can reach there.
- The width of the standard cell should be an odd multiple of the x pitch. X pitch means horizontal width of the grid box. </p>

Setting the grid according to the tracks.info file </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/e13cdfca-8e3a-4cba-b922-69aa7c6669c6) </p>

The layout satisfies the 2nd condition of a standard cell i.e. the width of the standard cell must be in odd multiples of x pitch. In this case it is 3 times x pitch (1+1+1/2) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/d73e8785-e380-4509-a613-a3fa0e3300c9) </p>




Labs to convert our standard cell layout into LEF file
1. set the pin labels as ports
2. set port class and port use
-  port classes are input/output/inout
- inout is used for power and ground pins
- port use is for what use the pins are placed. 
1. use types are signal and power </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/5e108412-31e0-4ee9-afd2-7bdb19e8d871) </p>

port class can be set in magic tkcon terminal as shown below </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/515a2460-574a-49f0-a5f0-b19b270b902d) </p>

After setting the port class and use, we are ready to extract the lef file </p>

Extracting the lef file </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/3ecae684-9528-47a6-85e0-860fa792139f) </p>



Extracted lef file </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/33fec069-17b9-414f-a5af-20f8d19eaf5a) </p>


**Now we need to integrate the lef file into picorv32a** </p>

Commands added to config.tcl in picorv32a folder to include our sky130A_inv standard cell </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/fae7ccb8-1250-47ad-9e28-130e94477af7) </p>


Integrating the lef file of sky130a_inv into the openlane flow </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/f6e933f3-4f41-41a5-b36f-7487976fe97f) </p>



Executing synthesis again. We see that there are 1554 instances of our inverter </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9ab41a85-cc96-477a-8655-901d8ccea603) </p>


There is a slack violation here and the violation is -23.89 </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/f73bf235-5be0-41a6-ad0c-cedc02673678) </p>


Chip area = 147712.918400 </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/fe7a6968-09f2-41c2-9c4f-a937ebb327f7) </p>


To fix the slack violation we need to edit the synthesis strategy and other related variables: </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/6b927a85-c852-4a17-b1c2-ff4e27ec0b10) </p>


SYNTH_STRATEGY – sets synthesis priority to delay reduction or area reduction </br>
SYNTH_BUFFERING – Enables abc cell buffering, Enabled = 1, Disabled = 0 </br> 
SYNTH_SIZING - Enables abc cell sizing (instead of buffering), Enabled = 1, Disabled = 0 (Default: `0`) </br>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/354ce7c0-d495-441e-ac69-f107367e8f82) </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/fdf7cf8b-6f64-4749-a830-32b6a6b7c191) </p>




|| before | after |
|:---: | :---:  | :-: |
|tns|-711.59|0|
|wns|-23.8|0|
|chip area|147712.91|181730.54 |

Chip area has increased as per chosen strategy </p>

Integrated “sky130_himinv“ into the merged.lef file </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/ced0c6e3-b476-461c-927b-dc383f55229b) </p>


**Running floorplan via organic method**
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/53bbf839-ae6f-4166-8839-bdea6b586a81) </p>


executing placement </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/7a9fc9b3-506f-489b-b8ee-7ba2bc24f497) </p>


**Post synthesis STA analysis** 

````
sta pre_sta.conf
````

initial results </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/829b2ccb-cfdb-4bdc-b4af-5a1b726566a4) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/b93534d2-593e-4eb3-90ef-fb8480b8a28c) </p>



Changing the max fanout value for meeting timing requirements </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/f4ecd695-8f01-4d7c-94f0-fbfd8ead28c3) </p>


**New STA results**  
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/d368820c-debb-49b5-8bd6-501d53b76526) </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9103202c-cb50-490d-9c52-ecccaa599b68) </p>


As we can clearly see in the picture below, slack is met. We didn’t need to replace any standard cells. This is because we executed the synthesis flow using combination of SYNTH_STRATEGY = DELAY3 and MAX_FANOUT = 4.   </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/6a374a12-43b5-4808-9d34-ab5b399f5fed) </p>



**Setup Slack – 8.11, Hold slack – 0.24** </p>

**Commands to do basic timing ECO** </p>

To Reports all the connections to a net
```
report_net -connections _11672_
```
To Check the command syntax
```
help replace_cell
```
To Replace the cell
```
replace_cell _14510_ sky130_fd_sc_hd__or3_4
```
To Generate the custom timing report
```
report_checks -fields {net cap slew input_pins} -digits 4
```

### Running CTS

Variables defining the CTS stage </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/d1da8b5c-547a-45bb-99ed-898f2ba56cf7) </p>

executing run_cts </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/82711313-112f-4fbb-a1fd-5bded3708f59) </p>

new cts file created in synthesis folder </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/998a35fe-7217-4b1c-bdca-b633b22e4d9d) </p>

verifying CTS step by checking values of the environment variables of CTS </p>

Note – CTS_MAX_CAP is the capacitance value of the largest clock buffer. The largest clock buffer was known from echoing the variable CTS_ROOT_BUFFER. (indicated by arrows below) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/6de96c41-fb00-429f-99aa-ffd54010eded) </p>

**STA with real clocks** </p>

Real clock circuits have buffers in their path </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/10f02ae9-5f2c-4084-8310-f437ef26d42c) </p>

Entering Openroad to do the timing analysis. (timing analysis is done by openSTA, openSTA is included in Openroad). </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/70d91965-ab31-48d1-95c8-74a5b31bd59b) </p>

### Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

```
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/04-06_16-16/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/04-06_16-16/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/04-06_16-16/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit

```

**Setup slack value for post cts timing analysis**

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/980f52c5-5382-4665-9770-91ea3ce10123) </p>

**hold slack**  </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/8656b484-7dee-494e-981c-03f550eee151) </p>

### Effect of changing clock_buffers on STA

removing clock_buff_1 usage </p> 

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/561a6f6f-32a4-491e-8381-18c98f05c948) </p>

STA results of the design without clock_buf_1 </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/f8cb703b-b475-440b-8dd3-3911418ed3f3) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/9e4b18b5-e439-4414-b402-e05561f82511) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/3e66af83-cc92-424a-8bac-f88d9163531f) </p>

|| before | after |
|:---: | :---:  | :-: |
|setup slack|13.9481|13.9481|
|hold slack|0.1278|0.2774|

clock skew results </p>
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/bb793f53-7cc3-4909-a8fb-b319b0d428f4) </p>

### Power delivery network

```
gen_pdn
```

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/042d77e9-aa7c-46cf-a575-988872f74cad) </p>

The resulting file **14-pdn.def** contains the information from **cts.def** as well as the power distribution network. </p>

PDN layout result </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/b09ca435-6e5f-4bf9-a5fa-33e3ff36b591) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/ce91293c-79d7-4397-9dea-1309f7a2577d) </p>

## Day 5 Labs : Routing 

1. Global routing - fast route tool
2. Detail routing - Triton route tool 


Input files in routing stage:
1. LEF files
2. DEF files
3. Preprocessing route guides – what you get after global routing with the fast route tool

**Executing routing by command**

```
run_routing
```
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/d51a3f7d-0495-47c8-a845-befc661eb8b5) </p>

routing completed </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/4957bf77-0e14-4de4-bf99-4f5d22d35abd) </p>
 
**Routed design snapshot by tool** </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/935e1e4a-e723-4f91-9f7e-688674654deb) </p>

**Routed layout in magic tool** </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/f69896d8-2d90-4237-8187-86eed46931c6) </p>

zoomed in </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/378c3d7a-24c7-49eb-8db4-e691c53fb5f7) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/cc646a3c-0007-4e62-8176-c2310211cf51) </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/67011ce0-0247-4600-b8ad-7422d9ae9f11) </p>

extracted spef file as a part of routing process. </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/323ecd30-4fa2-4ec2-b9b5-c1b294dc7cc4) </p>

### Post routing STA

Hold slack </p>

![image](https://github.com/himansh107/openlane_workshop/assets/75253218/a2a13ec0-cec5-4c00-a501-e235ed4769ab) </p>

Setup slack </p>
 
![image](https://github.com/himansh107/openlane_workshop/assets/75253218/2fb9340a-0cd6-4763-b66b-5cb7dc526095) </p>







































 

























