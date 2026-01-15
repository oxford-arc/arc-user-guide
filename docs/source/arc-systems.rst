ARC Systems
===========

At the centre of the ARC service are two high performance compute clusters - **arc** and **htc**.

- **arc** is designed for multi-node parallel computation
- **htc** is designed for high-thoughput operation (lower core count jobs).

**htc** is also a more heterogeneous system offering different types of resources, such as GPGPU computing and high memory systems; nodes on **arc** are uniform. Users get access to both both clusters automatically as part of the process of obtaining an account with ARC, and can use either or both.

For more detailled information on the hardware specifications of these clusters, see the tables below:

.. table::
        :widths: 5, 25, 10, 25, 15, 15

        +---------+--------------------------------------------------------------------------------+------------+-----------------------------------------------------------------------------------------+------------------+---------------------------------------------------------------------------+
        | Cluster | Description                                                                    | Login Node | Compute Nodes                                                                           | Minimum Job Size | Notes:                                                                    |
        +=========+================================================================================+============+=========================================================================================+==================+===========================================================================+
        | arc     | | Our largest compute cluster.                                                 |            | | CPU: 48 core Cascade Lake (Intel Xeon Platinum 8268 CPU @ 2.90GHz)                    |                  | Non-blocking island size is 2212 cores                                    |
        |         | | Optimised for large parallel jobs spanning multiple nodes.                   | arc-login  | | Memory: 384 GB                                                                        | 1 core           |                                                                           |
        |         | | Scheduler prefers large jobs.                                                |            | | or                                                                                    |                  |                                                                           |
        |         | | Offers low-latency interconnect (Mellanox HDR 100).                          |            | | CPU: 288 core Zen 5c/Turin (AMD EPYC 9825 @ 2.2GHz)                                   |                  |                                                                           |
        |         |                                                                                |            | | Memory: 2.3 TB                                                                        |                  |                                                                           |   
        +---------+--------------------------------------------------------------------------------+------------+-----------------------------------------------------------------------------------------+------------------+---------------------------------------------------------------------------+
        | htc     | | Optimised for single core jobs, and SMP jobs up to one node in size.         |            | | CPUs: mix of Broadwell, Haswell, Cacade Lake, Sapphire/Emerald Rapids, AMD Genoa/Rome |                  | Jobs will only be scheduled onto a GPU node if requesting a GPU resource. |
        |         | | Scheduler prefers small jobs.                                                | htc-login  | | GPU: P100, V100, A100, RTX, H100, L40s                                                | 1 core           |                                                                           |
        |         | | Also catering for jobs requiring resources other than CPU cores (e.g. GPUs). |            |                                                                                         |                  |                                                                           |
        +---------+--------------------------------------------------------------------------------+------------+-----------------------------------------------------------------------------------------+------------------+---------------------------------------------------------------------------+

Operating system
----------------


The ARC systems use the Linux Operating System (specifically AlmaLinux 9) which is commonly used in HPC. We do not have any HPC systems running Windows (or MacOS). If you are unfamiliar with using Linux, please consider:

- Finding introduction to Linux resources online (through Google/Bing/Yahoo etc).
- Working through our brief Introduction to Linux course.
- Attending our Introduction to ARC training course (this does not teach you how to use Linux but the examples will help you gain a greater understanding).

Capability cluster (arc)
------------------------

The capability system - cluster name arc - has a total of 262× 48 core and 10× 288 core worker nodes, some of which are co-investment hardware. These machines are available for general use, but may be subject to job time limits and/or may occasionally be reserved for exclusive use of the entity that purchased them.

The ARC system offers a total of 15,456 CPU cores.

ARC nodes consist of 2 difference type of node:

- 262 worker nodes with:

  - 2x Intel Platinum 8628 CPU. The Platinum 8628 is a 24 core 2.90 GHz Cascade Lake CPU. Thus all nodes have 48 CPU cores per node.
  - 384 GB memory
  - HDR 100 infiniband interconnect. The fabric has a 4:1 blocking factor with non-blocking islands of 44 nodes (2112 cores).

- 10 worker nodes with:

  - 2x AMD EPYC 9825 (Zen5c/Turin) CPU (144 cores @ 2.20 GHz per CPU)
  - 2.3 TB of DDR5 ECC Registered memory (equivalent to 8GB per core)
  - NDR 400 Infiniband interconnect

The cluster runs AlmaLinux 9.4 OS and is scheduled by SLURM.

Login node for the system is 'arc-login.arc.ox.ac.uk', which allows logins from the University network range (including VPN).

The generally available partitions are:

