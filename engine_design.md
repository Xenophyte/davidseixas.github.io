---
layout: page
title: Jekyll Theme - About - Massively
description: When building a website it's helpful to see what the focus of your site is. This page is an example of how to show a website's focus.
sitemap:
    priority: 0.7
    lastmod: 2018-04-28
    changefreq: weekly
---

## A tutorial in engine design

<span class="image left"><img src="https://img.purch.com/h/1400/aHR0cDovL3d3dy5zcGFjZS5jb20vaW1hZ2VzL2kvMDAwLzAwMi80NDUvb3JpZ2luYWwvMDcwNTA3X21ldGhhbmVfcm9ja2V0XzAyLmpwZw==" alt="" /></span>
Designing an engine can be a daunting task specially considering that the resources to learn the process are quite scarce. Here I'll try to describe the process of designing the engine to fulfill your mission requirements as I discover it myself. Here you'll find detailed calculations, useful links and references and hopefully soon, a description of the manufacturing, assembly and test process of the engine.

### Defining your mission objectives
Liquid propellant engines are the standard choice for orbital-class launchers since they have higher performance and are throttleable. Sometimes solid boosters are used as a first stage, but these are much simpler in design and basically run in berzerk-mode until they burn out, providing additional thrust to the rocket during the initial phase of the launch when the weight of the craft is higher.
<div class="box">
  <p>
  Our goal is to have a lander hover for 20 seconds at a few meters height. Since the craft will be essentially static, this is equivalent to a delta-v of
  $$
  \Delta v = g_0\,t_\text{burn}
  = (9.81\text{m/s}^2)\times(20\,\text{s}) = 196.2\,\text{m/s}
  $$
  So we need a delta-v of 200 seconds.
  </p>
</div>
<span class="image right"><img src="http://masten.aero/wp-content/uploads/2015/01/09-09-16_Masten_xombie_VTVL-2502.jpg" alt="" /></span>
### Lander Mass

Our lander is composed of eight parts:

1. control system
2. feed system (pipes and valves)
3. pressurization gas tank
4. fuel tank
5. oxidizer tank
7. engine
8. frame

Based on similar landers, such as Masten's Xombie and Armadillo Aerospace's Pixel we expect the GN&C + feed system + frame to weigh about 20 kg. The oxidizer and fuel tanks will be modelled as spheres and the pressurization gas (probably Helium) will be modelled as a cylinder. Our vehicle must satisfy these two constraints:

<div class="box">
  <p>
  Our goal is to have a lander hover for 20 seconds at a few meters height. Since the craft will be essentially static, this is equivalent to a delta-v of
  $$
  c\,\ln\left(\frac{m_i}{m_f}\right) > 200\,\text{seconds}\quad\text{and}\quad
  \dot{m}c > m_ig_0
  $$
  </p>
</div>

### CEA

Our choice of propellants will be based on availability and price. Therefore we will chose liquid oxygen and gasoline, which is a terrible choice in terms of performance but is cheap and readily available. We use NASA's Combustion Equilibrium Analysis (CEA) software with a python wrapper to compute the specific impulse as a function of several parameters:

1. chamber pressure
2. O/F mass flow ratio
3. chamber-to-throat area ratio

The code for this can be found in my [github repository](https://github.com/Xenophyte/preliminary_rocket_design).

#### O/F ratio

<span class="image right"><img src="{{ "/images/graph1.png" | absolute_url }}" alt="" /></span>

Here we have assumed a chamber pressure of 250 psia (1.724 MPa), and plotted the specific impulse as a function of the mass ratio. We also plot the chamber temperature (at the injector head) as a function of this parameter, as a higher temperature is usually a negative side-effect of higher performances. However, fortunately, the performance peak happens at a fuel-rich mass ratio (~2.43) where the temperature is lower by almost a hundred degrees Kelvin relative to its maximum value which happens closer to the stochiometric ratio (at ~3). This peak off the stochiometric ratio is due to species with less degrees of freedom (therefore lower calorific capacity) being either produced or preserved, allowing more energy to be stored as kinetic energy under the same entropy conditions. We therefore choose without much further ado a mass matio of 2.43. Gasoline has a density of 0.726 \\(\text{g/cm}^3\\) and liquid oxygen 1.146 \\(\text{g/cm}^3\\) which means a volume ratio of 1.54.

#### Chamber pressure

<span class="image right"><img src="{{ "/images/graph6.png" | absolute_url }}" alt="" /></span>

Here analyse the effect of chamber pressure on performance and chamber temperature. Here it is more difficult to find a trade-off point since specific impulse grows continuously, as expected, with chamber pressure, and so does the combustor's internal temperature. Still, chamber pressures of less than 200 psi (1.379 MPa) clearly lead to unacceptable performance values. Our choice will rely mostly on advice from more experienced people who have suggested that an engine of 250 psi (1.724 MPa) is a good start.

#### Chamber-to-throat area ratio

Finally, the last parameter obtained with NASA's CEA software is the chamber-to-throat area ratio \\(A_c/A_t\\). A wider chamber results in a more complete combustion but offers a larger area to cool and therefore will result in extra weight. Here temperature and specific impulse exhibit the same behavior. Ratios greater than 7 offer little benefit, and ratios lower than 3 will result in significant performance losses.

<p align="center">
  <img width="400" src="{{ "/images/graph7.png" | absolute_url }}">
  <img width="400" src="{{ "/images/graph8.png" | absolute_url }}">
</p>

### Engine design

From our CEA analysis we have found that we want an engine with the following parameters:
* chamber pressure of about 250 psia
* O/F mass ratio of 2.43
* \\(A_c/A_t\\) between 3 and 7

We will temporarily pick \\(P_c =\\) 250 psi, \\(r =\\) 2.43 and \\(A_c/A_t\\) = 5.67. This yields the following results:

* exit velocity (1 atm): 2567.5 m/s
* exit velocity (vac): 2908.8 m/s
* \\(c_\star\\): 1805.7 m/s
* injector temperature: 3369.08 Kelvin
* chamber end temperature: 3366.14 Kelvin
* throat-to-exit area ratio: 3.8352

#### Thrust and mass flows

We now know that our specific impulse is of about 261.72 seconds or equivalently an exit velocity of 2567.5 m/s.
