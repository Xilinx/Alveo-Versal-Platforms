.. include:: /include/links.txt
.. include:: /include/format.txt

.. # S1 * S2 = S3 - S4

.. _Installation Guide for Platform Development Packages:

##########################################################################
Installation Guide for Platform Development Packages
##########################################################################

The `Vitis® Unified Software Platform`_ can be used to develop applications for use with the Alveo datacenter accelerator cards. To set up an accelerator card platform for use in the Vitis development environment, follow the installation steps in `Vitis Software Platform Installation`_ (UG1393).

.. note::
    The development package installation will require a minimum version of XRT to be installed. When installing XRT using the instructions referenced above please install the specific version of XRT linked to in the table below.


.. # TODO Vitis documentation needs to be updated to link to this site in future for Versal Platforms (today it is referencing UG1120 for VUP), and it should link back to here or add a section for accelerator platform dev pkg installation.

After installing Vitis and XRT, install the desired Development Package as listed in the table below. This shall install the necessary platform files into ``/opt/xilinx/platforms`` where Vitis will automatically look for them when creating an application. 

.. csv-table:: **Table: Alveo Versal Development Packages**
    :header: "Platform", "Link", "OS Support"
    :widths: 30, 40, 30

    "vck5000_gen3x16_xdma_base_1", "`VCK5000 Gen3x16 Installation Packages`_", "see link left"
    "vck5000_gen4x8_xdma_base_2", "`VCK5000 Gen4x8 XDMA Installation Packages`_", "see link left"
    "vck5000_gen4x8_qdma_base_2", "`VCK5000 Gen4x8 QDMA Installation Packages`_", "see link left"


.. note::
    Root access is required for all software and firmware installations 

----------------------------------

Redhat and Centos
********************

To install the package on CentOS or RedHat navigate to the folder where the development RPM was downloaded and run

.. code:: bash

    $ sudo yum install ./<package_name>.rpm

----------------------------------

Ubuntu
********************

To install the package on Ubuntu navigate to the folder where the development RPM was downloaded and run

.. code:: bash

    $ sudo apt install ./<package_name>.deb

----------------------------------

When creating a new application project in Vitis, the installed platform will now be visible in the platform list. Please use the `Vitis`_ documentation for application development guidance.

If you already have an application (XCLBIN) for the target platform and wish to run this on real hardware, please see the :ref:`Installation Guide for Platform Deployment Packages` next.

----------------------------------

.. include:: /include/license.txt

