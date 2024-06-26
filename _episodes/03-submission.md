---
title: "Cirrus scheduler: Slurm"
teaching: 30
exercises: 20
questions:
- "How do I write job submission scripts?"
- "How do I control jobs?"
- "How do I find out what resources are available?"
objectives:
- "Understand the use of the basic Slurm commands."
- "Know what components make up and Cirrus scheduler."
- "Know where to look for further help on the scheduler."
keypoints:
- "Cirrus uses the Slurm scheduler."
- "`srun` is used to launch parallel executables in batch job submission scripts."
- "There are a number of different partitions (queues) available."
---

Cirrus uses the Slurm job submission system, or *scheduler*, to manage resources and how they are made
available to users. The main commands you will use with Slurm on Cirrus are:

* `sinfo`: Query the current state of nodes
* `sbatch`: Submit non-interactive (batch) jobs to the scheduler
* `squeue`: List jobs in the queue
* `scancel`: Cancel a job
* `srun`: Used within a batch job script or interactive job session to start a parallel program

Full documentation on Slurm on Cirrus can be found in the [Running Jobs on Cirrus](https://docs.cirrus.ac.uk/user-guide/batch/) section of the Cirrus documentations.

## Finding out what resources are available: `sinfo`

The `sinfo` command shows the current state of the compute nodes known to the scheduler:

```
auser@cirrus-login2:~> sinfo
```
{: .language-bash}
```
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
standard     up   infinite      1  drain r1i1n9
standard     up   infinite     19   resv r1i0n[23-26,29,35],r1i2n[10-11,15-16],r1i5n[22-24,27-29],r2i1n[19-20,35]
standard     up   infinite     51    mix r1i0n[8-9,19-20,33],r1i1n[12,26-27],r1i3n[1,7,28],r1i4n[16,27,31,35],r1i5n[0,2,9,18,25,31,33],r1i6n[0,24,31],r1i7n[3,14-15,20,31,33],r2i0n[5-6,14,19,28],r2i1n[14,22,25,27-29,32],r2i2n[0,2,10-11,18-19,27-28]
standard     up   infinite    295  alloc r1i0n[0-7,10-16,18,21-22,27-28,30-32,34],r1i1n[0-8,10-11,13-25,28-35],r1i2n[0-9,12-14,17-35],r1i3n[0,2-6,8-27,29-35],r1i4n[0-15,17-26,28-30,32-34],r1i5n[1,3-8,10-17,19-21,26,30,32,34-35],r1i6n[1-23,25-30,32-35],r1i7n[0-1,4-6,9-13,18-19,21-24,27-30,32],r2i0n[0-4,7-13,15-18,20-27,29-35],r2i1n[0-13,15-18,21,23-24,26,30-31,33-34],r2i2n[1,3,9,12,20-21,29-30]
standard     up   infinite      1   idle r1i7n2
standard     up   infinite      1   down r1i0n17
highmem      up   infinite      1  drain highmem01
gpu          up   infinite      2    mix r2i3n0,r2i5n0
gpu          up   infinite      1   resv r2i3n1
gpu          up   infinite     35  alloc r2i4n[0-8],r2i5n[1-8],r2i6n[0-8],r2i7n[0-8]
tds          up   infinite      1    mix r1i4n35
tds          up   infinite      3  alloc r1i4n[8,17,26]
gpu-tds      up   infinite      2  alloc r2i7n[7-8]
```
{: .output}

There is a row for each node state and partition combination. The default output shows the following columns:

* `PARTITION` - The system partition
* `AVAIL` - The status of the partition - `up` in normal operation
* `TIMELIMIT` - Maximum runtime as `days-hours:minutes:seconds`: on Cirrus, these are set using *QoS*
  (Quality of Service) rather than on partitions
* `NODES` - The number of nodes in the partition/state combination
* `STATE` - The state of the listed nodes (more information below)
* `NODELIST` - A list of the nodes in the partition/state combination

The nodes can be in many different states, the most common you will see are:

* `idle` - Nodes that are not currently allocated to jobs
* `alloc` - Nodes currently allocated to jobs
* `draining` - Nodes draining and will not run further jobs until released by the systems team
* `down` - Node unavailable
* `fail` - Node is in fail state and not available for jobs
* `reserved` - Node is in an advanced reservation and is not generally available
* `maint` - Node is in a maintenance reservation and is not generally available

If you prefer to see the state of individual nodes, you can use the `sinfo -N -l` command.

> ## Lots to look at!
> Warning! The `sinfo -N -l` command will produce a lot of output as there are 5860 individual 
> nodes on the current Cirrus system!
{: .callout}

```
auser@cirrus-login2:~> sinfo -N -l
```
{: .language-bash}
```
Mon Nov 29 19:02:37 2021
> > NodeLIST   NODES PARTITION       STATE CPUS    S:C:T MEMORY TMP_DISK WEIGHT AVAIL_FE REASON              
Tue Jun 25 13:34:59 2024
NODELIST   NODES PARTITION       STATE CPUS    S:C:T MEMORY TMP_DISK WEIGHT AVAIL_FE REASON
highmem01      1   highmem     drained 112    4:28:2 309556        0      1   (null) PGC Not in service
r1i0n0         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
r1i0n1         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
r1i0n2         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
r1i0n3         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
r1i0n4         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
r1i0n5         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
r1i0n6         1  standard   allocated 36     2:18:2 257400        0      1   (null) none
...lots of output trimmed...

```
{: .output}

> ## Explore a compute node
> Let’s look at the resources available on the compute nodes where your jobs will actually run. Try running this
> command to see the name, CPUs and memory available on the worker nodes (the instructors will give you the ID of
> the compute node to use):
> ```
> [auser@cirrus-login2:~> sinfo -n r1i0n0 -o "%n %c %m"
> ```
> {: .language-bash}
> This should display the resources available for a standard node. Are they what you expect given what we learnt
> earlier about the configuration of a compute node?
> > ## Solution
> > The output should show:
> > ```
> > HOSTNAMES CPUS MEMORY
> > r1i0n0 36 257400
> > ```
> > 
> > You may notice that the amount of memory available does not match the full capacity of the node. Slurm
> > outputs memory in GBs. Each node has 256 GiB of memory (which is ~274.8 GB). The node has less available
> > memory than this because some memory is used up with the OS and system processes.
> {: .solution}
{: .challenge}

## Using batch job submission scripts

### Header section: `#SBATCH`

As for most other scheduler systems, job submission scripts in Slurm consist of a header section with the
shell specification and options to the submission command (`sbatch` in this case) followed by the body of
the script that actually runs the commands you want. In the header section, options to `sbatch` should 
be prepended with `#SBATCH`.

Here is a simple example script that runs the `xthi` program (which shows process and thread placement) across
two nodes.

```
#!/bin/bash
#SBATCH --job-name=my_mpi_job
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=36
#SBATCH --cpus-per-task=1
#SBATCH --time=0:10:0
#SBATCH --account=ic084
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --exclusive

# Now load the "xthi" package
module load mpt
export PATH=$PATH:/work/z04/shared/jsindt/xthi/src

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

# srun to launch the executable
srun --hint=nomultithread --distribution=block:block xthi_mpi_mp
```
{: .language-bash}

The options shown here are:

* `--job-name=my_mpi_job` - Set the name for the job that will be displayed in Slurm output
* `--nodes=2` - Select two nodes for this job
* `--ntasks-per-node=36` - Set 128 parallel processes per node (usually corresponds to MPI ranks)
* `--cpus-per-task=1` - Number of cores to allocate per parallel process 
* `--time=0:10:0` - Set 10 minutes maximum walltime for this job
* `--account=ic084` - Charge the job to the `ic084` budget
* `--partition=standard` - Use nodes from the standard partition on Cirrus (the standard partition
  includes all compute nodes).
* `--qos=short` - Use the `short` Quality of Service (QoS). Different QoS define different limits
  for jobs. The short QoS allows small short jobs only but guarantees that they run quickly/straight away.
* `--exclusive` - Ensure that you are not sharing nodes with other users. This is always recommended, and is
  necessary for runs requiring more than one node.

> ## Partitions and QoS
> There are a small number of different partitions on Cirrus (covering different node types) and
> a wider variety of QoS (covering different use cases). You can find a list of the available 
> partitions and QoS in the [Cirrus User Guide](https://docs.cirrus.ac.uk/user-guide/batch/#resource-limits).
{: .callout}

We will discuss the `srun` command further below.

### Submitting jobs using `sbatch`

You use the `sbatch` command to submit job submission scripts to the scheduler. For example, if the
above script was saved in a file called `test_job.slurm`, you would submit it with:

```
auser@cirrus-login2:~> sbatch test_job.slurm
```
{: .language-bash}
```
Submitted batch job 23996
```
{: .output}

Slurm reports back with the job ID for the job you have submitted

### Checking progress of your job with `squeue`

You use the `squeue` command to show the current state of the queues on Cirrus. Without any options, it
will show all jobs in the queue:

```
auser@cirrus-login2:~> squeue
```
{: .language-bash}
```

  JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
5757684       gpu QML-CuTe jamesp-i PD       0:00      8 (Priority)
5757683       gpu QML-CuTe jamesp-i PD       0:00      8 (Priority)
5757682       gpu QML-CuTe jamesp-i PD       0:00      8 (Resources)
5761481       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761480       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761476       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761477       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761478       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761479       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761475       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761471       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761472       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761473       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
5761474       gpu     mdm2 akalpoka PD       0:00      1 (Priority)
...
```
{: .output}

You can show just your jobs with `squeue --me`.

### Cancelling jobs with `scancel`

You can use the `scancel` command to cancel jobs that are queued or running. When used on running jobs
it stops them immediately. You need to supply options to `scancel` to specify which jobs to kill.
To kill a particular job you supply the job ID, e.g. to kill the job with ID `12345`:

```
scancel 12345
```
{: .language-bash}

To kill all your queued jobs (but not running ones):

```
scancel --user=$USER --state=PENDING
```
{: .language-bash}

### Running parallel applications using `srun`

Once past the header section your script consists of standard shell commands required to run your
job. These can be simple or complex depending on how you run your jobs but even the simplest job
script usually contains commands to:

* Load the required software modules
* Add the path to the `xthi` executable to your `$PATH` variable
* Set appropriate environment variables (you should always set `OMP_NUM_THREADS`, even if you are
  not using OpenMP you should set this to `1`)

After this you will usually launch your parallel program using the `srun` command. At its simplest,
`srun` only needs 2 arguments to specify the correct binding of processes to cores (it will use the
values supplied to `sbatch` to work out how many parallel processes to launch). In the example above,
our `srun` command simply looks like:

```
srun --hint=nomultithread --distribution=block:block xthi
```
{: .language-bash}

### STDOUT/STDERR from jobs

STDOUT and STDERR from jobs are, by default, written to a file called `slurm-<jobid>.out` in the
working directory for the job (unless the job script changes this, this will be the directory
where you submitted the job). So for a job with ID `12345` STDOUT and STDERR would be in
`slurm-12345.out`.

If you run into issues with your jobs, the Service Desk will often ask you to send your job
submission script and the contents of this file to help debug the issue.

If you need to change the location of STDOUT and STDERR you can use the `--output=<filename>`
and the `--error=<filename>` options to `sbatch` to split the streams and output to the named
locations.

> ## Is the output what you expect?
>
> Locate and examine the STDOUT from the job you ran above. Does it indicate that each 
> MPI process is bound to a different physical core as expected?
>
> > ## Solution
> > The output should look something like the below showing that each of the 72 MPI processes
> > is bound to a separate physical core.
> >
> > ```
> > Node summary for    2 nodes:
> > Node    0, hostname r1i0n23, mpi  36, omp   1, executable xthi_mpi_mp
> > Node    1, hostname r1i0n24, mpi  36, omp   1, executable xthi_mpi_mp
> > MPI summary: 72 ranks
> > Node    0, rank    0, thread   0, (affinity =    0)
> > Node    0, rank    1, thread   0, (affinity =    1)
> > Node    0, rank    2, thread   0, (affinity =    2)
> > Node    0, rank    3, thread   0, (affinity =    3)
> > Node    0, rank    4, thread   0, (affinity =    4)
> > Node    0, rank    5, thread   0, (affinity =    5)
> > Node    0, rank    6, thread   0, (affinity =    6)
> > Node    0, rank    7, thread   0, (affinity =    7)
> > Node    0, rank    8, thread   0, (affinity =    8)
> > Node    0, rank    9, thread   0, (affinity =    9)
> > Node    0, rank   10, thread   0, (affinity =   10)
> > Node    0, rank   11, thread   0, (affinity =   11)
> > Node    0, rank   12, thread   0, (affinity =   12)
> > Node    0, rank   13, thread   0, (affinity =   13)
> > Node    0, rank   14, thread   0, (affinity =   14)
> > Node    0, rank   15, thread   0, (affinity =   15)
> > Node    0, rank   16, thread   0, (affinity =   16)
> > Node    0, rank   17, thread   0, (affinity =   17)
> > Node    0, rank   18, thread   0, (affinity =   18)
> > Node    0, rank   19, thread   0, (affinity =   19)
> > Node    0, rank   20, thread   0, (affinity =   20)
> > Node    0, rank   21, thread   0, (affinity =   21)
> > Node    0, rank   22, thread   0, (affinity =   22)
> > Node    0, rank   23, thread   0, (affinity =   23)
> > Node    0, rank   24, thread   0, (affinity =   24)
> > Node    0, rank   25, thread   0, (affinity =   25)
> > Node    0, rank   26, thread   0, (affinity =   26)
> > Node    0, rank   27, thread   0, (affinity =   27)
> > Node    0, rank   28, thread   0, (affinity =   28)
> > Node    0, rank   29, thread   0, (affinity =   29)
> > Node    0, rank   30, thread   0, (affinity =   30)
> > Node    0, rank   31, thread   0, (affinity =   31)
> > Node    0, rank   32, thread   0, (affinity =   32)
> > Node    0, rank   33, thread   0, (affinity =   33)
> > Node    0, rank   34, thread   0, (affinity =   34)
> > Node    0, rank   35, thread   0, (affinity =   35)
> > Node    1, rank   36, thread   0, (affinity =    0)
> > Node    1, rank   37, thread   0, (affinity =    1)
> > Node    1, rank   38, thread   0, (affinity =    2)
> > Node    1, rank   39, thread   0, (affinity =    3)
> > Node    1, rank   40, thread   0, (affinity =    4)
> > Node    1, rank   41, thread   0, (affinity =    5)
> > Node    1, rank   42, thread   0, (affinity =    6)
> > Node    1, rank   43, thread   0, (affinity =    7)
> > Node    1, rank   44, thread   0, (affinity =    8)
> > Node    1, rank   45, thread   0, (affinity =    9)
> > Node    1, rank   46, thread   0, (affinity =   10)
> > Node    1, rank   47, thread   0, (affinity =   11)
> > Node    1, rank   48, thread   0, (affinity =   12)
> > Node    1, rank   49, thread   0, (affinity =   13)
> > Node    1, rank   50, thread   0, (affinity =   14)
> > Node    1, rank   51, thread   0, (affinity =   15)
> > Node    1, rank   52, thread   0, (affinity =   16)
> > Node    1, rank   53, thread   0, (affinity =   17)
> > Node    1, rank   54, thread   0, (affinity =   18)
> > Node    1, rank   55, thread   0, (affinity =   19)
> > Node    1, rank   56, thread   0, (affinity =   20)
> > Node    1, rank   57, thread   0, (affinity =   21)
> > Node    1, rank   58, thread   0, (affinity =   22)
> > Node    1, rank   59, thread   0, (affinity =   23)
> > Node    1, rank   60, thread   0, (affinity =   24)
> > Node    1, rank   61, thread   0, (affinity =   25)
> > Node    1, rank   62, thread   0, (affinity =   26)
> > Node    1, rank   63, thread   0, (affinity =   27)
> > Node    1, rank   64, thread   0, (affinity =   28)
> > Node    1, rank   65, thread   0, (affinity =   29)
> > Node    1, rank   66, thread   0, (affinity =   30)
> > Node    1, rank   67, thread   0, (affinity =   31)
> > Node    1, rank   68, thread   0, (affinity =   32)
> > Node    1, rank   69, thread   0, (affinity =   33)
> > Node    1, rank   70, thread   0, (affinity =   34)
> > Node    1, rank   71, thread   0, (affinity =   35)
> > ```
> >
> {: .solution}
{: .challenge}

### Submitting jobs to GPU nodes



### Interactive jobs: direct `srun`

Similar to the batch jobs covered above, users can also run interactive jobs using the `srun`
command directly. `srun` used in this way takes the same arguments as `sbatch` but, obviously, these are
specified on the command line rather than in a job submission script. As for `srun` within
a batch job, you should also provide the name of the executable you want to run.

For example, to execute `xthi` across all cores on two nodes (1 MPI process per core and no
OpenMP threading) within an interactive job you would issue the following commands:

```
auser@cirrus-login2:~> export OMP_NUM_THREADS=1
auser@cirrus-login2:~> module load mpt
auser@cirrus-login2:~> export PATH=$PATH:/work/z04/shared/jsindt/xthi/src
auser@cirrus-login2:~> srun --partition=standard --qos=short --nodes=2 --ntasks-per-node=36 --cpus-per-task=1 --time=0:10:0 --account=ic084 --exclusive xthi_mpi_mp
```
{: .language-bash}
```
Node summary for    2 nodes:
Node    0, hostname r1i0n23, mpi  36, omp   1, executable xthi_mpi_mp
Node    1, hostname r1i0n24, mpi  36, omp   1, executable xthi_mpi_mp
MPI summary: 72 ranks
Node    0, rank    0, thread   0, (affinity = 0,36)
Node    0, rank    1, thread   0, (affinity = 18,54)
Node    0, rank    2, thread   0, (affinity = 1,37)
Node    0, rank    3, thread   0, (affinity = 19,55)
Node    0, rank    4, thread   0, (affinity = 2,38)
Node    0, rank    5, thread   0, (affinity = 20,56)
Node    0, rank    6, thread   0, (affinity = 3,39)
Node    0, rank    7, thread   0, (affinity = 21,57)
Node    0, rank    8, thread   0, (affinity = 4,40)
Node    0, rank    9, thread   0, (affinity = 22,58)
Node    0, rank   10, thread   0, (affinity = 5,41)
Node    0, rank   11, thread   0, (affinity = 23,59)
Node    0, rank   12, thread   0, (affinity = 6,42)
Node    0, rank   13, thread   0, (affinity = 24,60)
...long output trimmed...
```
{: .output}

## Other useful information

In this section we briefly introduce other scheduler topics that may be useful to users. We
provide links to more information on these areas for people who may want to explore these 
areas more. 

### Hybrid MPI and OpenMP jobs

When running hybrid MPI (with multiple processes) and OpenMP
(with multiple threads) jobs you need to leave free cores between the parallel processes launched
using `srun` for the multiple OpenMP threads that will be associated with each MPI process.

As we saw above, you can use the options to `sbatch` to control how many parallel processes are
placed on each compute node and the `--cpus-per-task` option to set the stride
between parallel processes. The `--cpus-per-task` option is also used to accommodate the OpenMP
threads that are launched for each MPI process - the value
for `--cpus-per-task` should usually be the same as that for `OMP_NUM_THREADS`. To ensure
you get the correct thread pinning, you also need to specify an additional OpenMP environment
variable. Specifically:

   - Set the `OMP_PLACES` environment variable to `cores` with `export OMP_PLACES=cores` in
     your job submission script

As an example, consider the job script below that runs across 4 nodes with 8 MPI processes
per node and 16 OpenMP threads per MPI process (so all 128 physical cores on 4 nodes are used,
512 physical cores in total).

```
#!/bin/bash
#SBATCH --job-name=my_mpi_job
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=9
#SBATCH --cpus-per-task=4
#SBATCH --time=0:10:0
#SBATCH --account=ic084
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --exclusive

# Now load the "xthi" package
module load mpt
export PATH=$PATH:/work/z04/shared/jsindt/xthi/src

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

# srun to launch the executable
srun --hint=nomultithread --distribution=block:block xthi_mpi_mp
```
{: .language-bash}

If you submit this job script, you should see output that looks like:

```
Node summary for    2 nodes:
Node    0, hostname r1i0n23, mpi   9, omp   4, executable xthi_mpi_mp
Node    1, hostname r1i0n24, mpi   9, omp   4, executable xthi_mpi_mp
MPI summary: 18 ranks
Node    0, rank    0, thread   0, (affinity =  0-3)
Node    0, rank    0, thread   1, (affinity =  0-3)
Node    0, rank    0, thread   2, (affinity =  0-3)
Node    0, rank    0, thread   3, (affinity =  0-3)
Node    0, rank    1, thread   0, (affinity =  4-7)
Node    0, rank    1, thread   1, (affinity =  4-7)
Node    0, rank    1, thread   2, (affinity =  4-7)
Node    0, rank    1, thread   3, (affinity =  4-7)
Node    0, rank    2, thread   0, (affinity = 8-11)
Node    0, rank    2, thread   1, (affinity = 8-11)
Node    0, rank    2, thread   2, (affinity = 8-11)
Node    0, rank    2, thread   3, (affinity = 8-11)
Node    0, rank    3, thread   0, (affinity = 12-15)
Node    0, rank    3, thread   1, (affinity = 12-15)
Node    0, rank    3, thread   2, (affinity = 12-15)
Node    0, rank    3, thread   3, (affinity = 12-15)
Node    0, rank    4, thread   0, (affinity = 16-19)
Node    0, rank    4, thread   1, (affinity = 16-19)
Node    0, rank    4, thread   2, (affinity = 16-19)
Node    0, rank    4, thread   3, (affinity = 16-19)
Node    0, rank    5, thread   0, (affinity = 20-23)
Node    0, rank    5, thread   1, (affinity = 20-23)
Node    0, rank    5, thread   2, (affinity = 20-23)
Node    0, rank    5, thread   3, (affinity = 20-23)
Node    0, rank    6, thread   0, (affinity = 24-27)
Node    0, rank    6, thread   1, (affinity = 24-27)
Node    0, rank    6, thread   2, (affinity = 24-27)
Node    0, rank    6, thread   3, (affinity = 24-27)
Node    0, rank    7, thread   0, (affinity = 28-31)
Node    0, rank    7, thread   1, (affinity = 28-31)
Node    0, rank    7, thread   2, (affinity = 28-31)
Node    0, rank    7, thread   3, (affinity = 28-31)
Node    0, rank    8, thread   0, (affinity = 32-35)
Node    0, rank    8, thread   1, (affinity = 32-35)
Node    0, rank    8, thread   2, (affinity = 32-35)
Node    0, rank    8, thread   3, (affinity = 32-35)
Node    1, rank    9, thread   0, (affinity =  0-3)
Node    1, rank    9, thread   1, (affinity =  0-3)
Node    1, rank    9, thread   2, (affinity =  0-3)
Node    1, rank    9, thread   3, (affinity =  0-3)
Node    1, rank   10, thread   0, (affinity =  4-7)
Node    1, rank   10, thread   1, (affinity =  4-7)
Node    1, rank   10, thread   2, (affinity =  4-7)
Node    1, rank   10, thread   3, (affinity =  4-7)
Node    1, rank   11, thread   0, (affinity = 8-11)
Node    1, rank   11, thread   1, (affinity = 8-11)
Node    1, rank   11, thread   2, (affinity = 8-11)
Node    1, rank   11, thread   3, (affinity = 8-11)
Node    1, rank   12, thread   0, (affinity = 12-15)
Node    1, rank   12, thread   1, (affinity = 12-15)
Node    1, rank   12, thread   2, (affinity = 12-15)
Node    1, rank   12, thread   3, (affinity = 12-15)
Node    1, rank   13, thread   0, (affinity = 16-19)
Node    1, rank   13, thread   1, (affinity = 16-19)
Node    1, rank   13, thread   2, (affinity = 16-19)
Node    1, rank   13, thread   3, (affinity = 16-19)
Node    1, rank   14, thread   0, (affinity = 20-23)
Node    1, rank   14, thread   1, (affinity = 20-23)
Node    1, rank   14, thread   2, (affinity = 20-23)
Node    1, rank   14, thread   3, (affinity = 20-23)
Node    1, rank   15, thread   0, (affinity = 24-27)
Node    1, rank   15, thread   1, (affinity = 24-27)
Node    1, rank   15, thread   2, (affinity = 24-27)
Node    1, rank   15, thread   3, (affinity = 24-27)
Node    1, rank   16, thread   0, (affinity = 28-31)
Node    1, rank   16, thread   1, (affinity = 28-31)
Node    1, rank   16, thread   2, (affinity = 28-31)
Node    1, rank   16, thread   3, (affinity = 28-31)
Node    1, rank   17, thread   0, (affinity = 32-35)
Node    1, rank   17, thread   1, (affinity = 32-35)
Node    1, rank   17, thread   2, (affinity = 32-35)
Node    1, rank   17, thread   3, (affinity = 32-35)
```
{: .output}

Each Cirrus compute node is made up of 2 NUMA (*Non Uniform Memory Access*) regions (1 per socket)
with 18 cores in each region. Programs where the threads of a process span multiple NUMA regions
are likely to be *much less* efficient so we recommend using thread counts that fit well into the
Cirrus compute node layout. Effectively, this means one of the following options for hybrid jobs
on nodes where all cores are used:

*  2 MPI processes per node and 18 OpenMP threads per process: equivalent to 1 MPI processes per NUMA region
*  4 MPI processes per node and  9 OpenMP threads per process: equivalent to 2 MPI processes per NUMA region
*  6 MPI processes per node and  6 OpenMP threads per process: equivalent to 3 MPI processes per NUMA region
* 12 MPI processes per node and  3 OpenMP threads per process: equivalent to 6 MPI processes per NUMA region
* 18 MPI processes per node and  2 OpenMP threads per process: equivalent to 9 MPI processes per NUMA region

<!-- Need to add information on the solid state storage and Slurm once it is in place

### Using the Cirrus solid state storage

-->


{% include links.md %}


