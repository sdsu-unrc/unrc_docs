# South Dakota State University
## "Roaring Thunder" Linux Cluster

In fall of 2018, the new SDSU Linux cluster, "thunder", became operational and is now in testing mode.  They system is housed in the Admin building (Morrill hall) data center and consists of 56 compute nodes, 5 big memory nodes, 4 gpu nodes, and 450 TB GPFS parallel file system.

[comment]: # (https://sdsucollegian.com/5546/news/new-high-performance-computing-cluster-thrills-researcher/)
[comment]: # (https://brookingsregister.com/article/new-high-performance-computing-cluster-thrills-sdsu-researcher)

![vncviewer screenshot](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/thunder/thunder.png)

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

### Command Line Linux (CLI)

The main way to interact with the cluster is through a remote shell terminal program, with Linux command line interface syntax.  The main tasks to master at the outset are how to manage files, folders and data from the commande line, editing text files within the terminal (using console editors such as vi or nano), interacting with the environment module system, and creating, submitting and monitoring batch jobs with the cluster job scheduler, SLURM.

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
[ ...]





### SLURM scheduler

### Disk quotas and policies

### Examples

### Contact

[Brian Moore](https://www.sdstate.edu/directory/brian-moore)
