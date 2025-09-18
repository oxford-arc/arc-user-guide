ARC Storage
===========

Once your account has been created, you will immediately have access to two persistent storage areas:

``$HOME`` (``/home/username``) with a 15 GiB quota

``$DATA`` (``/data/projectname/username``)  sharing a 5 TiB quota with your project colleagues

Additionally, when you run a SLURM job, a per-job ``$SCRATCH`` and ``$TMPDIR`` for temporary data/workfiles is created. 

``$TMPDIR`` is local to a compute node

``$SCRATCH`` is on a shared file system and available to all nodes in a job, if a job spans multiple nodes. 

Using ARC ``$SCRATCH`` storage
------------------------------

As a rule we recommend that you use your ``$DATA`` area to store your data, but utilise the per job ``$SCRATCH`` or ``$TMPDIR`` area - especially for intermediate or temporary files. Generally you would copy all required input data at the start of your job and then copying results back to your **$DATA** area.

A simple example of how to do this would be:

.. code-block:: bash

  #!/bin/bash
  #
  # After SBATCH lines in submission script...
  #
  cd $SCRATCH || exit 1
  # 
  # Copy job data to $SCRATCH
  #
  rsync -a $DATA/myproject/input ./
  rsync -a $DATA/myproject/bin ./ 
  #
  # Job specific lines...
  #
  module load foss/2020b

  mpirun ./bin/my_software
  #
  # Copy data back from $SCRATCH to $DATA/myproject directory
  #
  rsync -a --exclude=input --exclude=bin ./ $DATA/myproject/
  
This example copies the directories ``$DATA/myproject/input`` and ``$DATA/myproject/bin`` into ``$SCRATCH`` (which will then contain directories ``input`` and ``bin``). The script then runs ``./bin/my_software``; and copies all files in the ``$SCRATCH`` directory - excluding directories ``input`` and ``bin`` - back to ``$DATA/myproject/`` once the ``mpirun`` finishes.

The process is more straightforward if you only need to copy single input/ouput files when the application is centrally hosted, for example:

.. code-block:: bash

  #!/bin/bash
  #
  # After SBATCH lines in submission script...
  #
  # Load appropriate module, in this test case we are using Gaussian
  
  module load Gaussian/16.C.01
  
  # Set input/output filenames
  #
  export INPUT_FILE=test397.com
  export OUTPUT_FILE=test397.log
  
  echo "copying input from $SLURM_SUBMIT_DIR/$INPUT_FILE ..."
  
  cd $SCRATCH || exit 1
  cp $SLURM_SUBMIT_DIR/$INPUT_FILE ./

  # Job specific application command line...
  #
  g16 < $INPUT_FILE > $OUTPUT_FILE

  # Copy output back from $SCRATCH to job directory
  #
  echo "copying output back to $SLURM_SUBMIT_DIR/ ..."
  cp $OUTPUT_FILE $SLURM_SUBMIT_DIR/
 
If you are unable to access either of these directories, please let us know.

.. note::
  Both **$SCRATCH** and **$TMPDIR** are not persistent; they will be automatically removed on job exit. It is important that your job copies all files into your $DATA area before it exits; we will not be able to recover your data if you left it on ``$SCRATCH`` or ``$TMPDIR`` once a job finished. 
  
Especially if your job is long running, we would advise to regularly copy intermediate data back to your $DATA directory. It is likely also advisable to set up your job to trap an impending timeout within your job or job script to allow you to save your work in case a job times out.    

An example of how to do both of these from a submit script would be:

.. code-block:: bash

  #!/bin/bash
  #SBATCH --job-name=test
  #SBATCH --ntasks-per-node=1
  #SBATCH --nodes=1
  #SBATCH --time=00:02:00
  #SBATCH --signal=B:SIGINT@60 # ask SLURM to send a SIGINT to the job's control script 60 seconds before a timeout

  # define a function to rsync my data to it's destination
  copy_data()
  {
    rsync -a --partial /scratch/ $DATA/my_job_output/
  }

  # define what to do when SLURM signals an impending job timeout
  sig_handler()
  {
    echo "BATCH interrupted"
    echo "Exiting abnormally - triggering a copy of /scratch to $DATA"
    copy_data
    # now kill the process; this should trigger a final copy after it exited (time permitting)
    kill ${jobpid}
    exit 2
  }

  trap 'sig_handler' SIGINT

  ./bin/my_software > /scratch/my_jobrun_${SLURM_JOB_ID} &
  jobpid=$!

  # start a loop copying contents of /scratch/ every hour
  while true ; do
    copy_data
    sleep 3600
  done &

  # wait for the original job to finish
  wait ${jobpid}

  # after the job finishes, copy the output back
  # in case of a timeout, the job would have been killed by the trap handler
  copy_data

.. note::

  Under certain circumstances - for example, if your code is using the node CPUs very efficiently, or your code is generating a lot of data to copy, or if you are using MPI - the above can have serious negative impact on code performance / code efficiency. Under those circumstances, we recommend requesting extra cores to handle the data copying - i.e. asking for more cores than you allocate for your application (at a level of 1-2 cores per node) to leave cores 'free' to handle the data moving.

