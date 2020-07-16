How to launch TractoFlow
========================

Local computer
--------------

To run the pipeline, use the following command:

::

    # With Singularity
    $> nextflow run tractoflow/main.nf --bids input_bids --dti_shells "DTI_SHELLS" --fodf_shells "FODF_SHELLS" -with-singularity singularity_name.img -resume
    # Or
    $> nextflow run tractoflow/main.nf --root input_folder --dti_shells "DTI_SHELLS" --fodf_shells "FODF_SHELLS" -with-singularity singularity_name.img -resume

    # With Docker
    $> nextflow run tractoflow/main.nf --bids input_bids --dti_shells "DTI_SHELLS" --fodf_shells "FODF_SHELLS" -with-docker scilus/docker-tractoflow:2.1.1 -resume
    # Or
    $> nextflow run tractoflow/main.nf --root input_folder --dti_shells "DTI_SHELLS" --fodf_shells "FODF_SHELLS" -with-docker scilus/docker-tractoflow:2.1.1 -resume

Where ``DTI_SHELLS`` are the shells used to compute the DTI metrics
(typically b-value < 1200 e.g. "0 1000") and ``FODF_SHELLS`` are the shells used
to compute the fODF metrics (typically b > 700 e.g. "0 1000 2000").

If you want to skip steps already processed by an anterior run, you can add `-resume` option in the command line.

High Performance Computer (HPC)
-------------------------------

The following example is based on the SLURM executor:

If you want to use only one node, please use the same commands presented for the
local computer. The follwing lines must be saved in ``.sh`` file (e.g. ``cmd.sh``)
to be executed with ``sbatch``.

::

    #!/bin/sh

    #SBATCH --nodes=1
    #SBATCH --cpus-per-task=32
    #SBATCH --mem=0
    #SBATCH --time=48:00:00

    nextflow -c singularity.conf run tractoflow/main.nf --root input_folder --dti_shells "DTI_SHELLS" --fodf_shells "FODF_SHELLS" -with-singularity singularity_name.img -resume

To launch on multiple nodes, you must to use the MPI option that use Ignite executor.
The following example use 2 nodes with 32 threads on each nodes. The follwing lines
must be saved in ``.sh`` file (e.g. ``cmd.sh``) to be executed with ``sbatch``.

::

    #!/bin/sh

    #SBATCH --nodes=2
    #SBATCH --cpus-per-task=32
    #SBATCH --mem=0
    #SBATCH --time=48:00:00

    export NXF_CLUSTER_SEED=$(shuf -i 0-16777216 -n 1)

    srun nextflow -c singularity.conf run tractoflow/main.nf --root input_folder --dti_shells "DTI_SHELLS" --fodf_shells "FODF_SHELLS" -with-singularity singularity_name.img -with-mpi -resume

To launch the pipeline on the HPC:

::

    $> sbatch cmd.sh
