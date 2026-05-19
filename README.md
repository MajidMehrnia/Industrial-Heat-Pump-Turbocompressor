## Description

Developed a heat pump co-simulation framework using Simulink & GT-SUITE, based on a reference thermal architecture. The model comprises different subsystems:
**Refrigerant Cycle**, **Coolant Cycle** and **Controls**. The control algorithms are implemented in Simulink and are contained in the **Controls** subsystem.

The refrigerant system was modeled in GT-SUITE and coupled with Simulink to improve simulation accuracy. The working refrigerant in this system is **R1234yf**. The condenser is a tube-and-fin type heat exchanger that dissipates refrigerant heat to the air. The air flow is driven by the fan. The liquid receiver provides storage for the refrigerant and permits only subcooled liquid to flow into the expansion valves. The evaporator is a tube-and-fin type heat exchanger that lets the refrigerant absorb heat from the air. It also dehumidifies the air when the air is humid. The compressor models simulated in this work are representative of various industrial and thermal-management compressor technologies, including **centrifugal turbocompressors**, and industrial heat-pump compressors commonly used in process cooling, and high-capacity heat-pump applications. Two different approaches are employed to simulate the compressors: (i) a simplified 1D map-based model; and (ii) a hybrid 3D-to-1D modeling approach.

### Main Thermodynamic Output
The main thermodynamic output of the calculations is the **P–h diagram** of the heat pump cycle, as shown in the figure below. The calculated COP (based on refrigerant enthalpy difference (cycle COP)) is 5.2 at a cabin temperature of 40 °C, which is a reasonable value for this operating condition. It should be noted that the reported COP was calculated solely based on the refrigerant-side enthalpy differences across the compressor and condenser. The electrical power consumption of the compressor drive, condenser fan, cabin blower, and other auxiliary components was not included in the calculation. Therefore, the presented value represents the cycle (thermodynamic) COP rather than the overall system COP, and the actual system-level COP of the electric vehicle heat pump would be lower. For detailed information, please refer to the [results](results) folder of this project, where enthalpy, entropy and temperature values for different parts of the cycle are provided.

