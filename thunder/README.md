# South Dakota State University
## "Roaring Thunder" Linux Cluster

In fall of 2018, the new SDSU Linux cluster, "thunder", became operational and is now in testing mode.  They system is housed in the Admin building (Morrill hall) data center and consists of 56 compute nodes, 5 big memory nodes, 4 gpu nodes, and 450 TB GPFS parallel file system.

[comment]: # (https://sdsucollegian.com/5546/news/new-high-performance-computing-cluster-thrills-researcher/)
[comment]: # (https://brookingsregister.com/article/new-high-performance-computing-cluster-thrills-sdsu-researcher)

![vncviewer screenshot](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/thunder/thunder.png)
Photo: Brookings Register/SDSU Collegian

In the picture above, the 450 TB DDN GPFS parallel file system is at the top of the rack on the left. The larger nodes are the specialty nodes (gpu or high memory), the main enclosures for the compute nodes (4 nodes per enclosure) are near the bottom of both racks.


## Quickstart

Documentation is currently being developed for the system.  The basics of getting connected and running a simple job are outlined here.

### Accounts

To gain access to the cluster, for now use the existing "form"
at https://www.sdstate.edu/information-technology/connecting-cluster, and put in `thunder` for the research server name.  Email the form to SDSU.HPC@sdstate.edu; CC to brian.moore@sdstate.edu and chad.julius@sdstate.edu.

### Login

The login node of the cluster is `roaringthunder.sdstate.edu`; you can also use the shorter name `rt.sdstate.edu`.  Use a terminal program (e.g. PuTTY) to make a command line connection to the login node.  When you first log in for the first time, you should see notification that a folder has been automatically created for you in the `/home` area.

### Campus Active Directory credentials

Your username and password for the system will be your current SDSU campus username and password (for example username `brian.moore`, etc.).  Use your SDSU FacStaff account if you have one--check with Support Desk if you are not sure.

If you only have a student account, your username will be the "short" version, usually one string in the pattern: first initial, middle initial, last name (for example `cajulius`).

Email us if you are having login problems after being notified of account creation:
SDSU.HPC@sdstate.edu, CC to brian.moore@sdstate.edu and chad.julius@sdstate.edu.

### Connecting to the system

On the SD State public website, we have information about connecting to the previous cluster (blackjack/bigjack); see https://www.sdstate.edu/information-technology/connecting-cluster. The first step is to initiate a terminal connection, https://www.sdstate.edu/information-technology/terminal-connection-windowsmac.  These instructions are for the previous cluster, but the procedure is the same.  For the hostname, use `rt.sdstate.edu` in place of `blackjack.jacks.local`.  One change is that for the new cluster, `rt.sdstate.edu` should be available from the campus student wireless network.

### Linux Command Line Interface (CLI)

The main way to interact with the cluster is through a remote shell terminal program, with Linux command line interface syntax.  The main tasks to master at the outset are how to manage files, folders and data from the commande line, editing text files within the terminal (using console editors such as vi or nano), interacting with the environment module system, and creating, submitting and monitoring batch jobs with the cluster job scheduler, SLURM.

We have a command line help page at https://www.sdstate.edu/information-technology/linux-command-line-documentation; also at https://github.com/sdsu-unrc/unrc_docs/blob/master/linux_command_line/linux_cmd.md with better formatting.
The material in the CLI guide still has the previous cluster for specific context/examples, but the basic approach is the same for both clusters.

### Environment modules

As on the previous cluster, we employ on thunder environment modules to load custom software.  A key reason for using the cluster (or other Linux server) is the ability to build and install new programs. This can be done either by the user, or system support staff and made available for all users.

A good place to start with environment modules is with the
`module_avail` command, which will show the list of programs.

```ShellSession
[brian.moore@login ~]$ module avail

---------------- /cm/shared/modulefiles_local ----------------------------------
   MCR/R2017b                             h5py/2.8.0
   MCR/R2018b                    (D)      hdf5/1.10.4-cuda
   NCL/6.5.0                              hdf5/1.10.4-for-netcdf
   R/3.5.1-mkl-nox11                      hdf5/1.10.4-mpich-3.3
   R/3.5.1-mkl                   (D)      hdf5/1.10.4                           
   R/3.5.1                                hdf5/1.10.4-zlib-1.2.11
   WRF/3.9.1                              hmmer/3.2.1
```
[...]

The environment modules give us a way to flexibly present different versions of software.  For example, supposing you were looking for the `scipy` module in python.  You might try the following:

```ShellSession
[brian.moore@login ~]$ python
Python 2.7.5 (default, Apr 11 2018, 07:36:10)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import scipy
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named scipy
>>>
```

The default system python does not have this module.  However if you load one of our local python installs, you can find it.

```ShellSession
[brian.moore@login ~]$ module load python
[brian.moore@login ~]$ python
Python 3.7.1 | packaged by conda-forge | (default, Nov 13 2018, 18:33:04)
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import scipy
>>>
```

