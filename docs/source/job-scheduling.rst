Job Scheduling and Resources
============================

Cluster Node Types
------------------ 

Login/submit nodes
^^^^^^^^^^^^^^^^^^

These are only to be used for accessing the cluster and submitting jobs. Login nodes should not be used for software build or compute tasks. They are not designed for building software or running analysis; they do not have the same CPU architecture as the cluster nodes, and they do not run the same operating system as the cluster nodes. Please use the interactive nodes for any software builds (see below).

We have an explicit policy that user processes can only use a maximum of 1 hour of CPU time on login nodes.

Interactive nodes
^^^^^^^^^^^^^^^^^

These nodes should be used for pre/post processing of data and for building software to be used on the ARC clusters. See the section on interactive jobs for more information.

Compute nodes
^^^^^^^^^^^^^

Typical **arc** compute nodes have 48 cores. The **htc** compute nodes typically have 16, 20 or 48 cores.

The Job Scheduler
-----------------

The **arc**/**htc** clusters use SLURM as their resource manager (or scheduler). This is the same system used for the ARC's previous clusters (ARCUS-B and ARCUS-HTC) so existing ARC users will be familiar with its commands and submission script syntax.
 
As a reminder, to work with ARC's clusters, you will need to submit a job to the job scheduler; the login nodes are for preparing and submitting scheduler jobs and should not be used for performing computational work. If you need to run interactive computational work such as pre/post processing data or building your own code - this must be performed on interactive nodes.

Nodes on **arc**/**htc** are not allocated exclusively to jobs; jobs are allocated the requested number of cores and may share nodes with other jobs. The default number of cores allocated is 1 (as is the default number of nodes). Default amount memory per CPU is 8000MB. You will not be able to use resources you have not requested in your job submission; this includes memory and CPU cores.

Thus if you need more than 1 CPU core, you will need to explicitly ask for them. At its simplest this can be specified by requesting a specific number of tasks, e.g.::

    #SBATCH --ntasks-per-node=8

to request 8 tasks.

For MPI job submissions this would normally be changed to asking for a number of nodes and specifying the number of tasks per node, e.g.::

    #SBATCH --nodes=2
    #SBATCH --ntasks-per-node=48
    
to request two nodes with 48 tasks each.

For a hybrid MPI/OpenMP job, where an MPI tasks spawns multiple CPU threads, the specification needs to also specify how many CPUs per task the job will need. For example, to request 2 nodes with two MPI tasks per node that each start 24 compute threads, you need to request::

    #SBATCH --nodes=2
    #SBATCH --ntasks-per-node=2
    #SBATCH --cpus-per-task=24

The default number of CPUs per task is 1.

It is possible to request exclusive access to a node by adding ``--exclusive`` to your ``sbatch`` command or the following line to your submit script::

    #SBATCH --exclusive
    
However, we strongly advise being specific about required resources rather than using exclusive node access; homogenity of resources (or CPU features) can not be assumed. This is especially true on the HPC system, or when submitting to multiple clusters (see section Job Scheduling).

This is a very short overview; SLURM offers various ways to specify resource requirements; please see ``man sbatch`` for details.


Partitions
----------

Both clusters have the following time-based scheduling partitions available:

- short (default run time 1hr, maximum run time 12hrs)
- medium (default run time 12hrs, maximum run time 48hrs)
- long (default run time 24hrs, no run time limit)
- devel (maximum run time 10 minutes - for batch job testing only) 
- interactive (maximum run time 24hrs, can oversubscribe, for pre/post-processing and building software)

Jobs in the **short** and **medium** partitions are scheduled with higher priority than those in the **long** partition; however, they will not be able to run for longer than the time allowed on those partitions.

On the previous ARC clusters (ARCUS-B & ARCUS-HTC), users who wanted to submit long running jobs needed to submit the jobs to the scheduler specifying an acceptable timelimit, then once the job had started running, would request that the job's walltime be extended. One the new **arc**/**htc** clusters, this is no longer required; users can now submit jobs with longer time limits to the **long** partition. 

.. note::

    The default time limit on long partition is 1 day; users must specify a time limit if a longer runtime is required.

    We will no longer extend jobs.

The **htc** cluster has an additional partition available named legacy. This partition contains a number of nodes which have CentOS 7.7 installed in order to maintain compatibility with some legacy commercial applications. Access to the legacy partition is restricted to users with a requirement to use legacy software and will be enabled by the ARC team for specific users when it has been demonstrated that using a more recent version of the software application is not possible.

Cluster selection
^^^^^^^^^^^^^^^^^

By default jobs will be scheduled based upon the login node you using - if you are logged into **arc-login** jobs you submit will be queued to the **arc** cluster. If you are logged into **htc-login** jobs will be queued to the **htc** cluster.

