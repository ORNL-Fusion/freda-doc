========================================================================
FREDA-UQ: A Multi-Fidelity UQ Framework for the Integrated Plasma Simulator (IPS)
========================================================================

.. contents:: Table of Contents

Contact
=======

For questions regarding these new capabilities,  please contact:

* **Luis Damiano** (ladamia@sandia.gov) †
* **Mike Eldred** (mseldre@sandia.gov) †
* **Cami Collins** (collinscs@ornl.gov)
* **Jin Myung Park** (parkjm@ornl.gov)
* **Chris Holland** (chholland@ucsd.edu)

† POCs for multi-fidelity uncertainty quantification, or the Dakota software.

1. Introduction
===============

1.1. What is this framework?
-------------------------------

This capability includes a set of tools for performing advanced Uncertainty
Quantification (UQ) and Sensitivity Analysis (SA) for fusion energy models
within the Integrated Plasma Simulator (IPS).

It provides a user-friendly framework that automates complex multi-fidelity
analyses by connecting the Dakota toolkit to IPS workflows. The interface with
Dakota has been abstracted so that a user can define an entire UQ study in a
JSON configuration file.

1.2. The goal: Better designs, faster
---------------------------------------

As part of the Fusion REactor Design and Assessment (FREDA) project, our goal is
to make UQ a standard part of the design process. This framework helps
physicists and engineers answer critical design questions with confidence, such
as:

* "How precise is our prediction?" What is the statistical uncertainty on a key
  metric, like divertor heat flux?
* "Which parameters matter most?" What are the most powerful "knobs" we can turn
  to optimize our design?
* "How can we save computational cost?" How can we use cheap, low-fidelity (LF)
  models to reduce the number of expensive, high-fidelity (HF) simulations we
  need to run?

1.3. How to read this guide
-----------------------------

* **To understand the "why"**: Read Section 2. Conceptual overview.
* **To run a pre-built demo**: Read Section 3. Getting started and Section 4.
  Step-by-step tutorials.
* **To create your own study**: Read Section 5. Configuration guide in detail.
* **For the theory**: Read Section 6. Technical reference for a deeper dive into
  the methods.

---

2. Conceptual overview
======================

This framework is designed to run two primary types of UQ analyses, both of
which use low-fidelity models to accelerate the study. Future versions will
include additional capabilities.

2.1. Use case 1: Multi-fidelity Monte Carlo (MFMC)
---------------------------------------------------

* **What it is:** A method for calculating statistical metrics (like the mean or
  standard error) of a simulation output. It is a Control Variate (CV)
  technique, where a cheap, low-fidelity (LF) model is used as a "control" to
  correct the statistical error from a small number of expensive, high-fidelity
  (HF) runs.
* **The payoff:** The variance of the final estimate is reduced by a factor of
  :math:`(1-\rho^2)`, where :math:`\rho` is the Pearson correlation coefficient
  between the HF and LF models. A stronger correlation gives a greater speedup.
* **Demonstration:** The **Scrape-Off Layer (SOL) Demo** (see Tutorial 1) uses
  this method to get a high-precision estimate for divertor heat flux
  (``qe_div``). It combines the C1 (LF) and SOLPS (HF) models and achieves a
  more precise result than a standard Monte Carlo run of equivalent cost.

2.2. Use case 2: Global sensitivity analysis (GSA)
---------------------------------------------------

* **What it is:** A method to determine how much of the uncertainty (variance)
  in an output is caused by the uncertainty in each input parameter. This is
  formally known as Variance-Based Decomposition (VBD).
* **The payoff:** The analysis produces **Sobol Indices** for each parameter.
  The two most important are:

  * **Main effect** (:math:`S_i`): The fraction of output variance caused by the
    input parameter :math:`x_i` *alone*.
  * **Total effect** (:math:`T_i`): The fraction of output variance caused by
    the input parameter :math:`x_i` *plus all of its interactions* with other
    parameters.