You notice that in this case we loaded a python 3 version.  This is because there are multiple pythons in the environment modules and the python 3 version is the default, indicated by (D) in the module avail listing.  If we want a non-default module, we can explicitly ask for it.

```ShellSession
[brian.moore@login ~]$ module load python/2.7

The following have been reloaded with a version change:
  1) python/3.7 => python/2.7

[brian.moore@login ~]$ python
Python 2.7.15 |Anaconda, Inc.| (default, May  1 2018, 23:32:55)
[GCC 7.2.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

Notice in the case above, the environment module system was smart enough to realize we already had a version of python loaded and it automatically unloaded the previous version. The command `module list` is good to use always before loading new modules, to see what is already loaded.  Modules can be removed with the `module unload` command.

### SLURM scheduler

The cluster has one login node (`roaringthunder`) and many identical worker nodes behind a private network switch. In our case we have 60 worker nodes (56 compute, 5 big-mem, 4 gpu).  To run on the worker nodes, we submit a batch script to the scheduler.

Don't run long and resource intensive jobs on the login node!!  Use the login node to test your application and code, then deploy to the nodes.

On `thunder`, the scheduler is SLURM, which is the most commonly used scheduler on HPC batch systems.  The old cluster ran Moab/PBS, a different kind of scheduler.

SLURM is open source and there are many good sources of information for it on the internet, for example at https://slurm.schedmd.com/documentation.html.  On the schedmd site, we have found the two page summary very useful, https://slurm.schedmd.com/pdfs/summary.pdf.  An even briefer "cheat sheet" is https://www.chpc.utah.edu/presentations/SlurmCheatsheet.pdf .

Before running a job, it can be useful to do a few basic SLURM commands.  For example, `squeue` shows the state of the job queue, both running and pending jobs.

```ShellSession
[brian.moore@login ~]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
             18233      test  openmpi jeffrey.  R 1-17:18:26      5 node[020-024]
             18238      defq PLATANUS software  R 1-15:13:00      1 big-mem002
             18248      defq PLATANUS software  R 1-00:36:07      1 big-mem003
             18256      test starccm. dillon.p  R    1:44:58      6 node[001-006]
             18257      test starccm. weston.c  R      49:39      6 node[007-012]
             18258      test starccm2 weston.c  R      43:47      6 node[013-018]
```

We see there are a few jobs running, some on just a single node, some on multiple nodes.  In this case, all jobs are running, see the R status under the ST header line.  If all nodes were occupied, we might see also some jobs in pending state, PD.

The PARTITION header refers to the SLURM partition; a partition is a group of nodes.  When you submit your job, it will choose from the nodes in the partition you select (or the default partition, if you don't select).

We can see partition and node information with the `sinfo` command.

```ShellSession
[brian.moore@login ~]$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
defq*        up   infinite     25  alloc big-mem[002-003],node[001-018,020-024]
defq*        up   infinite     40   idle big-mem[001,004-005],gpu[001-004],node[019,025-056]
test         up   infinite     23  alloc node[001-018,020-024]
test         up   infinite     33   idle node[019,025-056]
```

The output of `sinfo` shows the partitions and what the nodes are doing in the partition; alloc means jobs running, idle means available to accept jobs.

Note especially here that the `test` partition has just the 56 compute nodes in it.  If you are running compute intensive, or just general purpose jobs, choose the test partition for your jobs.  The `defq` partition has *all* nodes in it, a mixture of compute, gpu and big-mem.  If you don't know for sure how to select the kind of node you want in your submit script, ask us, or use the `test` partition only.

More information about `squeue`, `squeue` and other SLURM commands can be obtained with the Linux `man` command, for example `man sinfo`.

Other important SLURM commands are `sbatch`, to submit a SLURM job, `scancel`, and `sacct`, to see information about a job, and `srun`.

The `srun` command can be used to initiate a interactive SLURM job.

```ShellSession
[brian.moore@login ~]$ srun --partition=test --pty bash
[brian.moore@node019 ~]$
```

The `--pty` option means execute in psuedo-terminal, and `bash` is the name of the command to execute; `bash`
is the Linux command line interpreter.

In this case, your interactive job is just like a regular SLURM job, but connected to a terminal you can type commands into.  Since we have not specified otherwise, in the example above it will be given default number of processes (one), default maximum walltime, etc.  You can specify non-default values with more options on the `srun` invocation.


### Example SLURM job

For examples in general on the system, you can browse through the `/examples` folder.  Not all of these are guaranteed to work; some have been tested while others have been put there just to give a general idea.

For our example here, let's use a MATLAB example, in `/examples/matlab`.  The MATLAB script we are going to run is in the file `matrixsoln.m`.

```MATLAB
% file matrixsoln.m
% MATLAB matrix linear solution (Ax=b) problem, to illustrate multithreading.
%
A=rand(20000);
b=rand(20000,1);
tic
x=A\b;
toc
norm(A*x-b)
```

This

### Disk quotas and policies


### Contact

[Brian Moore](https://www.sdstate.edu/directory/brian-moore)
