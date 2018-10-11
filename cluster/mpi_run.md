---
Author:
- |
    Brian Moore, University Networking and Research Computing (UNRC)     
    South Dakota State University
Title: |
    Hoe to run an mpi job on the cluster
---


# Parallel computing

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

sDared memory (multicore, [typically] single server), e.g. openmp

![image](shared-TACC.png)

distributed memory (one job runs on several servers), e.g. mpi

![image](dist-TACC.png)

From “Introduction to Parallel Computing” slides from TACC presentation
by Frank Willmore at Univ. Texas

## High Throughput Computing

![image](hpc-htc-lmichael-uwisc_crop.png)

From “Why High Throughput Computing” slides by Lauren Michael of
Wisconsin’s CHTC

### MPI: Message Passing Interface

MPI is an API (application programming interface) for distributed memory
computing. Several implementations exist. Usually a C or fortran (less
commonly C++) source code.

-   <http://www.mcs.anl.gov/research/projects/mpi/>\
    The standard definition, from Argonne Lab.

-   <https://www.open-mpi.org/>\
    Commonly used implmentation. See especially the FAQ.

-   <https://computing.llnl.gov/tutorials/mpi/>\
    Tutorial from LLNL

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

### copy, compile the hello, world MPI example

```
mooreb@blackjack:~> mkdir testmpi
mooreb@blackjack:~> cd testmpi
mooreb@blackjack:~/testmpi> cp /home/mooreb/examples/mpi/openmpi/llnl/mpi_hello.c .
mooreb@blackjack:~/testmpi> cp /home/mooreb/examples/mpi/openmpi/llnl/mpirun.pbs .
mooreb@blackjack:~/testmpi> module load openmpi
mooreb@blackjack:~/testmpi> mpicc -O2 -o mpi_hello mpi_hello.c
mooreb@blackjack:~/testmpi> ls
mpi_hello  mpi_hello.c  mpirun.pbs
mooreb@blackjack:~/testmpi>
```

### running mpi hello, world interactively

With such a small program, you can run an few processes interactive from
the command line.

```
mooreb@blackjack:~/testmpi> mpirun -np 4 ./mpi_hello
--------------------------------------------------------------------------
WARNING: There is at least non-excluded one OpenFabrics device found,
[...]

  Local host: blackjack
--------------------------------------------------------------------------
Hello from task 0 on blackjack!
MASTER: Number of MPI tasks is: 4
Hello from task 3 on blackjack!
Hello from task 2 on blackjack!
Hello from task 1 on blackjack!
[blackjack:04067] 3 more processes have sent help message help-mpi-btl-openib.txt / no active ports found
[blackjack:04067] Set MCA parameter "orte_base_help_aggregate" to 0 to see all help / error messages
mooreb@blackjack:~/testmpi> mpirun --mca btl tcp,self -np 4 ./mpi_hello
Hello from task 0 on blackjack!
MASTER: Number of MPI tasks is: 4
Hello from task 1 on blackjack!
Hello from task 2 on blackjack!
Hello from task 3 on blackjack!
mooreb@blackjack:~/testmpi>
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