* **Demonstration:** The **Neutral Beam Injection (NBI) Demo** (see Tutorial 2)
  uses GSA to find which of 10 input parameters have the biggest impact on Net
  Electric Power (``PNet``) and Non-inductive Current Fraction (``fNI``). It
  uses NFREYA (LF) and NUBEAM (HF) to build a multi-fidelity "polynomial chaos
  expansion" (PCE) to compute these indices efficiently.

---

3. Getting started
==================

3.1. Prerequisites
--------------------

Before running a study, ensure you have:

1. Access to NERSC Perlmutter.
2. The path to the required conda environment:
    ``/global/common/software/atom/perlmutter/cesol/conda/dev``.
3. The path to the Dakota executable:
    ``/global/homes/l/ladamia/.local/bin/dakota``.
4. The workflow files for your chosen demonstration (e.g., the ``NBI_demo`` or
    ``SOL_demo`` directory).

The conda environment in (2.) loads Dakota 6.15, which does not have access to
the latest multi-fidelity capabilities. Regarding (3.), I built Dakota 6.20 in
my local directory. For now, you need to provide your own execitable for Dakota
6.20 or newer (ask me if you need help with that). In future versions, Dakota
6.20+ will be shipped in the conda env to streamline the workflow (this will be
completely transparent for the user).

3.2. Core file architecture
-----------------------------

Every study in this framework consists of three key files. In future versions,
the third file will be included in the conda env (this will be out of sight for
the final user).

1.  ``dakota_config.json`` (The "What")

    * **Purpose:** This is the *only file you edit*. It is the user-facing
      "control panel" where you define the entire study.
    * **Content:** You specify the UQ method, list your HF/LF models, define
      their costs, and list all input variables and output responses.

2.  ``run_study.sh`` (The "Executor")

    * **Purpose:** This is the Slurm batch script that runs the entire
      end-to-end workflow on Perlmutter.
    * **Action:** You edit this file to change Slurm allocation request info.
      You submit this script to the queue using ``sbatch``.

2.  ``dakota_ips_config.py`` (The "How")

    * **Purpose:** This is the "translator" script. It reads your simple
      ``dakota_config.json`` and automatically writes the complex ``dakota.in``
      file that Dakota needs.
    * **Action:** *Do not edit or execute this file.* This file is called by
      ``run_study.sh``. User can safely disregard this file.

3.3. General workflow
-----------------------

Running *any* study follows the same 4 steps:

1. **Configure:** Prepare your study directory (e.g., ``my_nbi_study/``). Ensure
    it contains the three core files (``.json``, ``.py``, ``.sh``) and any
    necessary model files (e.g., ``fidelity1_nfreya/``, ``fidelity2_nubeam/``).
2. **Edit:** Open ``dakota_config.json`` and edit the parameters, variables, or
    method as needed for your study.
3. **Submit:** From your study directory, submit the executor script to the
    Slurm scheduler:

    .. code-block:: bash

        sbatch run_study.sh

4. **Analyze:** Once the job finishes, all results will be collected in a new
    ``study/`` directory. You can then analyze the output files (e.g.,
    ``study_tabular.dat`` or ``dakota_output.out``).

---

4. Step-by-step tutorials
=========================

4.1. Tutorial 1: SOL multi-fidelity Monte Carlo (MFMC)
------------------------------------------------------

This tutorial replicates the analysis from the Q4 report, estimating divertor
heat flux using C1 and SOLPS.

1. **Copy files:** Create a new directory (e.g., ``my_sol_demo``) and copy the
    contents of the ``SOL_demo`` directory into it.

2. **Inspect configuration:** Open ``dakota_config.json``. You will see:

    * The ``method`` block specifies ``"multifidelity_sampling"``. This tells
      Dakota to run the Control Variate method.
    * It also specifies ``"pilot_samples": 16``, which matches the 16 initial HF
      samples used in the Q4 report to estimate the correlation.
    * The ``fidelities`` list defines ``LF`` (C1 model) and ``HF`` (SOLPS model)
      and their relative costs.
    * The ``responses`` block lists the output: ``qe_div``.

