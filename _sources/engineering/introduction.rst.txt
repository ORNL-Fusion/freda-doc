============
Introduction
============

The engineering modules in FREDA
comprise of the following analyses and codes:

* Neutronics (OpenMC)
* Computational Fluid Dynamics (OpenFOAM)

  * Heat Transfer
  * Fluid Flow
  * Species Transport

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
