# Example SBATCH Scripts

### SBATCH Hello World
This example uses the "Bash" shell to print a simple "Hello World" message. Since this script uses built-in Bash commands, no software modules are loaded. That will be introduced in the next SBATCH script.

```bash
#!/bin/bash
## Introduction for writing an SBATCH script
## The next lines specify what resources you are requesting.
## Starting with 1 node, 8 processors per node, and 5 minutes of walltime.
## Setup your sbatch flags
#SBATCH --time=00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --job-name=hello
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --output=hello.o%j
#SBATCH --error=hello.e%j
## All other instructions to SLURM are here as well and are preceded by a single #. Note that a single # can also precede normal comments
## Change to the directory the SBATCH script was submitted from
cd $SLURM_SUBMIT_DIR
## Print out a hello message indicating the host this is running on
echo "Hello World from host $(hostname)."
```

Note that the `--ntasks-per-node` value must always be less than or equal to the number of physical cores available on each node of the system on which you are running and is machine-specific. We recommend that you always request the maximum number of processors per node to avoid multiple jobs on a single node that shares memory. For more information, please see the CARC systems information.

### Multi-processor example script
This example builds on the previous script by loading an OpenMPI software module and using `mpirun` to run the job across all requested processors.

```bash
#!/bin/bash
## Introductory Example
## Copyright (c) 2018
## The Center for Advanced Research Computing
## at The University of New Mexico
####################################################
## Setup your sbatch flags
#SBATCH --time=00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --job-name=helloworld_parallel
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --output=helloworld_parallel.o%j
#SBATCH --error=helloworld_parallel.e%j
# load the environment module to use OpenMPI built with the Intel compilers
module load openmpi-3.1.1-intel-18.0.2-hlc45mq
# Change to the directory where the SBATCH script was submitted from
cd $SLURM_SUBMIT_DIR
# run the echo command on every CPU. Hostname prints the name of the computer it is running on.
# $SLURM_NTASKS is the total number of CPUs requested. In this case 1 node x 8 CPUs per node = 8
mpirun -np $SLURM_NTASKS echo "Hello World from host $(hostname)"
####################################################
```

### Multi-node example script
This example extends the previous script to run across multiple nodes.

```bash
#!/bin/bash
## Introductory Example
## Copyright (c) 2018
## The Center for Advanced Research Computing
## at The University of New Mexico
####################################################
## Setup your sbatch flags
#SBATCH --time=00:05:00
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=8
#SBATCH --job-name=helloworld_parallel
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --output=helloworld_parallel.o%j
#SBATCH --error=helloworld_parallel.e%j
# Change to the directory the SBATCH script was submitted from
cd $SLURM_SUBMIT_DIR
# load the environment module to use OpenMPI built with the Intel compilers
module load openmpi-3.1.1-intel-18.0.2-hlc45mq
# run the echo command on every CPU across all nodes.
# $SLURM_NTASKS is the total number of CPUs requested. In this case 4 nodes x 8 CPUs per node = 32
# $SLURM_JOB_NODELIST contains the names of the nodes we were assigned.
mpirun -np $SLURM_NTASKS echo "Hello World from host $(hostname)"
###################################################
```

*This quickbyte was validated on 5/26/2026*