3.  **Run study:** Submit the job:

    .. code-block:: bash

        sbatch run_study.sh

4.  **Analyze results:**

    * When complete, check the ``study/`` directory.
    * Open ``dakota_output.out``.
    * Search for the final statistics to find the computed mean value for
      ``qe_div`` and its **standard error**. You can compare this to the
      benchmark standard error (also in the file) to see the variance reduction,
      which should match the 8% improvement reported in the Q4 report.

4.2. Tutorial 2: NBI global sensitivity analysis (GSA)
------------------------------------------------------

This tutorial replicates the NBI analysis, finding the most important parameters
for ``PNet`` and ``fNI``.

1. **Copy files:** Create a new directory (e.g., ``my_nbi_demo``) and copy the
    contents of the ``NBI_demo`` directory into it.

2. **Inspect configuration:** Open ``dakota_config.json``. You will see:

    * The ``method`` block specifies ``"multifidelity_polynomial_chaos"``.
    * Crucially, it also specifies ``"variance_based_decomp": true``, which is
      the keyword to activate the GSA and compute Sobol indices.
    * The ``fidelities`` list defines ``LF`` (NFREYA) and ``HF`` (NUBEAM).
    * The ``variables`` block defines all **10 uncertain input parameters**.
    * The ``responses`` block lists the outputs: ``h98``, ``pnet``, and ``fni``.

3. **Run study:** Submit the job:

    .. code-block:: bash

        sbatch run_study.sh

4.  **Analyze results:**

    * When complete, check the ``study/`` directory.
    * Open ``dakota_output.out``.
    * Search for **"Variance-Based Sensitivity"**. This table will show you the
      **Sobol indices** ("Main" and "Total" effects) for each of your 10 input
      variables on each of your 3 output responses.
    * This table quantitatively proves what the presentation chart showed:
      ``fni`` is dominated by ``sampling_Pinj_MW``, while ``pnet`` is dominated
      by ``sampling_q95`` and ``sampling_A``.

---

5. Configuration guide: ``dakota_config.json``
============================================

This is the most important file for customizing your own study. It is a simple
JSON object with four top-level keys.

.. code-block:: json

    {
      "study_id": "...",
      "method": { ... },
      "hierarchy": { ... },
      "fidelities": [ ... ]
    }

5.1. ``study_id``
-----------------
* **Type:** ``String``
* **Purpose:** A unique name for your study (e.g., ``"nbi_gsa_v2"``). This is
  used as the prefix for all output files.

5.2. ``method``
---------------
* **Type:** ``Object``
* **Purpose:** Defines *what* UQ analysis to run.
* **Example (for GSA):**

    .. code-block:: json

        "method": {
          "model_pointer": "'HIERARCHY'",
          "multifidelity_polynomial_chaos": {
            "allocation_control": "greedy"
          },
          "variance_based_decomp": true,
          "output": "normal"
        }

* **Example (for MFMC):**

    .. code-block:: json

        "method": {
          "model_pointer": "'HIERARCHY'",
          "multifidelity_sampling": {
              "pilot_samples": 16,
              "sample_type": "random",
              "max_function_evaluations": 32
          },
          "output": "normal"
        }

5.3. ``hierarchy``
------------------
* **Type:** ``Object``
* **Purpose:** Tells Dakota how to combine the different fidelities (e.g., as an
  ``additive`` correction).
* **Action:** You can generally leave this unchanged as a starting point. See
  `correction`_.

5.4. ``fidelities``
-------------------
* **Type:** ``List``
* **Purpose:** This is where you define your models. It's a list, where each
  item is an object representing one fidelity (one model). You must list them
  from **lowest to highest fidelity**.

Each fidelity object has 5 required keys:

1. ``id``: A short string name (e.g., ``"LF"``, ``"HF"``).

2. ``cost``: A number representing the relative computational cost (e.g.,
    ``2.5`` for LF, ``204.0`` for HF).

