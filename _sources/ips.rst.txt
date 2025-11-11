IPS Framework
=============

About
-----

The Integrated Plasma Simulator (IPS) Framework is designed
for coupling plasma physics codes to simulate the interactions of
various heating methods on plasmas in a tokamak. The physics goal of
the project is to better understand how the heating changes the
properties of the plasma and how these heating methods can be used to
improve the stability of plasmas for fusion energy production.

The IPS framework is thus designed to couple standalone codes flexibly
and easily using python wrappers and file-based data coupling. These
activities are not inherently plasma physics related and the IPS
framework can be considered a general code coupling framework. The
framework provides services to manage:

 - the orchestration of the simulation through component invocation,
    task launch and asynchronous event notification mechanisms
 - configuration of complex simulations using familiar syntax
 - file communication mechanisms for shared and internal (to a
    component) data, as well as checkpoint and restart capabilities

The framework performs the task, configuration, file and resource
management, along with the event service, to provide these features.

The framework also allows for integration with an optional IPS Web Portal, which can be used as a collaborative analytics/visualization platform. The IPS Web Portal is usually deployed alongside a JupyterHub/JupyterLab instance.

Documentation
-------------

The latest version of the IPS Documentation can be found at `https://ips-framework.readthedocs.io/en/latest/` . This documentation is meant to capture the primary development branch of the IPS framework. A web portal instance for this branch will be supported, but you may have to install the IPS framework from the Git source instead of through PyPI.

The stable version of the IPS Documentation can be found at `https://ips-framework.readthedocs.io` , which will always reference the last release of the IPS framework on PyPI.

Documentation specific to the IPS 

IPS Portal URLs
---------------

- Stable version: `https://lb.ipsportal.production.svc.spin.nersc.org/`
- Development version: `https://lb.ipsportal.development.svc.spin.nersc.org/`

Installation
------------

From PyPI:

``pip install ipsframework``

The framework will follow semantic versioning (breaking changes only in major versions (if not 0.x.x) or minor versions (if 0.x.x)), so it is recommended you specify a version in this format:

``pip install "ipsframework>=0.8.0,<0.9.0"``

For the development version, you can easily install it from Git:

``pip install git+https://github.com/HPC-SimTools/IPS-framework.git@0.9.0-prerelase``

Source Code
-----------

- IPS Framework - `https://github.com/HPC-SimTools/IPS-framework`
- IPS Web Portal - `https://github.com/HPC-SimTools/IPS-Portal`


