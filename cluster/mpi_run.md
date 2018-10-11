

# Running an MPI job on the cluster:  Hello, World


[Brian.Moore@sdstate.edu](https://www.sdstate.edu/directory/brian-moore)  University Networking and Research Computing (UNRC)


In this document we take you through how to compile and run a first MPI program on the cluster, an MPI "Hello, World" program.

# Introduction: Parallel computing

Resources for more information

-   <https://computing.llnl.gov/tutorials/parallel_comp/>
    From Lawrence Livermore Lab. Good overall introduction.

-   <https://portal.tacc.utexas.edu/training/course-materials>
    Past course materials from TACC (Texas Advanced Computing Center)

-   <https://portal.tacc.utexas.edu/c/document_library/get_file?uuid=e05d457a-0fbf-424b-87ce-c96fc0077099&groupId=13601>\
    “Introduction to Parallel Computing” slides from TACC presentation
    by Frank Willmore

-   <http://chtc.cs.wisc.edu/>
    Univ. of Wisconsin’s Center for High Throughput Computing

-   <https://meetings.internet2.edu/media/medialibrary/2015/10/19/20151008-michael-why-htc.pdf>
    “Why High Throughput Computing” slides by Lauren Michael of
    Wisconsin’s CHTC.

## Shared-memory vs. Distributed memory computing

Shared memory (multicore, [typically] single server), e.g. openmp

![Schematic figure of shared memory processing, from TACC](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/cluster/shared-TACC.png)

Distributed memory (one job runs on several servers), e.g. MPI

![Schematic figure of distributed memory processing, from TACC](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/cluster/dist-TACC.png)

From “Introduction to Parallel Computing” slides from TACC presentation
by Frank Willmore at Univ. Texas

## High Throughput Computing

![Graphical explanation of High Throughput Computing, contrasted with HPC, from Lauren Michael at U Wisc](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/cluster/hpc-htc-lmichael-uwisc_crop.png)

From “Why High Throughput Computing” slides by Lauren Michael of
Wisconsin’s CHTC


### MPI: Message Passing Interface

MPI is an API (application programming interface) for distributed memory
computing. Several implementations exist. Usually a C or fortran (less
commonly C++) source code.

-   <http://www.mcs.anl.gov/research/projects/mpi/>
    The standard definition, from Argonne Lab.

-   <https://www.open-mpi.org/>
    Commonly used implmentation. See especially the FAQ.

-   <https://computing.llnl.gov/tutorials/mpi/>
    Tutorial from LLNL


### Hello, world MPI program example from LLNL (Lawrence Livermore National Lab, MPI tutorial)

On blackjack, file is located at `/home/mooreb/examples/mpi/openmpi/llnl/mpi_hello.c`

https://computing.llnl.gov/tutorials/mpi/samples/C/mpi_hello.c

```C
/******************************************************************************
* FILE: mpi_hello.c
* DESCRIPTION:
*   MPI tutorial example code: Simple hello world program
* AUTHOR: Blaise Barney
* LAST REVISED: 03/05/10
******************************************************************************/
#include "mpi.h"
#include <stdio.h>
#include <stdlib.h>
#define  MASTER     0

int main (int argc, char *argv[])
{
int   numtasks, taskid, len;
char hostname[MPI_MAX_PROCESSOR_NAME];

MPI_Init(&argc, &argv);
MPI_Comm_size(MPI_COMM_WORLD, &numtasks);
MPI_Comm_rank(MPI_COMM_WORLD,&taskid);
MPI_Get_processor_name(hostname, &len);
printf ("Hello from task %d on %s!\n", taskid, hostname);
if (taskid == MASTER)
   printf("MASTER: Number of MPI tasks is: %d\n",numtasks);
MPI_Finalize();
}
```

### Copy, compile the hello, world MPI example

```
mooreb@blackjack:~> mkdir testmpi3
mooreb@blackjack:~> cd testmpi3
mooreb@blackjack:~/testmpi3> cp /home/mooreb/examples/mpi/openmpi/llnl/mpi_hello.c .
mooreb@blackjack:~/testmpi3> cp /home/mooreb/examples/mpi/openmpi/llnl/mpirun.pbs .
mooreb@blackjack:~/testmpi3> module load openmpi/3.0.0
mooreb@blackjack:~/testmpi3> mpicc -O2 -o mpi_hello mpi_hello.c
mooreb@blackjack:~/testmpi3> ls
mpi_hello.c  mpi_hellp  mpirun.pbs
mooreb@blackjack:~/testmpi3>
```

### Running mpi hello, world interactively

Do this for testing/development only!  For production runs use the job scheduler.

With such a small program, you can run an few processes interactive from
the command line.

```
mooreb@blackjack:~/testmpi3> mpirun -np 4 ./mpi_hello
Hello from task 1 on blackjack!
Hello from task 3 on blackjack!
Hello from task 0 on blackjack!
MASTER: Number of MPI tasks is: 4
Hello from task 2 on blackjack!
mooreb@blackjack:~/testmpi3>
```

### PBS script for mpi hello, world

```bash
#!/bin/bash
# File mpirun.pbs
# Runs a simple mpi job on bigjack (e.g. a hello, world)
# Brian.Moore@sdstate.edu ~May 2017

#PBS -q debug
#PBS -l nodes=2:ppn=12
#PBS -l walltime=00:30:00

. ${MODULESHOME}/init/sh
module load openmpi

cd $PBS_O_WORKDIR
cat $PBS_NODEFILE | uniq

date
# Use below if IB is available (batch2 queue)
#time mpirun -np $PBS_NP ./mpi_hello
# If running on debug nodes, use only tcp as IB is not present
time mpirun --mca btl tcp,self -np $PBS_NP ./mpi_hello
date
```

### Cluster job schedulers

Commonly used Linux cluster scheduler/resource managers:

-   Moab/TORQUE, <http://www.adaptivecomputing.com/>
    We use this.

-   Slurm, <http://slurm.schedmd.com/>

-   Sun Grid Engine, now migrated to Open Grid Scheduler,
    <http://gridscheduler.sourceforge.net/>

-   IBM Platform LSF,
    <http://www-03.ibm.com/systems/in/platformcomputing/products/lsf/>

What do they do?

They decide how a given job gets migrated out to the nodes and started
up (resource management), also how the waiting (idle) jobs get
rearranged or moved around to make things either more fair, more
efficient, or to attempt to meet certain target metrics (job
scheduling).

### Commonly used Moab/TORQUE commands

-   `showq`
    Shows jobs in the queue

-   `qsub`
    Submit a job to the queue

-   `qstat`
    Shows your jobs and their status, some other information about
    queues

-   `checkjob`
    Detailed information about one specific job

-   `pbsnodes`
    Information about nodes and queues, what’s available, status, etc.

Note: the `man` pages for some of these (for example `showq`) may only
be available on `bigjack`. `ssh` to `bigjack` to read `man` page then
come back to `blackjack`



### PBS submit

``` {.console}
mooreb@blackjack:~> mkdir testmpi3
mooreb@blackjack:~> cd testmpi3
mooreb@blackjack:~/testmpi3> cp /home/mooreb/examples/mpi/openmpi/llnl/mpirun.pbs .
mooreb@blackjack:~/testmpi3> cp /home/mooreb/examples/mpi/openmpi/llnl/mpi_hello.c .
mooreb@blackjack:~/testmpi3> module load openmpi
mooreb@blackjack:~/testmpi3> ls
mpi_hello.c  mpirun.pbs
mooreb@blackjack:~/testmpi3> mpicc -O2 -o mpi_hello mpi_hello.c
mooreb@blackjack:~/testmpi3> qsub mpirun.pbs
3004377.bigjack
mooreb@blackjack:~/testmpi3> qstat
Job ID                    Name             User            Time Use S Queue
------------------------- ---------------- --------------- -------- - -----
3004339.bigjack            runopenfoam      mooreb          4833:45: R batch2         
```

### PBS output file

``` {.console}
mooreb@blackjack:~/testmpi3> cat mpirun.pbs.o3004377
node002
node001
Tue Jun 13 08:33:03 CDT 2017
Hello from task 7 on node002!
Hello from task 11 on node002!
Hello from task 4 on node002!
Hello from task 0 on node002!
MASTER: Number of MPI tasks is: 24
Hello from task 3 on node002!
Hello from task 5 on node002!
[...]
```