3. ``variables``: An object defining the uncertain inputs for *this model*.
    * This is where you list your parameters (e.g., ``"uniform_uncertain": 10``), their bounds (``"lower_bounds": [...]``, ``"upper_bounds":[...]``), and their names (``"descriptors": [...]``).
    * CRITICAL: The descriptors (names) must match what the IPS workflow expects (e.g., ``'sampling_R'``, ``'hcd_model__INHCD_PE_0'``).

4. ``interface``: An object defining *how to run* this model.
     * The ``work_directory`` ``named`` key (e.g., ``'fidelity1_nfreya_wd'``)
       tells the IPS which workflow to execute for this fidelity.

5. ``responses``: An object defining the outputs from *this model*.
    * ``"response_functions": 3`` (must match the number of descriptors).
    * ``"descriptors": ["'h98'", "'pnet'", "'fni'"]`` (must match the output
      names from the IPS workflow).

Below is an example of the ``fidelities`` block for the NBI tutorial, which
defines both a low-fidelity (``LF``) and high-fidelity (``HF``) model.

.. code-block:: json
   :linenos:

    "fidelities": [
      {
        "id": "LF",
        "cost": 2.5,
        "variables": {
          "uniform_uncertain": 10,
          "lower_bounds": [ 3.0, 2.6, 1.70, 0.50, 6.80, 5.0, 0.90, 1.6, 20.0, 0.5 ],
          "upper_bounds": [ 4.5, 3.0, 2.00, 0.60, 7.00, 8.0, 1.00, 2.2, 80.0, 0.9 ],
          "descriptors": [
            "'sampling_R'", "'sampling_A'", "'sampling_kappa'", "'sampling_delta'",
            "'sampling_B0'", "'sampling_q95'", "'sampling_fgw_ped'",
            "'sampling_nepeak'", "'sampling_Pinj_MW'", "'sampling_Einj_keV'"
          ]
        },
        "interface": {
          "fork": {
            "asynchronous": { "evaluation_concurrency": 96 },
            "work_directory": ["directory_tag", "directory_save", "named", "'fidelity1_nfreya_wd'"],
            "copy_files": "'fidelity1_nfreya/*'"
          }
        },
        "responses": {
          "response_functions": 3,
          "descriptors": ["'h98'", "'pnet'", "'fni'"]
        }
      },
      {
        "id": "HF",
        "cost": 204,
        "variables": {
          "uniform_uncertain": 10,
          "lower_bounds": [ 3.0, 2.6, 1.70, 0.50, 6.80, 5.0, 0.90, 1.6, 20.0, 0.5 ],
          "upper_bounds": [ 4.5, 3.0, 2.00, 0.60, 7.00, 8.0, 1.00, 2.2, 80.0, 0.9 ],
          "descriptors": [
            "'sampling_R'", "'sampling_A'", "'sampling_kappa'", "'sampling_delta'",
            "'sampling_B0'", "'sampling_q95'", "'sampling_fgw_ped'",
            "'sampling_nepeak'", "'sampling_Pinj_MW'", "'sampling_Einj_keV'"
          ]
        },
        "interface": {
          "fork": {
            "asynchronous": { "evaluation_concurrency": 4 },
            "work_directory": ["directory_tag", "directory_save", "named", "'fidelity2_nubeam_wd'"],
            "copy_files": "'fidelity2_nubeam/*'"
          }
        },
        "responses": {
          "response_functions": 3,
          "descriptors": ["'h98'", "'pnet'", "'fni'"]
        }
      }
    ]

---

6. Technical reference
======================

This section provides a brief overview of the Dakota methods used in the tutorials.

6.1. SOL demo: ``multifidelity_sampling`` (MFMC)
-----------------------------------------------

* **Dakota method:** `multifidelity_sampling`_
* **Core theory:** This method implements a Control Variate (CV) Monte Carlo
  simulation.
