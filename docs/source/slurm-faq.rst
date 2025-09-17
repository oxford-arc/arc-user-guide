SLURM FAQs
==========


How do I submit, check the status, and/or delete a batch job?
-------------------------------------------------------------

Use the SLURM commands : ``sbatch``, ``squeue`` , ``scancel``

With a submission script called **submit.sh**, to submit this batch script, use the ``sbatch`` command::

  sbatch submit.sh

You can use the ``--test-only`` flag to sbatch to validate a submit script, and to get an idea of when the job will run if submitted now.

To check the status of a batch job use the command ``squeue``. If you know the job ID, ie. 12345 then use the command::

  squeue -j 12345

To delete a batch job, you need to know the job ID and then use the scancel command::

  scancel 12345


Why does my queued job not start?
---------------------------------

Jobs may be queued for various reasons. A job may be waiting for resources to become available. Or you might have hit a limit for the maximum number of jobs that can be
running on the system. One way to determine why a job is queuing is to use the ``scontrol show job`` command. For example, if the job ID is 12345::

  scontrol show job 12345

The Reason field, normally near the top of the scontrol show job output may be helpful. Some common reason messages include:

**JobHeldUser**

This means your job is held because your ARC project has run our out of compute "credit". Please contact support@arc.ox.ac.uk for a top up.

You can release jobs held using the command::

  scontrol release <JobID> 

Where <JobID> above is the numeric job ID to release.

To check your credit balance at any time you can use the command:: 

  mybalance 

Email us when credit is low and we will add credit so that your jobs are not held.


**PartitionTimeLimit**

The walltime you have requested is in excess of the time allowed in the partition you have submitted to.
 

**ReqNodeNotAvail**

The most common reason for this message is that the nodes are reserved. This may be for systems maintenance or in the case of co-investment nodes, reserved by the stakeholder.

**Priority**

Your job is waiting because jobs with a higher scheduling priority are in the queue ahead of it. Job priority is a fairly complicated calculation, taking in a variety of factors ranging from the size and run time of the job to a users (or accounts) recent use of the cluster. The cluster operates a "fair share" policy to ensure that all users of ARC's systems get equal access to resources. One of the main factors decreasing your job's initial scheduling priority will be your recent usage of the ARC systems; as more and more of your submitted jobs run on the clusters the successful running of those jobs will gradually erode the level of priority accorded by the scheduler to your next upcoming job. This is to ensure that all users of ARC's systems get a "fair share" of the resource and than no single individual or project can dominate usage of the clusters e.g. by submitting thousands of jobs. The scheduler's fair-share algorithm will gradually increase a waiting job's priority in the queue. A job will be scheduled once there are no jobs with a higher scheduling priority in the queue ahead of it.

Fair-share decay is calculated every 5 minutes, with a half-life of 14 days. It will therefore have no effect if you have not had a running job in the previous 28 days.

Please note fair-share priority is not linked to number of jobs you submitted and are queued, only the number of jobs you have successfully run on the systems. 

**Resources**

Your job is currently top of the job queue and waiting for enough compute resource to become available. It will then start.

**QOSGrpNodeLimit**

This happens when you have specified a Quality of Service resource (normally because you are requesting Co-Investment nodes). The message indicates that all the nodes available for your QoS are currently in use. Your job will run once enough resource becomes available on your QoS. If you need to run urgently you could simply run the line::

    scontrol update jobid=<jobid> qos=standard
    
..to update your job to use **standard** Qos, this will allow your job to use the standard QoS and therefore have access to the whole cluster.

**QOSMaxWallDurationPerJobLimit**

This issue is typically seen by users that are members of ARC projects which have Basic or "free" quality of service. Basic QoS is typically associated with all projects where a Principal Investigator’s
home Department is based within an MSD Department. Basic QoS allows free use at the point of access with the following scheduling attributes:

  - You have a *maximum* timelimit of 24 hours per job. 
  - You can submit multiple jobs BUT only run one concurrent job on the clusters.
  - Jobs will have a lower prioritisation than jobs submitted with Standard QoS and will therefore be scheduled around Standard and Priority QoS jobs

More details here: https://www.arc.ox.ac.uk/arc-service-level-agreements


Why does my job fail with the error "/bin/bash^M: bad interpreter: No such file or directory"
---------------------------------------------------------------------------------------------

You have edited your submission script using a Windows editor (such as notepad).  Windows has extra characters at the end of a line,
which are not needed under Linux and which cause the Torque qsub command to fail.

Eliminate the extra characters by using the following command::

  dos2unix myScript.sh
 
Why does my job fail/die after running for just a few seconds?
--------------------------------------------------------------

There is a problem with the job submission script.  The error output from the job submission should provide some information as to why the job failed.
If you require help with determining what the problem is, please contact the ARC support team and provide relevant details to help with diagnosis.
This should include Job ID and batch script details.  Time/date of submission and which ARC system the job was submitted is also useful.

 
Why does my job fail/die after running for a few hours/days?
------------------------------------------------------------

Possibly your job has run out of walltime.  Every job has a walltime limit that is specified in the submission script or by the sbatch command or picked 
up from the relevant default value.  See next question regarding requesting increase to the walltime of a running job.

 
How can I increase the walltime of a running job?
-------------------------------------------------

If you submit a job and find that it may not finish within the requested walltime, then to avoid having the job terminated when it reaches its walltime limit,
please contact the ARC support team with details of the job (Job ID and ARC system the job is running on) requesting that the job walltime be increased. 
If you are able to estimate the additional walltime required this is helpful.

 
How can I get an email notification when a job begins/finishes?
---------------------------------------------------------------

Include the ``--mail-type`` and ``--mail-user`` options in the job submission script.  These can be specified at the beginning of the job submission script as
a line of the form::

  #SBATCH --mail-type=BEGIN,END 
  #SBATCH --mail-user=email.address@unit.ox.ac.uk

or included on the ``sbatch`` command line as::

  sbatch --mail-type=BEGIN,END --mail-user=email.address@unit.ox.ac.uk submit.sh

More details about sbatch options can be found in the sbatch man page (man sbatch)

 
How can I check the availability of free compute nodes?
-------------------------------------------------------

Use the command the SLURM command ``sinfo``


MPI Jobs fail when ``mpirun`` used in a loop or multiple times in a submission script
-------------------------------------------------------------------------------------

If you have a submission script which calls ``mpirun`` multiple times, there may be issues with the second and subsequent calls failing. In this case, put the following command after each ``mpirun`` command (e.g. inside the loop):

``sleep 60``

This delay will allow time for the previous MPI session to close down completely.

OpenMPI Jobs fail with the error ``Failed to modify UD QP to INIT on mlx5_0: Operation not permitted``
------------------------------------------------------------------------------------------------------

There is a known issue with several installed versions of OpenMPI and their communication with the underlying network interfaces. These error messages may occur sporadically and so we recommend adding the following options to the ``mpirun`` command if affected:: 

  mpirun -mca pml ucx -mca btl '^uct,ofi' -mca mtl '^ofi' [MPI executable]

If your job calls ``mpirun`` from a within a wrapper script and not directly, you can use the following environment variable settings which should be added to your submission script before the call to your application::

  export OMPI_MCA_btl='^uct,ofi'
  export OMPI_MCA_pml='ucx'
  export OMPI_MCA_mtl='^ofi'