.. table::
        :widths: 20 20 20 20 20

        +-------------+---------------+------------------+------------------+------------------+
        | Partition   | Nodes / cores | Nodes            | Default run time | Maximum run time |
        +=============+===============+==================+==================+==================+
        | short       | 267 / 15,216  | | arc-c[046-293] | 1 hour           | 12 hours         |
        |             |               | | arc-c[299-301] |                  |                  |
        |             |               | | arc-c[306-311] |                  |                  |
        |             |               | | arc-c[312-321] |                  |                  |
        +-------------+---------------+------------------+------------------+------------------+
        | medium      | 261 / 14,928  | | arc-c[046-293] | 12 hours         | 2 days           |
        |             |               | | arc-c[299-301] |                  |                  |
        |             |               | | arc-c[312-321] |                  |                  |
        +-------------+---------------+------------------+------------------+------------------+
        | long        | 261 / 14,928  | | arc-c[046-287] | 1 day            | unlimited        |
        |             |               | | arc-c[299-301] |                  |                  |
        |             |               | | arc-c[312-321] |                  |                  |
        +-------------+---------------+------------------+------------------+------------------+
        | legacy      | 4 / 192       | arc-c[302-305]   |                  | 10 minutes       |
        +-------------+---------------+------------------+------------------+------------------+
        | devel       | 2 / 96        | arc-c[294-295]   |                  | 10 minutes       |
        +-------------+---------------+------------------+------------------+------------------+
        | interactive | 3 / 144       | arc-c[296-298]   | 1 hour           | 4 hours          |
        +-------------+---------------+------------------+------------------+------------------+

Throughput cluster (htc)
------------------------

The throughput system - cluster name htc - currently 124 worker nodes, some of which are co-investment hardware. These machines are available for general use, but may be subject to job time limits and/or may occasionally be reserved for exclusive use of the entity that purchased them. The hardware on the HTC system is more heterogeneous than on the ARC system.

51 of the nodes are GPGPU nodes. More information on how to access GPU nodes is available.

OS is AlmaLinux 9.4. Scheduler is SLURM.

Login node for the system is ``htc-login.arc.ox.ac.uk``, which allows logins from the University network range (including VPN).

Details on the partitions are:

.. table::
        :widths: 10 20 30 18 22

        +-------------+-------------------+------------------------------------------+------------------+------------------+
        | Partition   | Nodes / cores,    | Nodes                                    | Default run time | Maximum run time |
        |             | GPUs              |                                          |                  |                  |
        +=============+===================+==========================================+==================+==================+
        | short       | | 124 / 7,872     | | htc-c[005-046,048-073]                 | 1 hour           | 12 hours         |
        |             | | - 76x V100      | | htc-g[009-018]                         |                  |                  |
        |             | | - 16x A100      | | htc-g[032-035,037-38]                  |                  |                  |
        |             | | - 24x RTX8000   | | htc-g[041-043,050-055]                 |                  |                  |
        |             | | - 12x RTXA6000  | | htc-g[058-060]                         |                  |                  |
        |             | | - 20x P100      | | htc-g[061-084]                         |                  |                  |
        |             | | - 52x Titan RTX |                                          |                  |                  |
        |             | | - 92x L40s      |                                          |                  |                  |
        +-------------+-------------------+------------------------------------------+------------------+------------------+
        | medium      | | 101 / 6,888     | | htc-c[006-046,048-073]                 | 12 hours         | 2 days           |
        |             | | - 48x V100      | | htc-g[009-018]                         |                  |                  |
        |             | | - 16x A100      | | htc-g[061-084]                         |                  |                  |
        |             | | - 24x RTX8000   |                                          |                  |                  |
        |             | | - 92x L40s      |                                          |                  |                  |
        +-------------+-------------------+------------------------------------------+------------------+------------------+
        | long        | | 101 / 6,888     | | htc-c[006-046,048-073]                 | 1 day            | unlimited        |
        |             | | - 48x V100      | | htc-g[009-018]                         |                  |                  |
        |             | | - 16x A100      | | htc-g[061-084]                         |                  |                  |
        |             | | - 24x RTX8000   |                                          |                  |                  |
        |             | | - 92x L40s      |                                          |                  |                  |
        +-------------+-------------------+------------------------------------------+------------------+------------------+
        | devel       | | 2 / 80          | htc-g[046-047]                           |                  | 10 minutes       |
        |             | | - 16x V100      |                                          |                  |                  |
        +-------------+-------------------+------------------------------------------+------------------+------------------+
        | interactive | | 2 / 80          | htc-g[048-049]                           | 1 hour           | 4 hours          |
        |             | | - 16x V100      |                                          |                  |                  |
        +-------------+-------------------+------------------------------------------+------------------+------------------+

Node CPU details are:

