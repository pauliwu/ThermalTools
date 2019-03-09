## Outline for the Manual
 
1. [**Background**](#1.-Background) : Introduction to thermal conductivity, <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> of materials
2. [**NEMD simulations**](#2.-NEMD-Simulations) : Calculation of <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> using molecular dynamics simulations
3. [**System size scaling**](#3.-System-size-scaling) : Effect of simulation cell size on calculated <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a>
4. [**Temperature scaling**](#4.-Temperature-scaling) : Effect of system temperature on calculated <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a>
5. [**Thermal conductivity plugins**](#5.-Thermal-conductivity-plugins) : Calculating <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> of MoS<sub>2</sub>
6. [**Quantum corrections**](#6.-Quantum-corrections) : Beyond classical thermal conductivity simulations
    * Velocity autocorrelation in equilibrium simulations
    * Phonon density of states
    * Specific heat of materials
    * Quantum-corrected thermal conductivity
7. [**Summary and take-away messages**](#7.-Summary-and-take-away-messages) : Things to remember when you do your own thermal conductivity simulations
8. [**Current research applications**](#8.-Current-research-applications) : Engineering thermal conductivity of materials. Brief look at thermal conductivity of fractal and alloyed systems
9. [**Downloads/Documentation**](#9.-Downloads-and-Documentation) : Thermal conductivity plugin downloads, Plugin manuals and contributing to software development
10. [**References**](#10.-References)

# 1. Background 


## Introduction to thermal conductivity


* Thermal conductivity (denoted by the symbol <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a>) is a fundamental property of materials that determines their ability to conduct (i.e. transmit) heat (See Refs 1,2). Materials with a higher <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> values conduct heat well and low-<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> materials are more insulating (Ref 9.).

<div align=center>
<h4>Table 1: Thermal conductivity of common materials</h4>
</div>

| Material       | <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /> (W/m-K) |
|----------------|:-------------------------------------:|
| Diamond        | 1000                                  |
| Silver         | 406                                   |
| Copper         | 401                                   |
| Water          | 0.591                                 |
| Wood           | 0.12                                  |
| Wool           | 0.0464                                |
| Air            | 0.025                                 |
| Silica Aerogel | 0.003                                 |


* High-<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> materials are commonly used in heat-sink and thermal-dissipation applications and materials with low thermal conductivity are used primarily for insulation. Low <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> insulating materials are also used for thermoelectric energy harvesting applications (See [Section 8](#8.-Current-research-applications)).

* Both high and low <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a> materials are extremely useful for engineers. 
# 2. NEMD Simulations

### Theory and Equations

As described previously, thermal conductivity is calculated by measuring the temperature gradient along the material. We establish the thermal gradient by adding and removing a predefined quantity of heat, E<sub>0</sub>, at <a href="https://www.codecogs.com/eqnedit.php?latex=x&space;=&space;\frac{L}{4}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x&space;=&space;\frac{L}{4}" title="x = \frac{L}{4}" /></a> and <a href="https://www.codecogs.com/eqnedit.php?latex=x&space;=&space;\frac{3L}{4}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x&space;=&space;\frac{3L}{4}" title="x = \frac{3L}{4}" /></a> respectively. Since we can control the amount, E<sub>0</sub> and frequency of heat input, <a href="https://www.codecogs.com/eqnedit.php?latex=\frac{1}{\Delta&space;t}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{1}{\Delta&space;t}" title="\frac{1}{\Delta t}" /></a> , we effectively control the heat flux in the system. Once the steady-state temperature profile is established in the simulation cell, we combine the temperature profile with the known heatflux to calculate the thermal conductivity.

Specifically,

Heat Flux, <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\displaystyle&space;J_y&space;=&space;\frac{1}{2}&space;\frac{E_0}{\Delta&space;t}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\displaystyle&space;J_y&space;=&space;\frac{1}{2}&space;\frac{E_0}{\Delta&space;t}" title="\large \displaystyle J_y = \frac{1}{2} \frac{E_0}{\Delta t}" /></a>

The factor <a href="https://www.codecogs.com/eqnedit.php?latex=\frac{1}{2}$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{1}{2}$" title="\frac{1}{2}$" /></a> comes from the fact that heat conduction happens along both the +x and -x directions away from the heat source at <a href="https://www.codecogs.com/eqnedit.php?latex=x&space;=&space;\frac{L}{4}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x&space;=&space;\frac{L}{4}" title="x = \frac{L}{4}" /></a>.

Also, from Fourier's law of thermal conduction, we have <a href="https://www.codecogs.com/eqnedit.php?latex=\displaystyle&space;J_y&space;=&space;-&space;\kappa&space;\cdot&space;A&space;\cdot&space;\frac{\mathrm{d}T}{\mathrm{d}x}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\displaystyle&space;J_y&space;=&space;-&space;\kappa&space;\cdot&space;A&space;\cdot&space;\frac{\mathrm{d}T}{\mathrm{d}x}" title="\displaystyle J_y = - \kappa \cdot A \cdot \frac{\mathrm{d}T}{\mathrm{d}x}" /></a>, where A is the cross sectional area of heat transfer. (Ref 6)

Putting these equations together, we have <a href="https://www.codecogs.com/eqnedit.php?latex=\displaystyle&space;\kappa&space;=&space;-&space;\frac{1}{2\&space;A}&space;\frac{E_0}{\Delta&space;t}&space;\frac{\mathrm{d}x}{\mathrm{d}T}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\displaystyle&space;\kappa&space;=&space;-&space;\frac{1}{2\&space;A}&space;\frac{E_0}{\Delta&space;t}&space;\frac{\mathrm{d}x}{\mathrm{d}T}" title="\displaystyle \kappa = - \frac{1}{2\ A} \frac{E_0}{\Delta t} \frac{\mathrm{d}x}{\mathrm{d}T}" /></a>

Note here that the thermal conductivity <a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;\kappa" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;\kappa" title="\large \kappa" /></a>  goes as the _inverse_ of the temperature gradient. More conducting material will have 'flatter' temperature profiles.

<div align=center>
<h4>Image 1: Schematic of NEMD simulations for measuring thermal conductivity of 2D materials</h4>
<img src = "./Media/calculation_schematic.png"  width="500" height="250">
</div>

# 3. System size scaling

## Thermal conductivity is affected by size of the system in NEMD simulations
Length Scaling is a direct consequence because of MD. The thermal transport in NEMD happens over two square regions. Phonons having mean free path largest than the size of the square do not take part in thermal transport in our system setup. As the system increases in size, more and more phonons are being involved in thermal transport. Studies have shown the mean free path for Transition metal Dichalcogenides is of the order of 1000 nm \cite{gandi2016thermal}. It is therefore essential to scale systems to at least one third its value.

These simulations are performed inside the Casimir Limit. This results in thermal conductivity values having a strong size dependence. Following the scaling scheme of Schon which uses Matthiessen's Rule . To predict the thermal conductivity of the infinite system we make use of a linear function such as

<a href="https://www.codecogs.com/eqnedit.php?latex=\frac{1}{\kappa}&space;\propto&space;\frac{1}{\kappa_0}&space;\bigg(&space;\frac{1}{L}&space;&plus;&space;\frac{1}{L_0}&space;\bigg)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{1}{\kappa}&space;\propto&space;\frac{1}{\kappa_0}&space;\bigg(&space;\frac{1}{L}&space;&plus;&space;\frac{1}{L_0}&space;\bigg)" title="\frac{1}{\kappa} \propto \frac{1}{\kappa_0} \bigg( \frac{1}{L} + \frac{1}{L_0} \bigg)" /></a>
Here, <a href="https://www.codecogs.com/eqnedit.php?latex=\frac{1}{L}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{1}{L}" title="\frac{1}{L}" /></a> is the inverse scattering length relating to the heat source-sink distance and <a href="https://www.codecogs.com/eqnedit.php?latex=\frac{1}{L_0}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{1}{L_0}" title="\frac{1}{L_0}" /></a> is the material's inverse scattering length because of phonon phonon scattering. 

It is important to note that length scaling happens in two dimensions. It is important to maintain a 2:1 ratio between length and width while increasing system size. The major difference is that one dimensional scaling ignores the contribution of phonons with wavelengths larger than the smallest dimension of the solid, whereas two dimensional scaling is inclusive.

# 4. Temperature scaling


## Thermal conductivity is affected by the temperature of the system


The system temperature reflects the population of phonons in the material. At higher temperatures, there is a greater chance of phonon scattering (by other phonons) leading to reduced thermal conductivity.

# 6. Quantum corrections

## Going beyond classical thermal conductivity simulations

<h4>Image 4: Experimentally-measured thermal conductivity of two bulk semiconductors, Silicon and Germanium. Taken from Glassbrenner, C. J. and G. A. Slack, Phys. Rev. 134, 4A (1964) A1058-A1069</h4>
<div align=center>
<img src = "./Media/expt_thermal_conductivity.png" width="500" height="250">
</div>

The main discrepancy arises because classical molecular dynamics simulations (like ours) do not correctly describe the specific heat of materials (denoted by C<sub>V</sub>) at low temperatures. There is a method to correct for this deficiency by calculating the quantum-mechanically accurate specific heat of materials separately using equilibrium molecular dynamics simulations. This involves computing the velocity autocorrelation functions, the vibrational density of states and the specific heat, in that order, from the MD simulations, as shown below.


<div align=center>
<h4>Image 5: Workflow for calculating specific heat from equilibrium MD simulations</h4>
<img src = "./Media/cv_workflow.png" width="800" height="250">
</div>

### Velocity AutoCorrelation Functions (VACF) and Vibrational Density Of States (VDOS)

The velocity autocorrelation function Z(t) for atom type <a href="https://www.codecogs.com/eqnedit.php?latex=\alpha" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\alpha" title="\alpha" /></a> is defined as <a href="https://www.codecogs.com/eqnedit.php?latex=\displaystyle&space;Z_{\alpha}(t)&space;=&space;\frac{1}{N}\sum_{i=1}^N{\frac{v_i(t)\cdot&space;v_i(0)}{v_i(0)\cdot&space;v_i(0)}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\displaystyle&space;Z_{\alpha}(t)&space;=&space;\frac{1}{N}\sum_{i=1}^N{\frac{v_i(t)\cdot&space;v_i(0)}{v_i(0)\cdot&space;v_i(0)}}" title="\displaystyle Z_{\alpha}(t) = \frac{1}{N}\sum_{i=1}^N{\frac{v_i(t)\cdot v_i(0)}{v_i(0)\cdot v_i(0)}}" /></a>


The density of states is given by the equation
<br>
<a href="https://www.codecogs.com/eqnedit.php?latex=G(\omega)&space;=&space;\displaystyle&space;\sum_{\alpha}&space;\frac{6N_\alpha}{\pi}&space;\displaystyle\int\limits_{0}^{\infty}&space;Z_{\alpha}(t)cos(\omega&space;t)dt" target="_blank"><img src="https://latex.codecogs.com/gif.latex?G(\omega)&space;=&space;\displaystyle&space;\sum_{\alpha}&space;\frac{6N_\alpha}{\pi}&space;\displaystyle\int\limits_{0}^{\infty}&space;Z_{\alpha}(t)cos(\omega&space;t)dt" title="G(\omega) = \displaystyle \sum_{\alpha} \frac{6N_\alpha}{\pi} \displaystyle\int\limits_{0}^{\infty} Z_{\alpha}(t)cos(\omega t)dt" /></a>
<br>
Here Z<sub><a href="https://www.codecogs.com/eqnedit.php?latex=\alpha" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\alpha" title="\alpha" /></a></sub> is the velocity autocorrelation for each element. G(<a href="https://www.codecogs.com/eqnedit.php?latex=\omega" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\omega" title="\omega" /></a>) is the density of states. If you observe the following equation you can see that the vibrational density of state is simply proportional to the Fourier transform of the velocity autocorrelation. 
<br>


With the density of states available, we can calculate specific heat using the following equation. This simply multiplies the vibrational energy of each state with the temperature derivative of the probability of occupation of each vibrational state.


<a href="https://www.codecogs.com/eqnedit.php?latex=\displaystyle\frac{C_v&space;(T)}{3Nk_B}&space;=&space;\frac{\displaystyle\int\limits_{0}^{\infty}\frac{u^2&space;e^u}{{(e^u&space;-&space;1)}^2}G(\omega)d\omega}{\displaystyle\int\limits_{0}^{\infty}&space;G(\omega)d\omega}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\displaystyle\frac{C_v&space;(T)}{3Nk_B}&space;=&space;\frac{\displaystyle\int\limits_{0}^{\infty}\frac{u^2&space;e^u}{{(e^u&space;-&space;1)}^2}G(\omega)d\omega}{\displaystyle\int\limits_{0}^{\infty}&space;G(\omega)d\omega}" title="\displaystyle\frac{C_v (T)}{3Nk_B} = \frac{\displaystyle\int\limits_{0}^{\infty}\frac{u^2 e^u}{{(e^u - 1)}^2}G(\omega)d\omega}{\displaystyle\int\limits_{0}^{\infty} G(\omega)d\omega}" /></a>
where <a href="https://www.codecogs.com/eqnedit.php?latex=\displaystyle&space;u&space;=&space;\frac{\hbar\omega}{k_B&space;T}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\displaystyle&space;u&space;=&space;\frac{\hbar\omega}{k_B&space;T}" title="\displaystyle u = \frac{\hbar\omega}{k_B T}" /></a>
