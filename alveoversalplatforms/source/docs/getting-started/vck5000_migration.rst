.. include:: /include/links.txt
.. include:: /include/format.txt

.. # S1 * S2 = S3 - S4


.. _VCK5000 Migration Guide:

##########################################################################
VCK5000 Migration Guide
##########################################################################

VCK5000 shells from ``vck5000_gen4x8_xdma_base_2`` forwards (see :ref:`Alveo Versal Deployment Packages Table`), use an updated formatting of the on-card OSPI configuration storage flash memory.   The older configuration flash storage format is not compatible with the ``vck5000_gen4x8_xdma_base_2`` and ``vck5000_gen4x8_qdma_base_2`` shells so it is necessary to execute a migration process to update the formatting of the OSPI flash storage to make it suitable for use with these newer platforms.

It may be necessary to setup the XRT environment to use the ``xbmgmt`` utility below. See :ref:`XRT environment setup`.

The processes described on this page should be executed for the following scenarios:

   - A brand new VCK5000 card or a VCK5000 card running an earlier platform such as ``vck5000_gen3x16_xdma_base_1`` must follow the Stage 1 instructions below.
   - A VCK5000 card running ``vck5000_gen4x8_xdma_base_2`` upgrading to ``vck5000_gen4x8_qdma_base_2`` must follow the Stage 2 instructions below.

.. attention:: 
   Migration and upgrade of the VCK5000 requires correct matching of the platform shell and satellite controller firmware versions programmed onto the card at each stage of the upgrade.  To ensure that the shell and firmware images are correctly matched during the migration and upgrade, the following sequence of upgrades should be followed:
   
   - If the VCK5000 card is currently programmed with ``vck5000_gen3x16_base_1``, complete the migration and upgrade process to configure the card with the ``vck5000_gen4x8_xdma_base_2``.
   - When the card is migrated and upgraded to ``vck5000_gen4x8_xdma_base_2`` shell,  it can then be upgraded to ``vck5000_gen4x8_qdma_base_2`` shell.
   - Direct migration and upgrade from ``vck5000_gen3x16_xdma_base_1`` to ``vck5000_gen4x8_qdma_base_2`` is not supported.

.. important::
   Migration is a one-way process. 
   
   Once the VCK5000 card is migrated with Stage 1 instructions below, it is not possible to revert the card to run pre-2022.1 platforms.

To determine whether migration is required run the following command. Note: It is assumed that Step 1 from :ref:`Alveo Versal Deployment Package Installation<Alveo Versal Deployment Package Installation Step 1>` has already been completed.

.. code:: bash

   $ xbmgmt examine --report platform --device <Management BDF>
   
To find <Management BDF> see :ref:`Obtaining Card BDF Values`.

If the output from the command above contains the following then migration is needed:

.. packages installed, latest XRT
.. code:: bash

   ------------------------------------
   1/1 [0000:d8:00.0] : xilinx_vck5000
   ------------------------------------
   Warning: Device is not ready - Limited functionality available with XRT tools.
   
   Flash properties
     Type                 : ospi_versal
     Serial Number        : N/A
   
   Device properties
     Type                 : vck5000
     Name                 : N/A
     Config Mode          : 0
     Max Power            : N/A
   
   Flashable partitions running on FPGA
     Platform             : xilinx_vck5000
     SC Version           : INACTIVE
     Platform ID          : 0x0

In particular if the ``Flash properties: Type`` is ``ospi_versal`` then migration is required.   Following a successful migration with the steps below, the ``Flash properties: Type`` value will be reported as ``ospi_xgq``.

------------------------------

Migration Steps
*************************************************************************

Stage 1:  OSPI migration and vck5000_gen4x8_xdma_base_2 shell update
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

.. note:: 
   The ``vck5000_gen4x8_xdma_base_2`` platform is aligned with the Vivado and Vitis 2022.1 software.   Ensure that 2022.1 XRT package (XRT version 2.13.466) is installed on the host and used in this stage to satisfy compatibility requirements with the 2022.1 platform packages.

Run the following command to migrate the card from ``vck5000_gen3x16_xdma_base_1`` to ``vck5000_gen4x8_xdma_base_2``.

.. code:: bash

   $ sudo /opt/xilinx/firmware/vck5000/gen4x8-xdma/base/migration/migrate.sh <Management BDF>

When complete, cold reboot the host machine as requested.

.. attention::
   Ensure the host machine is **cold** rebooted to ensure the new platform configuration is loaded.

After cold reboot completes, the Satellite Controller on the card also needs to be updated. Issue the following commands

.. code:: bash
   
   $ sudo xbmgmt program --base --device <Management BDF>
   $ timeout 40 watch -n 1 'xbmgmt examine --report vmr --device <Management BDF> --verbose | grep -i "program progress"'


The last command outputs information similar to below

.. code:: bash

   Every 1.0s: xbmgmt examine --report vmr --device d8:00.0 --verbose |...  Tue May  3 10:30:36 2022
   
     Program progress     : 100

``Program progress`` will increment from 0 to 100 to indicate SC upgrade progress. 

When the command above completes issue a **cold** reboot of the host system to complete the upgrade.

When the host machine completes the cold reboot, run the ``xbmgmt examine`` command to confirm that the Satellite Controller firmware version is now reported to be at version 4.4.33 before continuing to the next stage.

To confirm that the VCK5000 card has been successfully migrated issue the following command:

.. code:: bash

   $ sudo xbmgmt examine -r vmr --verbose --device <PCIe BDF of your VCK5000 card>

The presence of ``Has fpt: 1`` in the output of this command indicates that the card's OSPI configuration storage is successfully migrated.

Stage 2:  vck5000_gen4x8_qdma_base_2 shell update
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

.. note:: 
   The ``vck5000_gen4x8_qdma_base_2`` platform is aligned with the Vivado and Vitis 2022.2 software.   Ensure that 2022.2 XRT package (XRT version 2.14.354) is installed on the host and used in this stage to satisfy compatibility requirements with the 2022.2 platform packages.

To upgrade the card from ``vck5000_gen4x8_xdma_base_2`` to ``vck5000_gen4x8_qdma_base_2`` complete the following steps.

First, run the following command to program the platform shell on the card:

.. code:: bash

   $ sudo xbmgmt program --base shell --device <Management BDF>  --image <path to qdma install image (typically located in /lib/firmware/xilinx/...)>

When complete, cold reboot the host machine as requested.

After cold reboot completes, the Satellite Controller on the card also needs to be updated to version 4.4.35 or later.  

.. attention:: 
   For the XDMA to QDMA platform upgrade, the transfer of the new Satellite Controller firmware is completed with two commands instead of one.  

First, run the following command to set the Satellite Controller hardware into a state where it is ready to receive a new firmware:

.. code:: bash

   $ sudo xbmgmt program --base sc --device <Management BDF>  --image <path to qdma install image>


Next, re-run the same command to transfer the new firmware image to the Satellite Controller's program storage.

.. code:: bash

   $ sudo xbmgmt program --base sc --device <Management BDF>  --image <path to qdma install image>

When complete, cold reboot the host machine.

After cold reboot completes issue the ``xbmgmt examine`` command to the card and review the output to confirm that the satellite controller version is now reported as 4.4.35 (or later).

Return to :ref:`Alveo Versal Deployment Package Installation Step 3` to validate the installation.

----------------------------------

.. include:: /include/license.txt
