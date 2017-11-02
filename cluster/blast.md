
---

Running a job on the cluster: BLAST+
===

[Brian.Moore@sdstate.edu](Brian.Moore@sdstate.edu)  University Networking and Research Computing (UNRC)

---

To run a job on the cluster, you need to configure a PBS script to deploy your job to a node.  The script consists of resource requests followed by the program command.  You need to know how to run your program before you configure the script.  This might require testing/development on other servers or brief interactive tests on the head node.  We assume you have done that.  Here we present an example of the actual job submission process.

blastx example run
------------------

We have developed a small example run to get started. The query sequence
data and the example PBS script are located at
`/test1/examples/blast`. The query sequences are in the file
`example.fa`; the example PBS script is `blastxexample.pbs` This example has been set up to illustrate basic functionality, but not take too long to run.

Links to the example files in the unrc-docs respository:  
[blastxexample.pbs](./blastxexample.pbs)  
[example.fa](./example.fa)


```bash
#!/bin/bash
#  File blastxexample.pbs
#  Data and example from Priya Swaminathan
# ~Nov 2017 brian.Moore@sdstate.edu, padmapriya.swaminathan@sdstate.edu

#PBS -q debug
#PBS -l nodes=1:ppn=4
#PBS -l walltime=1:00:00
#PBS -W x=NACCESSPOLICY:SINGLEJOB

cd $PBS_O_WORKDIR
echo "Running in folder:" pwd
echo "Job ran on node(s):"
cat $PBS_NODEFILE | uniq
echo "Total reserved processors:"
echo $PBS_NP

. ${MODULESHOME}/init/sh
module load bio/blast+

date

# 2016-11-09 Note change in latest nr db location!
time blastx -num_threads $PBS_NP -db /test1/mooreb/blastdb/nr/nr  \
-query example.fa -evalue 1e-10 -word_size 3 -gapopen 11 -gapextend 1  \
 -outfmt 10 -out exampleout.csv

date
```


The BLAST+ program
------------------

BLAST (Basic Local Alignment Search Tool) is a program to compare
nucleotide or protein sequences to databases and find the significance
of the matches. To run the program, one needs the program itself, query
sequences and access to a blast database (either local, or accessible
through the network) to run against.

