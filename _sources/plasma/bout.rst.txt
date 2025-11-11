IPS-BOUT: BOUT++ / Hermes-3 Interface
=====================================

Overview
--------

IPS-BOUT is a component interface that connects the BOUT++ framework
and Hermes-3 plasma simulation codes to the Integrated Plasma
Simulator (IPS) framework. This integration enables sophisticated
multi-physics fusion reactor simulations as part of the FREDA (Fusion
REactor Design and Assessment) SciDAC project.  Repository:
https://github.com/boutproject/ips-bout

What are BOUT++ and Hermes-3?
-----------------------------

**BOUT++** is a flexible framework for simulating plasma fluid equations in curvilinear coordinate systems. It provides:

* Support for 1D, 2D, and 3D simulations
* Advanced numerical methods for solving PDEs
* Parallel execution using MPI
* Extensive library of physics models and numerical schemes

Repository: https://github.com/boutproject/BOUT-dev
  
**Hermes-3** is a multifluid plasma simulation model built on BOUT++,
 specifically designed for:

* Edge plasma modeling: Transport and turbulence in tokamak boundary regions
* Multi-species simulations: Arbitrary numbers of ion and neutral species
* Runtime configuration: Flexible setup without code recompilation
* Transport and turbulence: Both steady-state and time-evolving problems

Repository: https://github.com/boutproject/hermes-3

References
----------

**BOUT++** B.D. Dudson, M.V. Umansky, X.Q. Xu, P.B. Snyder,
H.R. Wilson, "BOUT++: A framework for parallel plasma fluid
simulations" Computer Physics Communications (2009), DOI:
`10.1016/j.cpc.2009.03.008
<https://doi.org/10.1016/j.cpc.2009.03.008>`_

**Hermes-3**: B. Dudson, M. Kryjak, H. Muhammed, P. Hill, J. Omotani,
 "Hermes-3: Multi-component plasma simulations with BOUT++", Computer
 Physics Communications (2024), DOI: `10.1016/j.cpc.2023.108991
 <https://doi.org/10.1016/j.cpc.2023.108991>`_
