---
Using a calculation center
---

TGCC (CEA calculation center)
-----------------------------

### Importing BrainVisa / Casa-distro virtual images

We use singularity images.

The TGCC is using [pcocc](https://github.com/cea-hpc/pcocc).

Pcocc can import from docker exports. Thus we have con convert singularity -> docker -> `.tar` export -> pcocc image:

Thus docker should be installed on the system which performs conversion
Casa-distro has a docker export function. We can run it from your development computer:

    casa_distro_admin converrt_image source=brainvisa-5.0.4.sif
    docker export -o brainvisa-5.0.4.tar brainvisa:5.0.4
    scp brainvisa-5.0.4.tar tgcc:/ccc/workflash/cont003/n4h00003/gaston/

Then log in the TGCC (via ssh), and in a shell there:

    cd docker-archive:brainvisa-cea-5.0.4.tar n4h00003:brainvisa-cea-5.0.4  # this dir is just an example
    # n4h00003 is the project identifier I am in
    pcocc image import docker-archive:brainvisa-5.0.4.tar n4h00003:brainvisa-5.0.4

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

In the example here, we run the command `python -m soma_workflow.MPI_workflow_runner` inside the container, in MPI mode (as amny instances of the program will be run as requested tasks specified with the `-n` option)
This ommand will run a [soma-workflow](https://github.com/populse/soma-workflow) workflow file in the job, using the MPI scheduler mode, using the configured resource `irene_MPI`.

### Configuring soma-workflow for MPI mode

### BrainVisa workflows

### Monitoring workflows execution

