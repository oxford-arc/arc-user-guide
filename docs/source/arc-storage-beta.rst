ARC Storage (Beta testing)
==========================

[Below needs to be updated]
  
Once your account has been created, you will immediately have access to two persistent storage areas:

**$HOME** (/home/username) with a 15GB quota

**$DATA** (/data/projectname/username)  sharing a 5TB quota with your project colleagues

Additionally, when you run a SLURM job, a per-job **$SCRATCH** and **$TMPDIR** for temporary data/workfiles is created. 

**$TMPDIR** is local to a compute node

**$SCRATCH** is on a shared file system and available to all nodes in a job, if a job spans multiple nodes.

Using ARC $SCRATCH storage
--------------------------

Note: Both **$SCRATCH** and **$TMPDIR** are not persistent; they will be automatically removed on job exit. It is important that your job copies all files into your $DATA area before it exits; we will not be able to recover your data if you left it on **$SCRATCH** or **$TMPDIR** once a job finished.

As a rule we recommend that you use your **$DATA** area to store your data, but utilise the per job **$SCRATCH** or **$TMPDIR** area - especially for intermediate or temporary files. Generally you would copy all required input data at the start of your job and then copying results back to your **$DATA** area.

A simple example of how to do this would be::

  #!/bin/bash
  #
  # After SBATCH lines in submission script...
  #
  # 
  cd $SCRATCH || exit 1
  # 
  # Copy job data to $SCRATCH
  #
  rsync -av $DATA/myproject/input ./
  rsync -av $DATA/myproject/bin ./ 
  #
  # Job specific lines...
  #
  module load foss/2020b

  mpirun ./bin/my_software
  #
  # Copy data back from $SCRATCH to $DATA/myproject directory
  #
  rsync -av --exclude=input --exclude=bin ./ $DATA/myproject/
  
This example copies the directories ``$DATA/myproject/input`` and ``$DATA/myproject/bin`` into **$SCRATCH** (which will then contain directories ``input`` and ``bin``). The script then runs ``./bin/my_software``; and copies all files in the **$SCRATCH** directory - excluding directories ``input`` and ``bin`` - back to ``$DATA/myproject/`` once the ``mpirun`` finishes.

The process is more straightforward if you only need to copy single input/ouput files when the application is centrally hosted, for example::

  #!/bin/bash
  #
  # After SBATCH lines in submission script...
  #
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

