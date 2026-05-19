## Description

Developed a heat pump co-simulation framework using Simulink & GT-SUITE, based on a reference thermal architecture. The model comprises different subsystems:
**Refrigerant Cycle**, **Coolant Cycle** and **Controls**. The control algorithms are implemented in Simulink and are contained in the **Controls** subsystem.

The refrigerant system was modeled in GT-SUITE and coupled with Simulink to improve simulation accuracy. The working refrigerant in this system is **R717**. The condenser is a tube-and-fin type heat exchanger that dissipates refrigerant heat to the air. The air flow is driven by the fan. The liquid receiver provides storage for the refrigerant and permits only subcooled liquid to flow into the expansion valves. The evaporator is a tube-and-fin type heat exchanger that lets the refrigerant absorb heat from the air. It also dehumidifies the air when the air is humid. The compressor models simulated in this work are representative of various industrial and thermal-management compressor technologies, including **Centrifugal turbocompressors**, and industrial heat-pump compressors commonly used in process cooling, and high-capacity heat-pump applications. Two different approaches are employed to simulate the compressors: (i) a simplified 1D map-based model; and (ii) a hybrid 3D-to-1D modeling approach.

### Compressor

The compressor drives the refrigerant or process gas flow in the system and is a key component in both industrial heat pump and centrifugal gas compression applications. Depending on the application, different compressor technologies are considered, including scroll compressors for automotive thermal systems, and high-speed centrifugal turbocompressors for industrial air and process gas systems.

For industrial centrifugal turbocompressors, the compressor is typically modeled using a high-fidelity 1D or hybrid 3D-to-1D approach. In this method, a detailed geometric representation (e.g., CAD-based impeller and diffuser geometry or reduced-order mean-line data) is used to derive flow area, volume, and loss distributions along the flow path. This enables the construction of a physics-based one-dimensional model capable of capturing the essential thermodynamic and fluid dynamic behavior of the machine while maintaining computational efficiency.

Compressor maps are commonly used to parameterize the reduced-order model. These maps are M-by-N matrices, where M corresponds to the number of corrected rotational speed lines and N represents discretized operating points along each speed line. The indexing structure is defined by a corrected speed vector of length M and an auxiliary coordinate β ranging from 0 to 1, which describes the progression along each speed line from surge to choke conditions.

The following figure illustrates a typical centrifugal compressor map used for model parameterization in system-level simulations:
<p align="center">
<img width="698" height="520" alt="CompressorMapExample_01" src="https://github.com/user-attachments/assets/27ff4b09-ec5d-40f5-b6fc-30222b6f8713" /> 
<p align="center">
<img width="840" height="415" alt="CompressorMapExample_05" src="https://github.com/user-attachments/assets/469ee5f6-096c-4bf0-9c4f-4926fe951f57" />



The compressor performance is defined using manufacturer-provided maps or experimentally calibrated data, which describe key variables such as mass flow rate, pressure ratio, and isentropic efficiency as functions of suction conditions, discharge pressure, and rotational speed. These maps inherently account for internal irreversible effects, including leakage, incidence losses, and heat transfer, without explicitly resolving the internal three-dimensional flow field. As a result, the compressor is treated as a black-box or grey-box component, enabling efficient and robust system-level simulation of both vapor compression heat pumps and gas compression systems.

This modeling approach is widely used for centrifugal compressors in industrial air and process gas applications, as well as for high-capacity heat pump systems, where dynamic interaction with the cycle is critical. It provides fast numerical convergence, low computational cost, and is well suited for control design, transient analysis, and parametric studies.

The refrigerant or gas compressor can be implemented in simulation environments such as GT-SUITE or Simulink/Simscape using dedicated thermo-fluid and turbomachinery libraries. These models capture key behaviors including mass flow rate, pressure ratio, efficiency, and dynamic shaft response under varying operating conditions. Additional sub-models may be included to represent mechanical losses, rotational inertia, and control system interactions.

One of the main challenges in co-simulation environments is the time-step mismatch between system-level and high-fidelity solvers. This is typically addressed using coupling strategies such as fixed-step synchronization, interface variable filtering, or reduced-order surrogate models.

Due to licensing constraints, detailed proprietary compressor models cannot be shared. Therefore, only representative schematics, parameterization methods, and co-simulation architectures are provided.

### Main Thermodynamic Output
The main thermodynamic output of the calculations is the **P–h diagram** of the heat pump cycle, as shown in the figure below. The calculated COP (based on refrigerant enthalpy difference (cycle COP)) is 5.2 at a **condensing temperature of 40 °C**, which is a reasonable value for this operating condition. It should be noted that the reported COP was calculated solely based on the refrigerant-side enthalpy differences across the compressor and condenser. The electrical power consumption of the compressor drive, condenser fan, cabin blower, and other auxiliary components was not included in the calculation. Therefore, the presented value represents the cycle (thermodynamic) COP rather than the overall system COP, and the actual system-level COP of the heat pump would be lower. For detailed information, please refer to the [results](results) folder of this project, where enthalpy, entropy and temperature values for different parts of the cycle are provided.

<p align="center">
<img width="731" height="523" alt="530534618-effea2f7-4077-4bf4-82ef-bf2cce446ec7" src="https://github.com/user-attachments/assets/822cede5-fdb1-4b8e-8a0d-c17b794ed56b" />


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
 