However, The clusters are accessible from either login nodes and can be specified by passing ``--clusters=arc`` or ``--clusters=htc`` SLURM options.  
Additionally, ``squeue`` can report the status of jobs on either cluster (or both using the option ``--clusters=all``). 

It is possible for  jobs to target either cluster or both clusters using the --cluster specification in job scripts, for example::

    #SBATCH --clusters=arc

or::

    #SBATCH --clusters=htc
or::

    #SBATCH --clusters=all
    
If submitted with ``--cluster=all`` a job will simply be run on the first available resource, regardless of what cluster this is on.

Submission Scripts
------------------

As an example - to request two compute nodes, running 48 processes per node (using MPI), with one CPU per task (the default) requiring 2GB of memory per CPU, and a two hour wall time, the following submission script could be used::

    #!/bin/bash 
    
    #SBATCH --nodes=2 
    #SBATCH --ntasks-per-node=48
    #SBATCH --mem-per-cpu=2G
    #SBATCH --time=02:00:00 
    #SBATCH --job-name=myjob 
    #SBATCH --partition=short 

    module load mpitest/1.0

    mpirun mpihello

 

To request a single core for 10 minutes, with one task on the node (and one CPU per task), requiring 8GB memory, a typical submission script would be::

    #!/bin/bash
    
    #SBATCH --time=00:10:00
    #SBATCH --job-name=single_core
    #SBATCH --ntasks-per-node=1
    #SBATCH --mem-per-cpu=8G
    #SBATCH --partition=short

    module purge
    module load testapp/1.0

    #Calculate number of primes from 2 to 10000
    prime 2 10000

Interactive Jobs
----------------

An interactive job gives you a login session on a compute node and gives you a shell. This allows users to interact with the node in real time, much like one would interact with a desktop PC, or the login nodes. We now expect users to use interactive jobs in order to run pre/post processing and software build activities - and there are nodes dedicated to these tasks.

To start an interactive session, you need to use the srun command, for example::

    srun -p interactive --pty /bin/bash
    
or for a session that allows graphical interfaces (via X forwarding)::

    srun -p interactive --x11 --pty /bin/bash

This would allocate 1 core on one interactive node and log you in to the system (giving you a shell on the system). Multiple cores, memory, or other resources can be requested the same way as for sbatch.

Exiting the shell ends the job. It will also be aborted once it exceeds the time limit.

GPU Resources
-------------

GPUs are only available on compute nodes which are part of the **htc** cluster. These resources are requested using the gres SLURM directive in your submission script.

The most basic way you can access a GPU is by requesting a GPU device using the gres option in your submission script::

    #SBATCH --gres=gpu:1

The above will request 1 single GPU device (of any type) - this is the same as the method previously used on ARCUS-B/HTC. Note that - as with CPUs and memory - you will only be able to see the number of GPUs you requested.

You may also request a specific type of GPU device, for example::

   #SBATCH --gres=gpu:v100:1

To request one V100 device, or::

   #SBATCH --gres=gpu:rtx8000:2

To request two RTX8000 devices. Available devices are P100, V100, RTX (Titan RTX), RTX8000, and A100.

Alternatively you can request a GPU (--gres=gpu:1) and specify the type via a constraint on the GPU SKU, GPU generation, or GPU compute capability. Each of the following are valid forms of constraint::

   #SBATCH --gres=gpu:1 --constraint='gpu_sku:V100'

   #SBATCH --gres=gpu:1 --constraint='gpu_gen:Pascal'

   #SBATCH --gres=gpu:1 --constraint='gpu_cc:3.7'

   #SBATCH --gres=gpu:1 --constraint='gpu_mem:32GB'

   #SBATCH --gres=gpu:1 --constraint='nvlink:2.0'
    

List of configured GPU related constraints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------+----------------------------------------------------------------------------------------------------+
| gpu_gen: | GPU generation (Pascal, Volta, Turing, Ampere)                                                     |
+----------+----------------------------------------------------------------------------------------------------+
| gpu_sku: | GPU model (P100, V100, RTX, RTX8000, A100)                                                         |
+----------+----------------------------------------------------------------------------------------------------+
| gpu_cc:  | CUDA compute capability                                                                            |
+----------+----------------------------------------------------------------------------------------------------+
| gpu_mem: | GPU memory                                                                                         |
+----------+----------------------------------------------------------------------------------------------------+
| nvlink:  | device has nvlink - contraint exist as simple (-C nvlink) and specifying version (-C 'nvlink:2.0') |
+----------+----------------------------------------------------------------------------------------------------+    
    
.. note::    

    Please note that co-investment GPU nodes are limited to short partition, i.e. the maximum job run time is 12 hours. No such restrictions apply to ARC owned GPUs.

For details on available options/combinations, and ownership information, see the table of available GPUs.    
    
    
