# Example SBATCH Scripts

### SBATCH Hello World
This example uses the "Bash" shell to print a simple "Hello World" message. Note that it specifies the shell with the `#!/bin/bash` shebang line at the top of the script. Since this script uses built-in Bash commands, no software modules are loaded. That will be introduced in the next SBATCH script.

```bash
#!/bin/bash
## Introduction for writing an SBATCH script
## The next lines specify what resources you are requesting.
## Starting with 1 node, 8 processors per node, and 2 hours of walltime.
## Setup your sbatch flags
#SBATCH --time=2:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --job-name=my_job
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --output=my_job.o%j
#SBATCH --error=my_job.e%j
## All other instructions to SLURM are here as well and are preceded by a single #. Note that a single # can also precede normal comments
## Change to the directory the SBATCH script was submitted from
cd $SLURM_SUBMIT_DIR
## Print out a hello message indicating the host this is running on
export THIS_HOST=$(hostname)
echo Hello World from host $THIS_HOST
```

Note that the `--ntasks-per-node` value must always be less than or equal to the number of physical cores available on each node of the system on which you are running and is machine-specific. We recommend you always request the maximum number of processors per node to avoid multiple jobs on one node that have to share memory. For more information, see CARC systems information.

### Multi-processor example script

```bash
#!/bin/bash
## Introductory Example
## Copyright (c) 2018
## The Center for Advanced Research Computing
## at The University of New Mexico
####################################################
## Setup your sbatch flags
#SBATCH --time=2:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --job-name=my_job
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --output=my_job.o%j
#SBATCH --error=my_job.e%j
# load the environment module to use OpenMPI built with the Intel compilers
module load openmpi-3.1.1-intel-18.0.2-hlc45mq
# Change to the directory where the SBATCH script was submitted from
cd $SLURM_SUBMIT_DIR
# run the command "hostname" on every CPU. Hostname prints the name of the computer it is running on.
# $SLURM_NTASKS is the total number of CPUs requested. In this case 1 node x 8 CPUs per node = 8
mpirun -np $SLURM_NTASKS hostname
####################################################
```

### Multi-node example script

```bash
#!/bin/bash
## Introductory Example
## Copyright (c) 2018
## The Center for Advanced Research Computing
## at The University of New Mexico
####################################################
## Setup your sbatch flags
#SBATCH --time=2:00:00
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=8
#SBATCH --job-name=my_job
#SBATCH --mail-user=myemailaddress@unm.edu
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --output=my_job.o%j
#SBATCH --error=my_job.e%j
# Change to the directory the SBATCH script was submitted from
cd $SLURM_SUBMIT_DIR
# load the environment module to use OpenMPI built with the Intel compilers
module load openmpi-3.1.1-intel-18.0.2-hlc45mq
# print out a hello message from each of the processors on this host
# run the command "hostname" on every CPU. Hostname prints the name of the computer it is running on.
# $SLURM_NTASKS is the total number of CPUs requested. In this case 4 nodes x 8 CPUs per node = 32
# $SLURM_JOB_NODELIST contains the names of the nodes we were assigned.
mpirun -np $SLURM_NTASKS hostname
###################################################
```

*This quickbyte was validated on 5/26/2026*
