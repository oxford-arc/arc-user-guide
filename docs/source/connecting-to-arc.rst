Connecting to ARC
=================

Access to ARC systems is via Secure Shell Protocol (SSH).

Fingerprints for the login node host keys can be found at the end of this section.

Connecting from Linux
---------------------

Linux and Mac users should use ssh from a terminal to connect to the ARC systems.

To connect to the ARC cluster::

    ssh -X username@arc-login.arc.ox.ac.uk

To connect to the HTC cluster::

    ssh -X username@htc-login.arc.ox.ac.uk

Access to ARC is available only from within the University of Oxford network. If you are not on the University network, you should use the University VPN service to connect. If you are unable to use the VPN service, you may be able to register your static IP with the ARC team (support@arc.ox.ac.uk) to enable access.

If you are connecting from outside the University network (or from the VPN service) you will need to connect via gateway.arc.ox.ac.uk - ensure you specify your ARC username in the ssh command, as shown below::

    ssh -X username@gateway.arc.ox.ac.uk

You can then SSH to arc-login or htc-login as required.

.. note::

    The above examples include the -X option which allows the tunnelling of X11 graphics from the cluster to your local machine. This is optional. However, if you do require graphics, an X11 server needs to be running on your local machine. For Linux this is typically part of the installation - however on a Mac you may need to install an X11 server such as Xquartz.

Connecting from Windows
-----------------------

SSH is available as a command in PowerShell from Windows 10. Users of Windows 10 or newer should be able to connect to ARC from PowerShell with the commands given above. However, users of older Windows versions, or those requiring X11 forwarding, will need to download and install an application that allows them to connect - a popular example is MobaXterm.

MobaXterm for Windows can be found at `https://mobaxterm.mobatek.net/ <https://mobaxterm.mobatek.net/>`_

Copying data to/from ARC
------------------------

MacOS/Linux users (and other Unix like operating systems) can use rsync, sftp, or scp. 

.. note::

    $HOME and $DATA are shared between the ARC and HTC systems, so the instructions below will work for both systems. gateway.arc.ox.ac.uk has no access to $HOME, but $DATA is available. 

Copying to the ARC systems
^^^^^^^^^^^^^^^^^^^^^^^^^^

1) Make sure you know the path to the destination directory. Login to ARC and create a directory in $DATA. Change to that directory and run the command 'pwd'; this will show you the full path to the destination directory. For example if you have created a directory myscripts in $DATA, 'pwd' will show output like::

    [ouit0578@gateway Scripts]$ pwd
    /data/system/ouit0578/myscripts

2) On your Mac or Linux PC open a terminal use the command 'cd directoryname' to get to the source directory where the file(s) you want to copy are located. The command pwd will show the full path to this directory, take note of this path. For example if you have a directory scripts the path might be something like::

    Scripts$ pwd
    /local/scripts
 
3) Still on your MAC or Linux PC to copy files use an scp command with the format::

    scp path/filename arcuserid@gateway.arc.ox.ac.uk:/path_to_destination_directory/

Continuing with the above example - to copy all the files in /local/scripts this would be::

    scp /local/scripts/* ouit0578@gateway.arc.ox.ac.uk:/data/system/ouit0578/myscripts/

To copy an entire directory hierarchy use the recurse option, -r 

For example::

    scp -r /local/scripts/* ouit0578@gateway.arc.ox.ac.uk:/data/system/ouit0578/myscripts/

In both the above cases, you will be prompted to authenticate with your ARC password.

Copying from the ARC systems
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The reverse should be used for copying from arc. Using the methods above, take note of the source (ARC) and destination (local PC) paths. In this example these will be::
 
ARC directory:  /data/system/ouit0578/mydata

Local PC directory: /local/datafromarc
 
Copying data from Mac or Linux PC
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 
To copy files from your local PC::
 
    scp ouit0578@gateway.arc.ox.ac.uk:/data/system/ouit0578/mydata/* /local/datafromarc/
    
Again a recursive copy can be made using the -r option::

    scp -r ouit0578@gateway.arc.ox.ac.uk:/data/system/ouit0578/mydata/* /local/datafromarc/

In both the above cases, you will be prompted to authenticate with your ARC password.

Copying to/from Windows clients
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Windows users can use tools such as MobaXterm or WinSCP to copy files. Use the discovery method in step 1) above to work out the remote ARC path for the transfer.