For general information about the program itself and how it works, see,
for example <http://blast.ncbi.nlm.nih.gov>. On that page, one can
scroll down to choose from various options, for example nucleotide
blast, and it will open a web portal where you can run query sequences
on NCBI’s server. For documentation on the program, the command line
user manual (<http://www.ncbi.nlm.nih.gov/books/NBK279690/>) is a good
place to start, for reference on the program itself and also on command
line use, which is how the program is used on the cluster. To obtain a
copy of the program itself (again, command line version) see
<ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/>. This is
what we have installed on the cluster in a shared location (the binary
version), but you can also download it for use on your own systems or in
your home folder. The program itself is fairly easy to download and use.
A much larger concern is getting the database you want to run against.

The program discussed here is more properly called BLAST+, to
distinguish it from an earlier version (still in use in some
applications) with the Blast name; this older program is now referred to
on the NCBI site as “legacy” Blast. This documentation treats only
Blast+.

Blast databases
---------------

Pre-formatted Blast databases can be downloaded from the NCBI site.
Currently we have two databases for general use, located on the cluster
at `/home/mooreb/blast/db`. The nr database has non-redundant protein
sequences; nt is a partially non-redundant nucleotide sequence database.
These databases are updated on demand. So far, each time we have
updated, we have saved the old version in offline storage so they can be
brought back if necessary to repeat a query against an older version. To
see when they were updated, look at the oldest date stamp in the folder;
a command such as `ls -altr` will list all files with date stamps,
showing oldest last.

The nr database currently occupies about 68 GB on disk; nt about 37 GB.
For this reason we discourage users from frequently downloading Blast
databases to their own local storage. Contact us first. If we can put
the database in a central location, it will save on overall disk space.

Blast databases can also be created from unformatted genomic data,
nucleotide or protein sequences. This topic is not covered here; contact
one of us if you are interested in getting more information.

Accessing the BLAST+ program on the cluster; options
----------------------------------------------------

On the cluster head node (`blackjack`) and worker nodes, we have
implmented an environment module system. To see the menu of available
modules, type `module avail` at the command line; `module list` is used
to see currently loaded modules. Once the module is loaded, the
executable commands for BLAST+ become available. There are several
commands (see the manual for more detail), for example `blastn` is for
searching a nucleotide database using a nucleotide query; `blastx` is
for searching a protein database using a translated nucleotide query.

Useful information can be obtained from the help options within the
program itself. For brief help, use the `-h` option; for detailed help,
use `-help`. The output from `-help` is quite long. To view the help
options with a pager, use (for example) `blastx -help | less`. Below is
shown an excerpt of the output, from the section about output format
options.

``` {.console}
-outfmt <String>
   alignment view options:
     0 = Pairwise,
     1 = Query-anchored showing identities,
     2 = Query-anchored no identities,
     3 = Flat query-anchored showing identities,
     4 = Flat query-anchored no identities,
     5 = BLAST XML,
     6 = Tabular,
     7 = Tabular with comment lines,
     8 = Seqalign (Text ASN.1),
     9 = Seqalign (Binary ASN.1),
    10 = Comma-separated values,
    11 = BLAST archive (ASN.1),
    12 = Seqalign (JSON),
    13 = Multiple-file BLAST JSON,
    14 = Multiple-file BLAST XML2,
    15 = Single-file BLAST JSON,
    16 = Single-file BLAST XML2
```



### interactive run

Before running the PBS script, if you want, you can run interactively.
This is not required, but it illustrates a point that when you are
developing a PBS script, it can be very useful to know how the program
behaves when from from the command line for a small data set. If you are
doing this on your own, check the load on `blackjack` (using the `top`
command) and it is OK to run the example interactively on blackjack
itself; use no more than `-num_threads 4`.

For the tutorial session, if a few people need to run at the same time,
it is better to initiate an interactive PBS session. Example console
output to illustrate this follow.

``` {.console}
mooreb@blackjack:~> qsub -I -l nodes=1:ppn=4 -l walltime=1:00:00
qsub: waiting for job 2059886.bigjack to start
qsub: job 2059886.bigjack ready

mooreb@node057:~> module load bio/blast+
mooreb@node057:~> mkdir blasttest
mooreb@node057:~> cd blasttest
mooreb@node057:~/blasttest> cp /home/mooreb/examples/blast/example.fa .
mooreb@node057:~/blasttest> time blastx -num_threads 4 \
> -db /home/mooreb/blast/db/nr/nr -query example.fa -evalue 1e-10 \
> -word_size 3 -gapopen 11 -gapextend 1 -outfmt 10 -out exampleout.csv
real    4m49.076s
user    16m29.506s
sys 0m11.077s
mooreb@node057:~/blasttest> exit
logout

qsub: job 2059886.bigjack completed
```

Note that in typing the command, you do not need to use the bash command
continuation (`\`) approach; you can also write it out just as one long
command; the output will wrap to the next line. Then hit return to
execute.

Once you type the command, the terminal will be occupied for how long it
takes to finish. If you want to recover the terminal prompt while you
wait, you can use `ctrl-Z` to suspend the command, then `bg` to put it
in background. The output of the time command will still be written to
the terminal when it finishes. During the run you can use `top` or other
commands to monitor the job. Since we only asked for 4 processors, there
might be other jobs on your node.

Another thing to note in this case is we chose the csv format for the
output report. You can use WinSCP or another file transfer program to
copy the file to your local system and view the results quickly in a
spreadsheet. In the PBS example below, we also show the XML report
option, which can be useful when the results are to be read by another
program. Another option is to use the BLAST archive format
(`-outfmt 11`) which can be used with the BLAST+ `blast_formatter`
command to create csv or XML or other reports from the archive after the
run is finished.

### run using the Moab/PBS scheduler

Once you have successfully run a small data set interactively, the next
step is to develop a PBS scheduler submission script, a Linux bash
script with your command inside it and a few resource requests (lines
beginning with `#PBS`) at the beginning of the script. An example is
given below. This file, `blastxexample.pbs` should also be in the
examples folder, `/home/mooreb/examples/blast`.

If you are after high-throughput–or running many, many sequences–now you
can increase the amount of data in the input data file with the query
sequences in it. This script runs on one cluster worker node. If you
have many thousands of sequences, you will probably want to pre-process
them with a script of some kind (typicall bash or perl) to cut them into
“node-sized” chunks. This is just one approach, but it gives an idea of
what can be done with the cluster.



To run this example, you will have to copy the PBS script file to the
folder you are working in and submit it to the scheduler.

``` {.console}
mooreb@blackjack:~/blasttest> cp /home/mooreb/examples/blast/blastxexample.pbs .
mooreb@blackjack:~/blasttest> ls
blastxexample.pbs  example.fa
mooreb@blackjack:~/blasttest> qsub blastxexample.pbs
2061171.bigjack
mooreb@blackjack:~/blasttest>
```

When the job is submitted, you get a notification of the job number. At
this point you can use the usual scheduler commands (`qstat`, `showq`,
`checkjob`) to inquire as to the status of your job.

When the job has finished, you should see two new files. They will have
the same name as your submission script, but with two extensions, one of
them beginning with `.o`, the other with `.e`; these are the standard
output and standard error from the PBS script. In this case, we
specified the actual output file in our executable line with the `-out`
option, so the output will be in that file. The console capture below
shows listing and displaying the files.

``` {.console}
mooreb@blackjack:~/blasttest> ls
blastxexample.pbs           blastxexample.pbs.o2061171  exampleresults.xml
blastxexample.pbs.e2061171  example.fa
mooreb@blackjack:~/blasttest> cat blastxexample.pbs.o2061171
Running in folder: pwd
Job ran on node(s):
node017
Total reserved processors:
12
Wed Apr 20 13:33:41 CDT 2016
Wed Apr 20 13:35:33 CDT 2016
mooreb@blackjack:~/blasttest> cat blastxexample.pbs.e2061171

real    1m52.237s
user    16m56.832s
sys 0m6.352s
mooreb@blackjack:~/blasttest>
```