* **How it works:**
  1. A standard Monte Carlo estimator :math:`\hat{Q}_{HF}^{MC}` has a variance
  of :math:`\frac{\mathbb{V}ar(Q_{HF})}{N}`.
  2. We can define a new, better estimator using our cheap LF model
  (:math:`Q_{LF}`) as a control variate: :math:`\hat{Q}_{HF}^{CV} =
  \hat{Q}_{HF}^{MC} - \beta (\hat{Q}_{LF}^{MC} - \mathbb{E}[Q_{LF}])`.
  3. The variance of this new estimator is
  :math:`\mathbb{V}ar(\hat{Q}_{HF}^{CV}) = \mathbb{V}ar(\hat{Q}_{HF}^{MC})
  \times (1-\rho^2)`, where :math:`\rho` is the Pearson correlation
  coefficient between the HF and LF models.
  4. Your framework runs a small number of ``pilot_samples`` (defined in the
  JSON) to estimate this correlation :math:`\rho` and the model costs. It
  then solves an optimization problem to find the best number of HF and LF
  samples to run to achieve the target accuracy for the minimum cost.

6.2. NBI demo: ``multifidelity_polynomial_chaos`` (GSA)
------------------------------------------------------

* **Dakota method:** `multifidelity_polynomial_chaos`_
* **Core theory:** This method builds a surrogate model called a Polynomial
  Chaos Expansion (PCE) to perform Global Sensitivity Analysis (GSA).
* **How it works:**

  1. The method models the high-fidelity response :math:`R` as a combination
  of the low-fidelity model plus a *discrepancy* function: :math:`R = (\sum
  \alpha^{LF}_i \Psi_i) + (\sum \delta_i \Psi_i)`.

  2. It runs samples at both LF and HF model fidelities to solve for the
  coefficients (:math:`\alpha` and :math:`\delta`) of this combined
  expansion.

  3. By setting ``"variance_based_decomp": true`` in the JSON, you instruct
  Dakota to use this final PCE to perform Variance-Based Decomposition
  (VBD).

  4. VBD analytically computes the **Sobol Indices** (:math:`S_i` and
  :math:`T_i`) from the PCE coefficients, which is far cheaper than running a
  full Monte Carlo study to get them.

     * **Main effect** (:math:`S_i`): Fraction of variance from :math:`x_i`
     alone.

     * **Total effect** (:math:`T_i`): Fraction of variance from :math:`x_i`
     *and* all its interactions.

---

7. Advanced topics & troubleshooting
====================================

7.1. Analyzing output files
---------------------------

* ``dakota_output.out``: The main log file from Dakota. Contains the final
  summary tables for GSA (Sobol indices) and MFMC (statistical moments).
* ``dakota_error.err``: Dakota-level errors.
* ``study_tabular.dat``: A data file with one row for every simulation run. It
  shows the input values for each parameter and the resulting output for each
  response. Very useful for plotting.
* ``dakota_stdout.log`` / ``dakota_stderr.log``: Standard output from the Dakota
  process itself. Good for checking progress.
* ``*_wd/`` directories: Each simulation run creates a work directory (e.g.,
  ``HF.1.1.work_dir_0/``). If a run fails, you can check the IPS and model logs
  inside this directory to debug the physics model itself.

7.2. Common errors
--------------------

* "Descriptor Mismatch": The names in your ``variables`` or ``responses`` blocks
  in the JSON file do not *exactly* match the names expected by the IPS
  workflow.
* "Python Script Fails": You have a syntax error in your ``dakota_config.json``
  file (like a missing comma or bracket).
* "Slurm Job Fails Immediately": You have a typo in a file path in
  ``run_study.sh`` (e.g., the conda environment path is wrong).
* "Model Runs Fail": The physics simulation itself is failing. Check the logs
  inside the individual ``*_wd/`` directories.

.. _multifidelity_sampling: https://snl-dakota.github.io/docs/latest_release/users/usingdakota/reference/method-multifidelity_sampling.html#multifidelity-sampling
.. _multifidelity_polynomial_chaos: https://snl-dakota.github.io/docs/latest_release/users/usingdakota/reference/method-multifidelity_polynomial_chaos.html#multifidelity-polynomial-chaos
.. _correction: https://snl-dakota.github.io/docs/latest_release/users/usingdakota/reference/model-surrogate-global-correction.html#correction