Quota
-----

By default your ``$HOME`` area will have a 15 GiB quota while the ``$DATA`` area will have a 5 TiB quota that is shared between yourself and the other members of your project.

To check your quota use the command:

.. code-block:: shell

  myquota

This command will list both your home quota and the quota of shared project data areas that you are a member of.

We can provide more detailed statements of data area quota usage to project leaders on request.

Larger Data quotas (more than 5 TiB) are available on request as a chargeable service. Please contact ARC support for further information.

If you are a user of Anaconda virtual environments and find yourself over quota in ``$HOME``, please check your conda package cache size. Information on this can be found here: `Anaconda Package Cache <https://arc-software-guide.readthedocs.io/en/latest/python/anaconda_venv.html#conda-package-cache>`_

Backups
-------

We do NOT currently create backups of the ARC shared file system (although the file system IS resilient to failures). We therefore strongly encourage you to keep copies of your files elsewhere, particularly when that data is critical to your research.

Snapshots
---------

Snapshots have been configured to be generated on home directories as well as projects data directories. Snapshots provide easy access to older versions of files. This is useful if files have been accidentally deleted or overwritten. It does not, however, constitute a backup; old snapshots will not be kept indefinitely (max. two weeks for weekly snapshots).

Within your home directory, there is a .snapshot directory which contains the hourly, daily and weekly snapshots available. 
To list/examine the snapshots, simply ``cd`` into ``$HOME/.snapshot`` (and for projects data, ``cd`` into ``/data/<projectname>/.snapshot``), and list the available directories:

.. code-block:: shell

  cd $HOME/.snapshot
  ls -1tr

You will see a listing of all snapshots (reverse order, i.e. newest last):

.. code-block:: text

  weekly.2020-08-02_0015
  weekly.2020-07-26_0015
  daily.2020-08-06_0010
  hourly.2020-08-07_1105
  hourly.2020-08-07_1005
  hourly.2020-08-07_0905
  daily.2020-08-07_0010
  hourly.2020-08-07_1305
  hourly.2020-08-07_1205
  hourly.2020-08-07_1405

To choose a particular snapshot, simply change into the relevant directory:

.. code-block:: shell

  cd hourly.2020-08-07_1205

Within those directories you will essentially find a copy of your home directory as it was when the snapshot was taken.

If you've accidentally deleted a file in your home directory which existed earlier than the last snapshot, then you can retrieve the older copy from the snapshot. Simply find the version of the file you are after within the .snapshot structure, and copy it back into your home directory.

For example - assuming you have deleted a file 'ARC-Introduction-2018-Hilary.pptx' from folder ``$HOME/Documents`` by mistake. To recover it, the steps would be:

.. code-block:: console

  [$(arcus) Documents]$ pwd
  /home/ouit0622/Documents

  [$(arcus) Documents]$ ls -1
  ARC-Introduction-2018-Hilary.pptx
  arc_job_submission_exercises
  arc_presentation
  MATLAB

  [$(arcus) Documents]$ rm ARC-Introduction-2018-Hilary.pptx

  [$(arcus) Documents]$ ls -1
  arc_job_submission_exercises
  arc_presentation
  MATLAB

  [$(arcus) Documents]$ cd $HOME/.snapshot/
  [$(arcus) .snapshot]$ ls -1tr
  weekly.2020-08-02_0015
  weekly.2020-07-26_0015
  daily.2020-08-06_0010
  hourly.2020-08-07_1105
  hourly.2020-08-07_1005
  hourly.2020-08-07_0905
  daily.2020-08-07_0010
  hourly.2020-08-07_1305
  hourly.2020-08-07_1205
  hourly.2020-08-07_1405

  [$(arcus) .snapshot]$ cd hourly.2020-08-07_1405

  [$(arcus) hourly.2020-08-07_1405]$ pwd
  /home/ouit0622/.snapshot/hourly.2020-08-07_1405

  [$(arcus) hourly.2020-08-07_1405]$ cd Documents

  [$(arcus) Documents]$ ls -1
  ARC-Introduction-2018-Hilary.pptx
  arc_job_submission_exercises
  arc_presentation
  MATLAB

  [$(arcus) Documents]$ cp ARC-Introduction-2018-Hilary.pptx $HOME/Documents

  [$(arcus) Documents]$ $HOME/Documents/
  [$(arcus) Documents]$ pwd
  /home/ouit0622/Documents

  [$(arcus) Documents]$ ls -1
  ARC-Introduction-2018-Hilary.pptx
  arc_job_submission_exercises
  arc_presentation
  MATLAB
  
Note: Snapshots do not take up space in the file system, i.e. they do not count towards your quota. If you are trying to determine where in your home directory space is used,
you must exclude the ``.snapshot`` directory from your commands as otherwise the information would be incorrect.
