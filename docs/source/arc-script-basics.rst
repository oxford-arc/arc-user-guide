
Submission Script Basics
------------------------


**What is a submission script**

A ``submission script`` describes the resources you need the resource manager (SLURM) to allocate to your job. It also contains the commands needed to execute the application(s) you wish to run, including any set-up the application(s) may require.

The script needs to be created using a Linux text editor such as ``nano`` or ``vi`` - we recommend creating and editing submission scripts on the cluster rather than editing them on a Windows machine, as this can cause problems.

**Simple submission script example**

In this example we are going to create a submission script to run a test application on the cluster. 

.. note::
   If you are not familiar with typing commands into a Linux shell (or terminal window) we suggest you follow an online Linux tutorial
   such as the Linux tutorial at `Ryans Tutorials <https://ryanstutorials.net/linuxtutorial/>`_

To begin with we change directory to your ``$DATA`` area and make a new directory named ``example`` to work in::

  cd $DATA
  mkdir example
  cd example
  
The next step is to create the submission script to describe your job to SLURM. 

First we use the ``nano`` editor to create the file::

  nano submit.sh

This command will start the Linux ``nano`` editor. You can use ``nano`` to add the following lines::

  #! /bin/bash
  #SBATCH --nodes=2
  #SBATCH --ntasks-per-node=4
  #SBATCH --time=00:10:00
  #SBATCH --partition=devel
  
  module load mpitest
  
  mpirun mpihello

.. note::
  Ensure the ``#! /bin/bash`` line is the first line of the script and the ``#SBATCH`` lines start at the beginning of the line.

To exit ``nano`` hold ``CTRL-X`` then answer ``Y`` to the question ``Save Modified buffer?`` then hit ``enter`` when asked ``File Name to Write: submit.sh``

**The anatomy of the script**

The first line ``#! /bin/bash`` tells Linux that this file is a script which can be run by the BASH shell interpreter. 

The following ``#SBATCH`` lines request specific cluster resources: 

``--nodes=2`` requests two ARC nodes

``--ntasks-per-node=4`` requests 4 cores per node (a total of 8)

``--time=00:10:00`` requests a run time of 10 minutes (the maximum for the ``devel`` partition)

``--partition=devel`` requests that this job runs on the ``devel`` partition, which is reserved for testing

``module load mpitest`` The **module load** command is used to make an application environment available to use in your job, in this case the **mpitest** application.

``mpirun mpihello`` This line runs the **mpihello** command using the special **mpirun** wrapper. 

.. note::
   MPI is required for multi-process operation across nodes, and may not be appropriate for all applications. 

**Submitting the job**

Now that you have a submission script, you can submit it to the SLURM resource manager. To do this type the following at the command line::

  sbatch submit.sh
  
SLURM will respond with::

  sbatch: CPU resource required, checking settings/requirements...
  Submitted batch job nnnnnnn
  
Where ``nnnnnnn`` is your job ID number.

This job should run very quickly, but you may be able to find it in the job queue by typing::

   squeue -u $USER
 
If it is running, you will see something like::

     JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   nnnnnnn     devel submit.s ouit0554  R       0:07      2 arc-c[302-303]
 
If the job is waiting to run (because another user is using the ``devel`` nodes) you will see::

     JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   nnnnnnn     devel submit.s ouit0554 PD       0:00      2 (None)
 
The difference being that in the first case you can see the job state is ``R`` for **RUNNING** and in the second it is ``PD`` for **PENDING** and it has not been allocated nodes in the ``NODELIST``


**Job output**

When your job completes, i.e. it is no longer showing in the job queue, you should find the SLURM output in a file named ``slurm-nnnnnnn.out`` where ``nnnnnnn`` is the
job ID of your completed job.

To view this output you can use the Linux ``cat`` command, so if our job ID was 2227191, we would use the command::

    cat slurm-2227191.out
    
This would give the output::

    Hello world from processor arc-c302, rank 0 out of 8 processors
    Hello world from processor arc-c302, rank 1 out of 8 processors
    Hello world from processor arc-c302, rank 2 out of 8 processors
    Hello world from processor arc-c302, rank 3 out of 8 processors
    Hello world from processor arc-c303, rank 4 out of 8 processors
    Hello world from processor arc-c303, rank 5 out of 8 processors
    Hello world from processor arc-c303, rank 6 out of 8 processors
    Hello world from processor arc-c303, rank 7 out of 8 processors
    
The above being the output from running the ``mpihello`` application on the 8 CPUs that we requested, and you can see it ran with 4 processes on ``arc-c302`` and 4 on ``arc-c303``



  