.. table::
        :widths: 15 35 20 20 10

        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | Nodes          | CPU                                           | Cores per node | memory per node | interconnect |
        +================+===============================================+================+=================+==============+
        | htc-c[005-006] | Intel Platinum 8628 (Cascade Lake), 2.90GHz   | 96             | 3TB             | HDR100       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-c[007-046] | Intel Platinum 8628 (Cascade Lake), 2.90GHz   | 48             | 384GB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-c[048-049] | AMD EPYC 9634 (Genoa), 2.25GHz                | 168            | 2.3TB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-c[050-055] | AMD EPYC 9634 (Genoa), 2.25GHz                | 168            | 1.5TB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-c[056-075] | AMD EPYC 9634 (Genoa), 2.25GHz                | 84             | 1.1TB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[009-018] | Intel Platinum 8628 (Cascade Lake), 2.90GHz   | 48             | 384GB           | HDR100       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g019       | AMD Epyc 7452 (Rome), 2.35GHz                 | 64             | 1TB             |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[032-040] | Intel Gold 5120 (Skylake), 2.20GHz            | 28             | 384GB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[041-043] | Intel Silver 4112 (Skylake), 2.60GHz          | 8              | 192GB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[045-049] | Intel E5-2698 v4 (Broadwell), 2.20GHz         | 40             | 512GB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[050-052] | Intel Silver 4208 (Cascade Lake), 2.10GHz     | 16             | 128GB           | HDR100       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[053-055] | Intel Gold 6342 (Ice Lake), 2.80GHz           | 16             | 500GB           | HDR100       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g056       | Intel Gold 6342 (Ice Lake), 2.80GHz           | 48             | 512GB           | HDR100       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g057       | NVidia Grace Hopper AArch64 3.5GHz            | 72             | 580GB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g058       | Intel Gold 5418Y (Sapphire Rapids), 2.0GHz    | 48             | 1.5TB           |              |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[059-060] | Intel Platinum 8468 (Sapphire Rapids), 2.1GHz | 96             | 1TB             | HDR100       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+
        | htc-g[061-084] | Intel Gold 6548N (Emerald Rapids), 2.8GHz     | 64             | 500GB           | NDR400       |
        +----------------+-----------------------------------------------+----------------+-----------------+--------------+

GPU Resources
-------------

ARC has a number of GPU nodes in the "htc" cluster.

Node GPU details are:

.. table::
        :widths: 15 10 10 15 10 10 20 10

        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | Nodes          | GPUs      | #GPUs | GPU memory | ECC | CUDA cores | CUDA compute capability | nvlink   |
        +================+===========+=======+============+=====+============+=========================+==========+
        | htc-g[009-014] | RTX8000   | 4     | 40GB       | yes | 4608       | 7.5                     | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[015-019] | A100      | 4     | 40GB       | yes | 6912       | 8.0                     | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[032-034] | P100      | 4     | 16GB       | yes | 3584       | 6.0                     | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[035]     | V100      | 4     | 16GB       | yes | 5120       | 7.0                     | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[037-038] | V100      | 4     | 32GB       | yes | 5120       | 7.0                     | yes      |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[041-043] | Titan RTX | 4     | 24GB       | yes | 4606       | 7.5                     | pairwise |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[045-049] | V100-LS   | 8     | 32GB       | yes | 5120       | 7.0                     | yes      |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[050-052] | RTXA6000  | 4     | 48GB       | yes | 10,752     | 8.6                     | yes      |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[053-055] | H100      | 4     | 82GB       | yes | 10,752     | 12.6                    | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g056       | MI210     | 4     | 64GB       | yes |            |                         |          |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g057       | GH200     | 1     | 96GB       | yes | 10,752     | 12.6                    | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g058       | H100      | 4     | 96GB       | yes | 10,752     | 12.6                    | yes      |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[059-060] | H100      | 8     | 80GB       | yes | 10,752     | 12.6                    | yes      |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+
        | htc-g[061-084] | L40S      | 4     | 46GB       | yes | 18,176     | 12.9                    | no       |
        +----------------+-----------+-------+------------+-----+------------+-------------------------+----------+

Memory
------

On the HTC cluster, there are several generally available high memory nodes:

- 2 nodes with 96 CPU cores & 3 TB memory
- 4 nodes with 168 CPU cores & 2.2 TB memory
- 4 nodes with 168 CPU cores & 1.5 TB memory
- 18 nodes with 84 CPU cores & 1.1 TB memory

You can use the high-memory nodes by adding a value between ``400G`` and ``3000G`` in the ``--mem`` option in your submission script, e.g.:

.. code-block:: bash

        #SBATCH --mem=1500G

to request 1.5 TB

Storage
-------

Our clusters systems share 2 PB of high-performance OnTAP filesystem for project data storage, as well as 1 PB of ultra high performance/low latency Weka filesystem for shared scratch storage.

Project data storage is mounted via NFS on all nodes. On nodes with NDR/HDR interconnect, the scratch filesystem uses that fabric instead.

For more information about the storage, please refer to the `ARC Storage <arc-storage.html>`__ page.

Software
--------

Users may find the application they are interested in running is already been installed on at least one of the systems. Users are welcome to request the installation of new applications and libraries or updates to already installed applications via our software request form.

For more information, please refer to our `ARC Software Guide <https://arc-software-guide.readthedocs.io>`__.
