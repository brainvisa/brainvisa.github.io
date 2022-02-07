# Using a calculation center

## TGCC (CEA calculation center)

### Importing BrainVisa / Casa-distro virtual images

We use singularity images.

The TGCC is using [pcocc](https://github.com/cea-hpc/pcocc).

Pcocc can import from docker exports. Thus we have con convert singularity -> docker -> `.tar` export -> pcocc image:

Thus docker should be installed on the system which performs conversion
Casa-distro has a docker export function. We can run it from your development computer:

    casa_distro_admin convert_image source=brainvisa-5.0.4.sif
    docker export -o brainvisa-5.0.4.tar brainvisa:5.0.4
    scp brainvisa-5.0.4.tar tgcc:/ccc/workflash/cont003/n4h00003/gaston/

(do not use your $HOME directory on the TGCC side, the quota there is too small to contain images.)

Then log in the TGCC (via ssh), and in a shell there:

    cd /ccc/workflash/cont003/n4h00003/gaston  # this dir is just an example
    # n4h00003 is the project identifier I am in: use your own instead
    pcocc image import docker-archive:brainvisa-5.0.4.tar n4h00003:brainvisa-5.0.4
    
Then the image should be in pcocc:

    pcocc image list
    
should show it.

### Configuring MPI for casa-distro images

Brainvisa/Soma-workflow can run using the MPI scheduler mode in order to run a workflow inside a single parallel job. So we must grant access to MPI inside the container.

pcocc called with the correct options will mount the MPI library directory, but we must setup the PATHS in the image. We use the hook in casa-distro images which sources `/casa/start_scripts/init.sh` if it is present

    mkdir $CCC_WORKDIR/casa_ini_scripts
    cat << EOF > $CCC_WORKDIR/casa_ini_scripts/init.sh
    export MPI_LIBDIR=/ccc/products/openmpi-4.0.5/intel--20.0.0/default/lib
    export LD_LIBRARY_PATH=$MPI_LIBDIR:"$LD_LIBRARY_PATH"
    export BRIDGE_MPRUN_PRELOAD=${BRIDGE_MPRUN_PRELOAD}:${MPI_LIBDIR}/libmpi.so  # probably not needed ?
    export LD_PRELOAD=${MPI_LIBDIR}/libmpi.so
    EOF
    
then submissions script must call (adapt -n to the number of CPUs to reserve):

    #!/bin/bash
    
    #MSUB -q v100l
    #MSUB -T 600
    #MSUB -Q test
    #MSUB -A n4h00003
    #MSUB -m scratch,work,store,workflash
    #MSUB -n 40
    #MSUB -c 1
    #MSUB -o /ccc/workflash/cont003/n4h00003/gaston/hcp_reco_cnn/wf_out_%I.txt
    #MSUB -e /ccc/workflash/cont003/n4h00003/gaston/hcp_reco_cnn/wf_err_%I.txt

    pcocc run -n 40 -c 1 -M openmpi-4.0.5 --mount /ccc/workflash/cont003/n4h00003/gaston/hcp_reco_cnn/start_scripts:/casa/start_scripts -I brainvisa-5.0.4 python -m soma_workflow.MPI_workflow_runner irene_MPI --workflow /ccc/workflash/cont003/n4h00003/gaston/large_pipeline.workflow

In the example here, we run the command `python -m soma_workflow.MPI_workflow_runner` inside the container, in MPI mode (as many instances of the program will be run as requested tasks specified with the `-n` option)
This ommand will run a [soma-workflow](https://populse.github.io/soma-workflow) workflow file in the job, using the MPI scheduler mode, using the configured resource `irene_MPI`.

### Configuring soma-workflow for MPI mode

The above command will not run successfully before the `irene_MPI` computing resource is configured in Soma-Workflow. On the TGCC login / submission machine, you can use a `$HOME/.soma-workflow.cfg` looking like this one:

    [irene_MPI]
    DATABASE_FILE        = /ccc/cont003/home/neurospi/gaston/.soma-workflow/irene_mpi/soma_workflow.db
    TRANSFERED_FILES_DIR = /ccc/cont003/home/neurospi/gaston/.soma-workflow/irene_mpi/transfered_files
    SCHEDULER_TYPE       = mpi

    # optional logging
    SERVER_LOG_FILE   = /ccc/cont003/home/neurospi/gaston/.soma-workflow/irene_mpi/logs/log_server
    SERVER_LOG_FORMAT = %(asctime)s => line %(lineno)s: %(message)s
    SERVER_LOG_LEVEL  = ERROR
    ENGINE_LOG_DIR    = path/logs/
    ENGINE_LOG_FORMAT = %(asctime)s => line %(lineno)s: %(message)s
    ENGINE_LOG_LEVEL  = ERROR

The really important thing here is the declared resource name (you can use any name, but pass the right one to the job command), and the `SCHEDULER_TYPE = mpi` which tells soma-workflow that it should use the [MPI scheduler mode](https://populse.github.io/soma-workflow/MPI_workflow_runner.html).

### BrainVisa workflows

### Monitoring workflows execution

