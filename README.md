## Description

Developed a heat pump co-simulation framework using Simulink & GT-SUITE based on a reference thermal architecture. The model consists of three main subsystems: **Refrigerant Cycle**, **Coolant Cycle**, and **Controls**. The control algorithms are implemented in Simulink within the **Controls** subsystem.

The refrigerant system is modeled in GT-SUITE and coupled with Simulink to improve simulation accuracy. The working fluid in this system is **R717 (Ammonia)**. The condenser is a tube-and-fin heat exchanger that rejects heat from the refrigerant to ambient air. The air flow is driven by a fan system. A liquid receiver is used to store refrigerant and ensure that only subcooled liquid is supplied to the expansion valves. The evaporator is also a tube-and-fin heat exchanger, where the refrigerant absorbs heat from ambient air and provides dehumidification under humid conditions.

The compressor models simulated in this work are representative of various industrial and thermal management compressor technologies, including **centrifugal turbocompressors** and industrial heat pump compressors commonly used in process cooling and high-capacity heat pump applications. Two different modeling approaches are employed: (i) a simplified 1D map-based model; and (ii) a hybrid 3D-to-1D reduced-order modeling approach.

## Centrifugal Turbocompressor System (process gas/air)

The compressor drives the refrigerant or process gas flow in the system and is a key component in both industrial heat pump and centrifugal gas compression applications. Depending on the application, different compressor technologies are considered, including scroll compressors for small thermal systems, and high-speed centrifugal turbocompressors for industrial air and process gas systems.

For industrial centrifugal turbocompressors, the compressor is typically modeled using a high-fidelity 1D or hybrid 3D-to-1D approach. In this method, a detailed geometric representation (e.g., CAD-based impeller and diffuser geometry or reduced-order mean-line data) is used to derive flow area, volume, and loss distributions along the flow path. This enables the construction of a physics-based one-dimensional model capable of capturing the essential thermodynamic and fluid dynamic behavior of the machine while maintaining computational efficiency.

Compressor maps are commonly used to parameterize the reduced-order model. These maps are M-by-N matrices, where M corresponds to the number of corrected rotational speed lines and N represents discretized operating points along each speed line. The indexing structure is defined by a corrected speed vector of length M and an auxiliary coordinate β ranging from 0 to 1, which describes the progression along each speed line from surge to choke conditions.

The following figure illustrates a typical centrifugal compressor map used for model parameterization in system-level simulations:
<p align="center">
<img width="698" height="520" alt="CompressorMapExample_01" src="https://github.com/user-attachments/assets/27ff4b09-ec5d-40f5-b6fc-30222b6f8713" /> 

 
<p align="center">
  <strong>1D Centrifugal Compressor Simulation</strong>
<p align="center">
<img width="537" height="278" alt="CompressorMapExample_05" src="https://github.com/user-attachments/assets/6c6b552a-174d-4494-b3c3-678262877813" />

 <p align="center">
  <strong>GT-SUITE & Simulink Co-Simulation of Industrial Compressors</strong>
  
  <p align="center">
<img width="1280" height="542" alt="GT-SUITE_blocks" src="https://github.com/user-attachments/assets/46b17975-6716-4d76-bbdf-1696ac41fe68" />

 The contour plot shows that the interpolated efficiency contours match the source data. Additionally, the points are now in a format that the compressor block can use. If the generated compressor map has oversimplified contours or is missing key details, re-select your data using a denser mesh. You may need to select more points along each speed line, select points along estimated additional speed lines, or select more data points for each efficiency contour level.

Because this process interpolates data, there may be some precision loss. The only way to avoid this precision loss is to obtain the original raw test data used to generate the maps. Such data should contain a complete collection of corrected mass flow rates, pressure ratios, and efficiency values at a variety of operating conditions and speeds. This data, and the data used by the compressor block, is specified in the auxiliary coordinate system defined by the corrected speed, and the auxiliary coordinate β.
<p align="center">
<img width="560" height="337" alt="CompressorMapExample_04" src="https://github.com/user-attachments/assets/bf81af34-33f4-4128-9864-5d9b931b5e09" />




The compressor performance is defined using manufacturer-provided maps or experimentally calibrated data, which describe key variables such as mass flow rate, pressure ratio, and isentropic efficiency as functions of suction conditions, discharge pressure, and rotational speed. These maps inherently account for internal irreversible effects, including leakage, incidence losses, and heat transfer, without explicitly resolving the internal three-dimensional flow field. As a result, the compressor is treated as a black-box or grey-box component, enabling efficient and robust system-level simulation of both vapor compression heat pumps and gas compression systems.

This modeling approach is widely used for centrifugal compressors in industrial air and process gas applications, as well as for high-capacity heat pump systems, where dynamic interaction with the cycle is critical. It provides fast numerical convergence, low computational cost, and is well suited for control design, transient analysis, and parametric studies.

The refrigerant or gas compressor can be implemented in simulation environments such as GT-SUITE or Simulink/Simscape using dedicated thermo-fluid and turbomachinery libraries. These models capture key behaviors including mass flow rate, pressure ratio, efficiency, and dynamic shaft response under varying operating conditions. Additional sub-models may be included to represent mechanical losses, rotational inertia, and control system interactions.

One of the main challenges in co-simulation environments is the time-step mismatch between system-level and high-fidelity solvers. This is typically addressed using coupling strategies such as fixed-step synchronization, interface variable filtering, or reduced-order surrogate models.

Due to licensing constraints, detailed proprietary compressor models cannot be shared. Therefore, only representative schematics, parameterization methods, and co-simulation architectures are provided.

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
The figure below illustrates how the refrigerant system interacts with the other components of the thermal management architecture in the Simulink.

In this project, only the compressor model of GT-SUITE was linked to Simulink. The figure below illustrates the virtual vehicle developed using Simscape and its add-on products.  

![Sim_diagram](https://github.com/user-attachments/assets/9ac5de1f-6cb7-4017-9ff3-9ec4309d36f7)


These signals enable real-time interaction between the compressor model and the system-level.

### Main Thermodynamic Output
The main thermodynamic output of the calculations is the **P–h diagram** of the heat pump cycle, as shown in the figure below. The calculated COP (based on refrigerant enthalpy difference (cycle COP)) is 5.2 at a **condensing temperature of 40 °C**, which is a reasonable value for this operating condition. It should be noted that the reported COP was calculated solely based on the refrigerant-side enthalpy differences across the compressor and condenser. The electrical power consumption of the compressor drive, condenser fan, cabin blower, and other auxiliary components was not included in the calculation. Therefore, the presented value represents the cycle (thermodynamic) COP rather than the overall system COP, and the actual system-level COP of the heat pump would be lower. For detailed information, please refer to the [results](results) folder of this project, where enthalpy, entropy and temperature values for different parts of the cycle are provided.

<p align="center">
<img width="731" height="523" alt="530534618-effea2f7-4077-4bf4-82ef-bf2cce446ec7" src="https://github.com/user-attachments/assets/822cede5-fdb1-4b8e-8a0d-c17b794ed56b" />


## Post-processing

<strong style="color:red;">Simulation Results from Simscape Logging</strong>

<img width="959" height="577" alt="BraytonCycleGasTurbineExample_05" src="https://github.com/user-attachments/assets/317acdc4-4f66-4287-827e-1f1a54f96aa2" />


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
 
