ARC/HTC Operating System Migration
==================================


Introduction
------------

As part of our ongoing improvements to the ARC service, we have upgraded the cluster OS from CentOS 8.1 to AlmaLinux 9.4.

All nodes in the cluster have features that allow to specify the operating system during job submission:

Current Legacy OS:
    - os:redhat8
    - os_version:8.1
    - os_flavour:CentOS

Current OS:
    - os:redhat9
    - os_version:9.4
    - os_flavour:AlmaLinux

All available cluster nodes have now been upgraded. Login nodes and graphical login nodes (NoMachine NX) will be updated in the near future. 

The legacy nodes have been migrated from CentOS 7.7 to CentOS 8.1.

Alma9 is the default OS in the queueing system and you will need to explicitly specify if you want to use a node with the older version by adding the following line in your submission script:

.. code-block:: bash

   #SBATCH --contraint="os:redhat8"

This applies even if using the 'legacy' partition.

Who is responsible for ensuring my workflow works on the new OS?
----------------------------------------------------------------

Each user is responsible for testing their workflow on the new operating system. The ARC team will be working on bringing as many modules as possible to the new OS, however, some may not be supported on the new platform. When this is the case, ensure you can port your workflow to the latest version of the package as possible. Please reach out with any questions or concerns.
