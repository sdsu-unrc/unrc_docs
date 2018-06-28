

## Why use the scheduler (PBS script)?

The organization of the Linux compute cluster  is with one or a few public facing login nodes (`blackjack`, etc.) and many identical worker nodes behind a private network switch. In our case we have about 60 worker nodes, organized into different groups; the largest (and default) group is the `batch2` queue with about 45 nodes.

The scheduler is the route to using the worker nodes. The syntax and mastery of the scheduler/PBS scripts can be demanding but it is crucial to making use of the cluster as a clustered system. If you find that your needs are satisfied by running jobs only interactively on the head node, you should try to migrate to one of our shared-use non-clustered Linux systems (e.g. silvertip); another alternative is to use `flapjack`, which is a public-facing cluster node

This document aims to help users run jobs on the SDSU Linux cluster using the Moab/PBS scheduler. There are two key elements. First, the user must know the correct syntax to run the program from the command line; if necessary, interactive (non-scheduled) testing can be done with small jobs to discover the syntax. Second, the command (or commands) is/are copied into a PBS scheduler script (usually near the end of the script) and submitted to the queue. If necessary, directives are added (near the top of the script) to request resources (nodes, processors, walltime, etc.) in excess or different from the default values.


## Short tutorial example: compiling and running a single-thread C program

I have provided a very simple single-threaded example
program on the cluster called `prime_ex` that does a prime number
search. The files should be located in my home folder underneath the
examples subfolder, i.e. in `/home/mooreb/examples/C`.

You have to first copy the C source code into your home folder or a
subfolder you create, for example as below:

``` {.console}
mooreb@blackjack:~> mkdir testprogram
mooreb@blackjack:~> cd testprogram
mooreb@blackjack:~/testprogram> cp /home/mooreb/examples/C/prime_ex.c  .
mooreb@blackjack:~/testprogram> ls
prime_ex.c
mooreb@blackjack:~/testprogram>
```

For this simple C program, use the gcc compiler:

``` {.console}
gcc prime_ex.c
```

This will create an executable file named `a.out`. You can rename `a.out` to whatever you like (using `mv`), or you can add an option `-o` in the compile to explicitly name the executable created by the compile.

## Testing: running the program on the command line interactively

The program computes all the prime numbers less than or equal to a given input number and writes back one line with the number of primes found. So this particular program takes as input interactively one integer number. To run this program for a small argument, from the command line, interactively, an example session is shown.

``` {.console}
mooreb@blackjack:~/testprogram> ./a.out
Enter nmax:
100
Input nmax = 100
The number of primes less than or equal to 100 is 25
mooreb@blackjack:~/testprogram>
```

The program is written to write the prompt and wait for input. Then when it receives the number and the user hits return, the program runs and the output is written to the screen.

(Btw, to check if the program is correct, see for example
<http://primes.utm.edu/howmany.shtml>).

This program runs instantaneously when the input is 100; we can make that number bigger and bigger and eventually it will take a while to compute. So this is a small example of a program that would be useful to run in background (and, eventually, on a worker node, not blackjack itself).

### input redirect

Our very simple `prime_ex` program does not take much in input, just one int, but some programs take very large input sets, so that it is inconvenient to type them all on the command line. You can put your inputs in a file and then redirect the file to the program as input.

To create an input file you can use an editor (see the section on Linux/UNIX editors) or you can just do:\

``` {.bash}
echo "1000000" > infile
```

and it will create a textfile called `infile` with just the given echoed characters in it.  With this input value (10000000), it should take about 2 min to finish; if you want it to run faster, use a smaller number.


Then you can use this file as input redirect to the program

``` {.bash}
./a.out < infile
```


### output redirect

You will notice that when we run with input redirect, it still writes the output to the screen. We can make that output go to a file instead with an output redirect.

``` {.bash}
./a.out < infile &> outfile
```

This will run, but you will notice it ties up the terminal while the job is executing; you can't type commands.  If you want the job to run, and release the terminal, also put the job in background.

### putting job in background

Here is an example syntax.

``` {.bash}
./a.out < infile &> outfile &
```

Then you can use the `more` command to check the contents of the output file.

## PBS script

Following the same example, we had the program in a folder beneath the home directory called testfolder. Copy a simple PBS script into that folder:

``` {.console}
mooreb@blackjack:~> cp /home/mooreb/examples/simple/simple.pbs testprogram/
mooreb@blackjack:~> cd testprogram
mooreb@blackjack:~/testprogram> ls
a.out  infile  outfile  prime_ex.c  simple.pbs
mooreb@blackjack:~/testprogram>
```

### Text editor

Now, open the file `simple.pbs` in a text editor. We chose the nano editor because it is simple, but you can use vi or emacs if you are familiar with those.

``` {.console}
mooreb@blackjack:~/testprogram> module load nano
mooreb@blackjack:~/testprogram> nano simple.pbs
```

![screen shot of nano editor running in a terminal, viewing file simple.pbs](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/cluster/nanocap01.png)


At this point you can move the cursor around with the arrow keys and type text or delete characters, etc. Use Ctrl-G for help and Ctrl-X to exit; it will prompt you to save if you have any changes.


Why did we not redirect the output to a file? The scheduler will automatically produce an output file, so we don’t need to re-direct the output. We could, but for such a small job, here there is no need.

## Submitting the PBS script

Now, if we are in the folder that has the submit script, and the program, and the input file, we use the `qsub` command to submit the job.

``` {.console}
mooreb@blackjack:~/testprogram> qsub simple.pbs
3003085.bigjack
```

It responds with a number, which is our job number.

### Tracking the running job

Assuming the job does not finish immediately, we can use some helper
commands to check on the status. The `qstat` command will show our
currently running (or recently ended) jobs.

``` {.console}
mooreb@blackjack:~/testprogram> qstat
Job ID                    Name             User            Time Use S Queue
------------------------- ---------------- --------------- -------- - -----
3003085.bigjack            simple.pbs       mooreb                 0 R debug    
```

This does not seem to tell us much, but the most important thing to note
is the “`R`” underneath the `S` (for status), which means the job is
running. Other commonly seen codes are `C` (Complete), `E` (error) or
`Q` (queued).

To see all the jobs running in the scheduler you can use `showq`:

``` {.console}
mooreb@blackjack:~/testprogram> showq

active jobs------------------------
JOBID              USERNAME      STATE PROCS   REMAINING            STARTTIME

3003085              mooreb    Running     1    00:29:53  Tue May  9 09:02:37
3003081             propsta    Running    36     1:03:45  Mon May  8 17:06:29
3003001             propsta    Running    36     1:51:09  Wed May  3 10:53:53
3002973            bahramib    Running    12     6:53:42  Tue May  2 15:56:26
[...]
```

Here we see our job running, having been listed as asking for one
process (the default). Note the time remaining, just less than 30
minutes; that is because the max walltime allowable in the debug queue
is 30 minutes. Since we did not indicate a walltime request in the PBS
script, it used the default, one hour, and that was then modified to the
maximum for this queue.

### After the job is finished

Now, if all goes well and the job finishes, you should have two new
files in your folder:

``` {.console}
mooreb@blackjack:~/testprogram> ls
a.out   prime_ex.c  simple.pbs~          simple.pbs.o3003085
infile  simple.pbs  simple.pbs.e3003085
```

The two new files have the name of the script, appended by the job
number preceded either by the character `o` (for output) or `e` for
error.

Use `more` to look inside the output file.

``` {.console}
mooreb@blackjack:~/testprogram> more simple.pbs.o3003085
Enter nmax:
Input nmax = 1000000
The number of primes less than or equal to 1000000 is 78498
```
