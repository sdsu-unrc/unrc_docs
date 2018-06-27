
## Environment modules (program selection)

A key reason for using the cluster (or other Linux server) is the
ability to build and install new programs. This can be done either by
the user, or system support staff and made available for all users.

A standard tool to aid this effort is the environment module system, a
flexible framework to add or remove programs from a users’ current
environment variables (the `$PATH`, etc.). From the point of view of the
user, this provides a (partial) menu of the available programs on the
system. A good link for more information on environment modules is
<http://www.admin-magazine.com/HPC/Articles/Environment-Modules>.

A good place to start with environment modules is with the
`module_avail` command, which will show the list of programs.

```ShellSession
mooreb@blackjack:~> module avail

------------------------------ /usr/share/modules ------------------------------
3.1.6                   modulefiles/module-info modulefiles/use.own
modulefiles/dot         modulefiles/modules
modulefiles/module-cvs  modulefiles/null

------------------------ /usr/share/modules/modulefiles ------------------------
dot         module-cvs  module-info modules     null        use.own

------------------------------ /test1/opt/modules ------------------------------
E-MEM/1.0.1            glimmer                openFOAM/17.06
FAST/8.16.0            gracob                 openmpi/3.0.0(default)
FASTX/0.0.14           hdf5/1.10.1p(default)  openmpi/3.0.0.1
FastQC/0.11.7          hdf5/1.10.1p-mva       openmpi/3.0.1
[...]

----------------------------- /shared/modulefiles ------------------------------
ansys/0.0.15                 bio/java/1.8.0
ansys/14.5.0                 bio/jellyfish/2.2.3
bio/ABySS/1.5.2              bio/meme/4.11.2_2
bio/BRAKER1/v1.9             bio/mothur/1.36.1
bio/BUSCO/2016_12            bio/mothur/1.38.1.1
[...]
```

The list is quite long ([...] indicates more listing omitted). Historically,
many program modules were put in the `bio` tree; more recently, a second folder for modules has been created (`/test1/opt/modules`), in addition to `/shared/modulefiles`

To load a given program into your environment use the `module load`
command. To view loaded modules, use `moudule list`. An example session
follows.

```console
mooreb@blackjack:~> matlab
If 'matlab' is not a typo you can run the following command to lookup the package that contains the binary:
    command-not-found matlab
-bash: matlab: command not found
mooreb@blackjack:~> module load matlab
mooreb@blackjack:~> module list
Currently Loaded Modulefiles:
  1) matlab/R2016b
mooreb@blackjack:~> matlab
MATLAB is selecting SOFTWARE OPENGL rendering.

                            < M A T L A B (R) >
[...]
```

In the example, the `matlab` command is not found until the environment
module is loaded.

## Using modules in PBS scripts

When you run a job on a worker node, using the Moab/Torque PBS scheduler program, you load the modules just as you would do from the interactive command line, but there is a need also include a line to initialize the module system on the node, with the command:

```bash
. ${MODULESHOME}/init/sh
```

So, for example, a sample script to run an mpi "hello, world" program is at
`/home/mooreb/examples/mpi/openmpi/llnl/mpirun.pbs`, and listed below.

```bash
#!/bin/bash
# File mpirun.pbs
# Runs a simple mpi job on bigjack (e.g. a hello, world)
# Brian.Moore@sdstate.edu ~Jun 2018

#PBS -q batch2
#PBS -l nodes=2:ppn=12
#PBS -l walltime=00:30:00

. ${MODULESHOME}/init/sh
module use /test1/opt/modules
module load openmpi/3.0.0

cd $PBS_O_WORKDIR
cat $PBS_NODEFILE | uniq

date

# Below is the "normal" mpirun invocation--runs on IB if available
# Uncomment and use it if running in the production (batch2) queue
time mpirun -np $PBS_NP ./mpi_hello

# If running on debug nodes, use only tcp as IB is not present
# Comment this out if running in the batch2 queue
#time mpirun --mca btl tcp,self -np $PBS_NP ./mpi_hello

date
```
