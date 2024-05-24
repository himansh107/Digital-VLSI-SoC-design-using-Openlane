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






