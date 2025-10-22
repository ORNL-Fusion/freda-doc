Multi-fidelity Global Sensitivity Analysis for Neutral Beam Injection
==========

We investigate how plasma density and heating power influence key performance
metrics, including integrated electron heating, ion heating, and driven current.
This approach allows us to efficiently navigate the design space and evaluate
the effects of various parameters on reactor performance, ultimately
contributing to more robust and reliable fusion reactor designs.

Neutral-beam injection
----------------------

* **Fidelities**
  * NFREYA (low fidelity, ``fidelity1_nfreya``, approx. 2.5 min per run, 1 core)
  * NUBEAM (high fidelity, ``fidelity2_nubeam``, approx. 8.5 min per run, 24 cores)
* **Physics parameters**
  * Plasma density (``fastran_init__NE_AXIS``)
  * Heating power (``nfreya__BPTOR_0`` or ``nubeam__PINJA_0``)
* **Quantities of interest**
  * Integrated electron heating (``pe_nb``)
  * Ion heating (``pi_nb``)
  * Driven current (``inb``)
* **Files**
  * Same files: ``collect``, ``makedb``, ``submitjob``, most ``input/in*`` files
  * Different files:
    * ``fastran_scenario`` (``NB IMPLEMENTATION = nfreya`` vs. ``NB IMPLEMENTATION = nubeam``)
    * ``dakota_ips.in`` (different variable name for heating power)
    * ``dakota_ips_driver`` (``$MACHINE_CONFIG`` vs. ``$MACHINE_CONFIG_SERIAL``)
    * ``input/inn*`` (``input/innfreya`` vs. ``input/innubeam``)

Running an Example
~~~~~~~~~~~~~~~~~~

Running an example involves a single call:

``ips_multifidelity_uq.py --config=sensitivity_nbi.json​``

Generated Artifacts
~~~~~~~~~~~~~~~~~~~

Running an example generates the following artifacts:

* ``dakota_ips.out``, ``dakota_ips.dat``, ``dakota.rst``
* ``SCAN`` folder with the simulation artifacts
* ``SUMMARY`` folder with the collected outputs
* ``params.in.${sim_id}`` with the physics parameters for ``sim_id``
* ``results.out.${sim_id}`` with the quantities of interest for ``sim_id``
