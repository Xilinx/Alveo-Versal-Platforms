.. include:: /include/links.txt
.. include:: /include/format.txt

.. # S1 * S2 = S3 - S4

.. _VCK5000 Gen4x8 XDMA Vitis Platform:
.. _vck5000_gen4x8_xdma_base_2:


##########################################################################
VCK5000 Gen4x8 XDMA Vitis Platform
##########################################################################

.. # start of documentation

The Xilinx® Alveo® Versal® ACAP VCK5000 Data Center Development Kit is the industry's first heterogeneous compute platform.

For more details on the VCK5000 card please see `UG1428`_.

The VCK5000 card is supported by the Vitis® AI development environment which consists of optimized IP, tools, libraries, models, and example designs. Vitis AI has been designed with high efficiency and ease-of-use in mind, unleashing the full potential of AI acceleration on the VCK5000. Run Tensorflow, Pytorch, and Caffe models using Python or C++ APIs in minutes without any prior hardware knowledge about FPGAs or ACAPs.

Featuring the Xilinx® Versal ACAP XCVC1902 device, the VCK5000 is equipped with many of the common board-level features needed for design development, enabling the demonstration, evaluation, and development of applications, including:

- PCIe attached acceleration.
- Scalar processing of complex algorithms and decision making.
- Vector processing for machine learning, video and image processing.
- Signal processing of complex math, convolutions.

The division of Static and DFX regions for the Gen4x8 XDMA platform is shown below.


.. _Figure Platform Static and DFX Region:

.. figure:: /media/vck5000_4x8_xdma_top.png
   :scale: 100 %
   :align: center

   **Figure: XDMA Platform Static and DFX Region**

--------------------------

Static Region Components
**************************
- PCIe Gen4x8 link for host to card connectivity

- `XDMA <PG347_>`_ for application data movement between host memory and card memory

- RPU for embedded card management including DFX download (XCLBIN), platform update, sensor reporting, etc.

- APU OS loading for PS Kernel support

:ref:`Installing a deployment platform <Installation Guide for Platform Deployment Packages>` on the cards instantiates the static region including the above components.

.. important:: 
   Installation of the VCK5000 Gen4x8 XDMA deployment package requires additional steps compared to the prior VCK5000 Gen3x16 XDMA platform.  Please review and follow all guidance on :ref:`the VCK5000 migration page <VCK5000 Migration Guide>` to ensure correct installation of this deployment package on the VCK5000 card.

--------------------------

DFX Region Components
************************
- PL region for HLS/RTL kernel Instantiation

- APU User Space for PS Kernel Instantiation

- 12GB of on-card DDR-4 for user-kernel usage (RTL/HLS/PS)

- AIE Array


Please see the following references for DFX kernel development utilizing the above DFX components:

- `AIE Tutorials`_ for details on how to create a mixture of user kernels for the vck5000 platform including utilising the AIE kernel.

- `Vitis Examples`_ for simple example DFX kernels, not specific to VCK5000



--------------------------

Platform Details
************************


-  **Platform name:** xilinx-vck5000-gen4x8-xdma-base-2
-  **Supported by:** Vitis tools 2022.1, no support for other tools versions
-  **Platform UUID:** 04624343-B44B-B0A1-3CD4-8A411789FF20
-  **Interface UUID:** AE9AF7BC-A57D-DE8A-3BA8-A3F9262D78BB
-  **Release Date:** April 2022
-  **Created by:** 2022.1 tools
-  **Supported XRT versions:** 2022.1
-  **Link speed:** Up to 8 lane Gen4
-  **Target card:** VCK5000-AIE-ADK-P-G-ED

.. -  **Release Notes:** Change log and known issues for the platform are available in the *Alveo VCK5000 Release Notes Answer Record* TODO.


--------------------------

Feature Map
************************

.. csv-table:: **Table: Card Installation Guides**
   :header: "Feature", "Support"
   :widths: 30, 70


   "DFX", "1-RP"
   "DMA", "XDMA PCIe Gen4x8, 2-channel, 512bit 250MHz datapath"
   "DDR", "12GB available to DFX"
   "PLRAM", "1x128KB"
   "DFX Clocking", "2x scalable clocks. Default 300MHz & 500MHz. 1 fixed 100MHz clock"
   "AIE", "yes"
   "GT", "GT Kernel Support"
   "Host Memory", "no"
   "Peer To Peer", "no"  

For more details on each feature please see :ref:`Platform Features <arch features>`

--------------------------

Static Region Floorplan and Resource Usage
********************************************

As is shown above in `Platform Static and DFX Region <Figure Platform Static and DFX Region_>`_, the static region in the VCK5000 Gen4x8 XDMA mainly comprises the hardened PCIe interconnect, XDMA data mover, RPU processing subsystem and PL connectivity for the PL DFX region. The APU subsystem is loaded as part of the Static Region however it is available to run user applications in the form the PS Kernels.

The VCK5000 platform Static Region utilises mostly hard silicon IP and does not consume significant PL usage.

.. csv-table:: **Table: DFX Available VC1902 Resources**
   :header: "Type", "VC1902 Total", "Static Reserved", "% of Total", "DFX-Avail", "% of Total"

   "LUT",       "900K", "22K",  "2.4",  "878k",     "97.6"
   "FF",        "1.8M", "44K",  "2.4",  "1756K",    "97.6"
   "BRAM36",    "967",  "0",    "0",    "967",      "100"
   "URAM",      "463",  "0",    "0",    "463",      "100"
   "DSP",       "1968", "0",    "0",    "1968",     "100"
   "PL NMU",    "28",   "2",    "7",    "26",       "93"
   "PL NSU",    "28",   "5",    "18",   "23",       "82"
   "AIE Tiles", "400",  "0",    "0",    "400",      "100"


All AIE tiles are available for application use. Please see https://www.xilinx.com/support/documentation/selection-guides/versal-ai-core-product-selection-guide.pdf for further information on available VC1902 device resources.

--------------------------

Card Thermal and Electrical Protections
********************************************

The following consequent actions can occur when an electrical or thermal threshold is crossed.

- **Clock Shutdown**
  
  - The DFX region clocks are stopped to immediately reduce power. The static region will remain operational. A hot reset is required to recover.

- **Card Shutdown**

  - The power supplies are shutdown. Occurs was a fatal threshold is crossed to prevent damage to the card. A cold reboot will be required to recover. 

.. note:: 
   Dynamic clock throttling of the DFX region is not supported for this release.

The following electrical threshold limits are based on a 300W maximum electrical power design and assumes the 6-pin and 8-pin AUX power cables have been connected as per `UG1531`_.

.. csv-table:: **Electrical Protection Thresholds**
   :header: "Sensor Description", "DFX Clock Shutdown Threshold", "Card Shutdown Threshold"
   :widths: 40, 30, 30

   "12V PEX power", "69W for >1s", "N/A"
   "12V AUX power", "231W for >1s", "N/A"
   "VCCINT current", "N/A", "60A per internal supply"


.. note::
   VCCINT for this card is 0.78V and there are 6 internal VCCINT supplies.


.. csv-table:: **Thermal Protection Thresholds**
   :header: "Sensor Description", "Warning Threshold", "DFX Clock Shutdown Threshold", "Card Shutdown Threshold"
   :widths: 40, 20, 20, 20

   "ACAP Temperature", "88°C", "100°C", "107°C"
   "VCCINT temperature", "100°C", "110°C", "125°C"


.. # end of documentation

----------------------------------

.. include:: /include/license.txt
