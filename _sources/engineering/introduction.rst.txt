============
Introduction
============

The engineering modules in FREDA
comprise of the following analyses and codes:

* Neutronics (OpenMC)
* Computational Fluid Dynamics (OpenFOAM)
* Magnet Analysius (Elmer, MFEM-FE)
* Structural Mechanics (DIABLO, MFEM-FE)
* Heat Transfer (OPENFOAM, Diablo, MFEM-FE)
* Species Transport (OPENFOAM)

-----------
Neutronics
-----------

Neutronics simulations are performed using OpenMC, 
a state-of-the-art Monte Carlo particle transport code (`openmc github`_).
OpenMC is capable of simulating neutron and photon transport in complex
geometries and materials, making it suitable for reactor
physics calculations, radiation shielding analysis, and other
applications involving neutron interactions. It also has a comprehensive
Python API that allows for easy integration with other tools and
customization of simulations.

.. _openmc github: https://github.com/openmc-dev/openmc

The geometry used for neutronics simulations in FREDA is
Direct Accelerated Geometry Monte Carlo (DAGMC) geometries,
which allows direct utilization of Compuated-aided Design (CAD)
files for particle transport.

Cubit is heavily used in FREDA to create and manipulate geometries
(:ref:`geometry-section`). 

Below is a list of metrics obtained from neutronics simulations in FREDA:

* Neutron flux distribution
* Reaction rates
* Tritium breeding ratios
* Dose rates (prompt, shutdown)
* Material activation
* Heat deposition

The spatial discretization of these metrics can be customized:

* Global
* CAD volume (i.e., OpenMC cell)
* Structured mesh (cartesian, cylindrical, spherical)
* Unstructured mesh (tetrahedral)

For FREDA, the neutronics simulations are automated
using utility functions for frontend and backend operations.
Further usage details can be found in the Python API docs (#! api docs here),
workflow docs (#! workflow docs here), and example notebooks (#! example notebooks here).

-----------
CFD
-----------

-----------
MAGNET ANALYSIS
-----------

The open-source code Elmer (`elmer wikipedia`_, `elmer github`_) is being employed for magnet analysis

.. _elmer wikipedia: https://en.wikipedia.org/wiki/Elmer_FEM_solver

.. _elmer webpage: https://www.elmerfem.org/blog/

.. _elmer github: https://github.com/ElmerCSC/elmerfem

Elmer (`elmer webpage`_) contains a coil solver which has special treatments available for continuous coils,
a steady-state magnet field solver, and a structural solver to examine the induced stresses from
the magnet field.

The FREDA team is building an open-source extenson of MFEM (`mfem webpage`_, `mfem github`_) specialized for Fusion-Energy applications, MFEM-FE.
MFEM is a free, lightweight, scalable C++ library for finite element methods.  MFEM is designed for scalability on
heterogeneous architectures (CPUs and GPUs) and incorporates natively AMR and higher-order Finite-Element spaces.

.. _mfem webpage: https://mfem.org/
.. _mfem github: https://github.com/mfem/mfem/ 

MFEM-FE-M is the magnet analysis module, which is based on the TESLA miniapp (`mfem_tesla webpage`_), but incorporating a coil solver and input/output features designed around magnet analysis, similar to Elmer but incorporating the possibility of AMR, higher-order methods,
and scalability on heterogeneous architectures.  The documentation for MFEM-FE-M is still being developed,
but the baseline capabilities in terms of magnetics follow those of the Tesla miniapp. 

.. _mfem_tesla webpage: https://mfem.org/electromagnetics/

-----------
STRUCTURAL ANALYSIS
-----------

FREDA's structural analysis capability is based on Diablo, an export-controlled structural analysis code which has been developed over a long period of time at Lawrence Livermore National Laboratory.  A descriptions follows:

DIABLO: Multiphysics finite element code for primarily implicit static and dynamic multi-physics including solid mechanics, heat transfer, multispecies diffusion, and quasi-electrostatics using distributed-memory parallelism via MPI on large-scale DOE clusters.  Can be co-executed with OPENFOAM and/or Nek5000 for coupled fluid-structure interaction, conjugate heat transfer, and quasi-electrostatics.  Includes state of the art solvers for contact mechanics and an array of linear and quadratic hexadron and tetrahedron element types with AMR as well as structural elements (beams and shells).  Diablo's use in FREDA follows that of it's use in FERMI (`diablo publication`_): 

.. _diablo publication: https://www.tandfonline.com/doi/full/10.1080/15361055.2022.2151818
  
LLNL is developing a structural mechanics module of MFEM-FE, again based upon the MFEM framework
(see the discussion in MAGNET ANALYSIS).  This work is also expected to leverage the developments (also based on MFEM) of
SMITH/SERAC (`smith github`_): 

.. _smith github: https://github.com/LLNL/smith 

Diablo is coupled to OPENFOAM for Fluid-Structure Interaction via PreCICE (`precice webpage`_), and this capability is being added to MFEM-FE.

.. _precice webpage: https://precice.org/  

-----------
HEAT TRANSFER
-----------

FREDA's heat transfer analysis capability is based upon a combination of OPENFOAM, Diablo, and MFEM-FE (see the MAGNET ANALYSIS and STRUCTURAL ANALSIS discussions).  OPENFOAM and Diablo can be coupled together for Conjugate Heat Transfer (CHT), again using PreCICE.  
