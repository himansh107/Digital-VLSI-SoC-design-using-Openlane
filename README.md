# Openlane_workshop

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


## Day 1 Labs : Open Lane directory structure in detail

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

### Floorplan 

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
 


### Cell design flow 

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


### MAGIC tutorial 

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



![image](https://github.com/himansh107/openlane_workshop/assets/75253218/d00b5ff7-9f2d-4934-87d6-38ed2f80cf0b)
 </p>







 

























