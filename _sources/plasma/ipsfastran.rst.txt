IPS-FASTRAN/CESOL: Plamsma backbone integrated modeling 
=======================================================

Overview
--------

IPS-FASTRAN is an integrated modeling platform for a wide range of flexible theory-based integrated modeling workflows for Tokamak, built upon the High Performance Computing (HPC) Integrated Plasma Simulator `IPS <https://ips-framework.readthedocs.io/en/latest>`_ framework. Typical IPS-FASTRAN workflows include:

- Iterative steady-state scenario modeling self-consistent with the state-of-art models of core transport, edge pedestal, equilibrium, stability, heating, and current drive
- Time-dependent modeling of discharge evolution from current ramp-up to termination towards a high fidelity tokamak pulse design (TokSimulator)
- Coupled Core-Edge Pedestal-SOL modeling (CESOL)
- Experiment analysis such as power balance, kinetic EFIT, and core transport analysis
- Optimization and theory-based reactor design (TokDesigner)
- Develop surrogate models for the integrated modeling components

Code repositories
-----------------

- IPS-FASTRAN: https://github.com/ORNL-Fusion/ips-fastran
- IPS-EPED: https://github.com/ORNL-Fusion/ips-eped
- IPS-SOLPS-ITER: https://github.com/ORNL-Fusion/ips-solps-iter
- TokDesigner: https://github.com/ORNL-Fusion/TokDesigner

Run environment on Perlmutter at NERSC
--------------------------------------

Conda environment

.. code-block:: bash

   > module load python
   > conda activate /global/common/software/atom/perlmutter/cesol/conda/latest

Test

.. code-block:: bash

    > python
    Python 3.8.19 | packaged by conda-forge | (default, Mar 20 2024, 12:47:35)
    [GCC 12.3.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>>import fastran
    >>>import fastran.plasmastate.plasmastate
    >>>import solps_iter
    >>>import solpsutils
    >>> 	
	
.. code-block:: bash

 	> ips.py --version
	ips.py 0.8.1

Quick start guide
-----------------

See https://ornl-fusion.github.io/tokdesigner-doc/quick_start_guide.html

References
----------

**IPS-FASTRAN** J.M. Park, et. al., "Integrated modeling of high bN steady state scenario on DIII-D", Phys. Plasmas, 25, 012506 (2018) `doi.org/10.1063/1.5013021 <https://doi.org/10.1063/1.5013021>`_

**IPS** W. R. Elwasif, et. al., "The Design and Implementation of the SWIM Integrated Plasma Simulator", Pisa, Italy, 2010, pp. 419-427, `doi: 10.1109/PDP.2010.63 <https://ieeexplore.ieee.org/document/5452438>`_.