![P-h_SIM](https://github.com/user-attachments/assets/effea2f7-4077-4bf4-82ef-bf2cce446ec7)


## Refrigerant System 

The refrigerant cycle comprises compressor, condenser, chiller expansion valve (EV1), evaporator 
expansion valve (EV2), chiller, and evaporator. 

The refrigerant flow is driven by the compressor, which is connected to the HV electrical network. 
The refrigerant flow continues to the condenser where heat is dissipated to the air. The air flow 
within the condenser is driven by the vehicle speed the condenser fan. Latter is connected to the 
LV network.

The refrigerant then flows through EV1 and EV2 and continues to chiller and evaporator. In the 
chiller, the refrigerant absorbs heat from the coolant cycle. In the evaporator the refrigerant absorbs heat from the cabin air and continues its way back to the compressor. R1234yf is used to accurately represent phase-change and thermodynamic behavior.



### Refrigerant System Architecture (GT-SUITE)

The GT-SUITE model represents the thermodynamic and mechanical behavior of the Heat Pump, while Simulink manages the system-level control and signal exchange. The model simulates the thermodynamic behavior of a closed-loop refrigerant cycle and is intended for system-level performance analysis and component evaluation. The refrigerant circulates through the system, undergoing pressure and phase changes to absorb heat from the evaporator and reject it through the condenser.

The figure below shows H/P system architecture in GT-SUITE. 


![Refrig_GT1](https://github.com/user-attachments/assets/46a3b2f9-04ed-427c-8833-e348070fcffc)




#### Compressor
The compressor drives the flow in the refrigerant loop. It is controlled to maintain a pressure of 0.3 MPa in the chiller and the evaporator, which corresponds to a saturation temperature of around 1°C. 
For a centrifigual compressor, a novel direct approach involves using a 3D CAD model of the fixed and orbiting scrolls to obtain volume and area profiles for the construction of a one-dimensional fluid dynamic model. The figure below presents a high-fidelity 1D hybrid approach for simulating a  turbocompressor, demonstrating excellent agreement with test data.

Compressor maps are M-by-N matrices where the number of rows, M, is equal to the number of corrected speeds and the number of columns, N, is equal to the number of points along each corrected speed line. The indexing vectors for these matrices are a corrected speed vector of length M and a vector for the auxiliary coordinate system parameter β of length N with values ranging from 0 to 1.

This figure shows a compressor map. It was used this compressor map to parameterize a Compressor block.
<img width="698" height="520" alt="CompressorMapExample_01" src="https://github.com/user-attachments/assets/27ff4b09-ec5d-40f5-b6fc-30222b6f8713" />

<img width="1006" height="519" alt="BraytonCycleGasTurbineExample_01" src="https://github.com/user-attachments/assets/fcd2d1ad-218b-4aea-a254-6daa41367c0b" />


The compressor performance is defined through manufacturer-provided performance maps, which describe the refrigerant mass flow rate and compressor power consumption as functions of suction and discharge pressures, suction temperature, and compressor rotational speed. These maps inherently account for internal losses, including leakage, and heat transfer effects, without explicitly resolving the internal flow field. The compressor is therefore treated as a black-box component, enabling efficient and robust system-level simulations of the two-phase heat pump cycle. This modeling approach provides fast convergence and low computational cost, making it well suited for parametric studies and control-oriented analyses.

![GT-SUITE_blocks](https://github.com/user-attachments/assets/fef207ef-334b-4d06-adff-b759e48033e8)


The refrigerant compressor is modeled and simulated in GT-SUITE using its dedicated thermo-fluid and mechanical component libraries. 
The model captures key compressor behaviors, including mass flow rate, pressure ratio, efficiency, and dynamic response under varying operating conditions. 
Different sub-models are employed to represent thermodynamic processes, mechanical losses, and control-relevant dynamics. One of the main challenges in this co-simulation was handling the time-step mismatch between the two models.Detailed compressor model implemented in GT-SUITE and coupled with Simulink through co-simulation. 

Due to GT-SUITE licensing restrictions, the original model files cannot be shared publicly. 
Therefore, only representative diagrams, descriptions, and co-simulation interfaces are provided in this repository.




## Thermal Management System Design  

This repository is based on a thermal management model originally developed in Simulink [1].

In addition to the Simulink implementation, the refrigerant-based thermal management system is also modeled independently in GT-SUITE. This enables a detailed system-level representation of the vapor compression cycle, including the compressor, condenser, expansion device, and evaporator, with high-fidelity thermodynamic and component performance modeling.

The combined use of **Simulink** (control-oriented modeling) and **GT-SUITE** (1D multi-physics system simulation) provides a comprehensive multi-fidelity framework for thermal system design.

To enhance model fidelity and accelerate the design process, a novel **ML**-based thermodynamic modeling approach has been developed and validated against **CFD** simulations. Related CFD and AI/ML developments are available in my companion repository: 
[Physics-Informed AI/ML for Thermodynamic Modeling](https://github.com/MajidMehrnia/Physics-Informed-AI-ML-for-Thermodynamic-Modeling)




## System Simulation
The figure below illustrates how the refrigerant system interacts with the other components of the vehicle thermal management architecture in the Simulink.

![Refrig_System](https://github.com/user-attachments/assets/bdc71a1a-0043-4013-a684-7a9282a2def7)

In this project, due to CPU limitations, only the compressor model of GT-SUITE was linked to Simulink. The figure below illustrates the virtual vehicle developed using Simscape and its add-on products.  

![Sim_diagram](https://github.com/user-attachments/assets/9ac5de1f-6cb7-4017-9ff3-9ec4309d36f7)


These signals enable real-time interaction between the compressor model and the system-level of BEV. 
The served fluid is a two-phase refrigerant. More information is available at [2].



## Post-processing

The post-processing of the thermal simulation results focuses on resolving heat flux distributions within the battery pack and their coupling to the coolant loop. Cell-level heat generation is computed internally from the electrical model and exported as a time-resolved thermal power signal, which is subsequently mapped to the thermal network. The resulting heat flux at the cell–cooling plate interface represents the effective thermal load imposed on the BTMS and varies strongly with drive cycle transients. Peak heat flux events correlate with high current demand phases rather than steady-state operation, highlighting the importance of transient thermal capacity over nominal cooling power when sizing the thermal interfaces.

![HF-1](https://github.com/user-attachments/assets/deccfc83-95e9-4024-8bff-fa5597fe9d16)


![T_vs_t_Motor_Battery](https://github.com/user-attachments/assets/4584f388-d131-4fe7-b464-d656eb21eac0)


Temperature distribution results indicate that maximum cell temperature is not governed by total heat generation alone, but by the internal thermal resistance chain spanning cell core, casing, interface materials, and cooling plate conduction. Post-processed temperature gradients across the battery module reveal that once coolant inlet temperature and flow rate exceed a threshold, further improvements in convective heat transfer yield diminishing returns. This confirms that the system operates in a conduction-limited regime during high load events, where contact resistance and through-plane conductivity dominate over fluid-side heat transfer coefficients. Consequently, regions of elevated temperature align spatially with regions of highest heat flux density rather than with coolant flow maldistribution.

From an energy distribution perspective, the BTMS post-processing shows that a non-trivial portion of the generated thermal energy is managed through active cooling work, introducing secondary losses via pumps and the refrigeration cycle. By integrating heat flux over time and comparing it to BTMS electrical power consumption, the simulation allows quantification of the effective thermal efficiency of the system. These results emphasize that optimal BTMS operation is achieved not by minimizing absolute cell temperature, but by limiting thermal gradients and peak heat flux while avoiding excessive auxiliary energy consumption. This framework enables informed trade-off studies between thermal performance, aging mitigation, and vehicle-level energy efficiency.

## Support
For any questions regarding the model place a comment in the repository.

## License
The model requires the following products:

- MATLAB® R2024a: Simulink®; Simscape™; Simscape Fluids™; Simscape Battery™; Simscape Driveline™; Simscape Electrical™; Stateflow®
- GT-SUITE

See [license](LICENSE.md) file attached to this repository

## Project status
In development

## Sources
[1] A Holistic Approach for Designing a Battery Electric Vehicle Thermal Management System, 
Steve Miller, Lorenzo Nicoletti

[2] The MathWorks. “Electric Vehicle Thermal Management - MATLAB & Simulink.”, Available 
[here](https://www.mathworks.com/help/hydro/ug/sscfluids_ev_thermal_management.html). 
 
