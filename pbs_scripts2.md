# Example Slurm Scripts

### Slurm Hello World:

This example uses the Bash shell to print a simple “Hello World” message.
In Slurm, the shell is specified by the shebang line at the top of the script ```#!/bin/bash```
If you do not specify a shell, then your default shell will be used.
Since this script uses only built-in Bash commands, no software modules are loaded. 
Module usage will be introduced in the next Slurm example.

```bash
#!/bin/bash
## Introduction for writing a Slurm script
## The next lines specify what resources you are requesting.
## Starting with 1 node, 8 processors per node, and 2 hours of walltime. 
## Setup your slurm flags
#SBATCH --job-name=my_job
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --time=02:00:00
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL

## Change to directory the Slurm script was submitted from
cd $SLURM_SUBMIT_DIR
## Print out a hello message indicating the host this is running on
export THIS_HOST=$(hostname)
echo "Hello World from host $THIS_HOST"
####################################################
```

Note that the number of tasks you request per node in Slurm, specified with
```--ntasks-per-node```, must always be less than or equal to the number of 
physical CPU cores available on the node where your job will run. 
This value is machine-specific. For example, on
Ealey, ```--ntasks-per-node``` should be <=64, however, we recommend you always request
the maximum number of processors per node to avoid multiple jobs on one
node that have to share memory. For more information see CARC systems
information.

### Multi-processor example script:

```bash
#!/bin/bash
## Introductory Example
## Copyright (c) 2018
## The Center for Advanced Research Computing
## at The University of New Mexico
####################################################
## Setup your Slurm flags
#SBATCH --job-name=my_job
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --time=02:00:00
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
# load the environment module to use OpenMPI
module load openmpi 
# Change to the directory where the Slurm script was submitted from
cd $SLURM_SUBMIT_DIR
# run the command "hostname" on ever CPU. Hostname prints the name of the computer is it running on.
# $SLURM_NTASKS is the total number of CPUs requested. In this case 1 nodes x 8 CPUS per node = 8
mpirun -np $SLURM_NTASKS hostname
####################################################
```

### Multi-node example script:

```bash
#!/bin/bash
## Introductory Example 
## Copyright (c) 2018
## The Center for Advanced Research Computing
## at The University of New Mexico
####################################################
## Setup your Slurm flags
#SBATCH --job-name=my_job
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=8
#SBATCH --time=02:00:00
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
# Change to directory the Slurm script was submitted from
cd $SLURM_O_WORKDIR
# load the environment module to use OpenMPI
module load openmpi
## Print a hello message from each of the processors on all assigned nodes
## $SLURM_NTASKS is the total number of MPI tasks: 4 nodes x 8 tasks per node = 32
mpirun -np $SLURM_NTASKS hostname
###################################################
```
