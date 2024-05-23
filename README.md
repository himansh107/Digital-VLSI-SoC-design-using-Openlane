# Openlane_workshop

## Day1 assignments

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

Most routers a grid router. They construct the routing grid out of the metal layer tracks. 
Routing grid is huge therefore a divide and conquer technique is applied. <br>

-	Global routing – generates routing guides
-	Detailed routing – uses the routing guides to implement the actual wiring

### 6. Sign off (Verification)

1. Design rule checking (DRC) – final layouts honour all design rules
2. Layout vs Schematic (LVS) - The layout vs schematic makes sure that the final layout matches the gate level netlist the we started with. 
3. Timing verification - Static timing analysis 



