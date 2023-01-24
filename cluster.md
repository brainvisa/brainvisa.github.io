# Using a calculation center

## TGCC (CEA calculation center)

### Importing BrainVisa / Casa-distro virtual images

We use singularity images.

The TGCC is using [pcocc](https://github.com/cea-hpc/pcocc).

Pcocc can import from docker exports. Thus we have con convert singularity -> docker -> `.tar` export -> pcocc image:

Thus docker should be installed on the system which performs conversion
Casa-distro has a docker export function. We can run it from your development computer:

    casa_distro_admin convert_image source=brainvisa-5.0.4.sif
    docker save -o brainvisa-5.0.4.tar brainvisa:5.0.4
    scp brainvisa-5.0.4.tar tgcc:/ccc/workflash/cont003/n4h00003/gaston/

(do not use your $HOME directory on the TGCC side, the quota there is too small to contain images.)

Then log in the TGCC (via ssh), and in a shell there:

  - to be done once, if needed: declare a PCOCC repository at a suitable location. The default images repository location is `$CCCWORKDIR/.pcocc-images`. If you want or need to use another location, create or edit the file `$HOME/.pcocc/repos.yaml` and add lines to declare new repositories, like this one:
      
        - name: project3
          path: /ccc/workflash/cont003/n4h00003/n4h00003/.pcocc-images
    
    This will declare a repository in `/ccc/workflash/cont003/n4h00003/n4h00003/.pcocc-images`, named `project3` (which, here, is shared between users of the project). Several repositories may be declared. You can check that they are taken into account using the command:
    
        pcocc image repo list

  - import the `.tar` image in PCOCC:

        cd /ccc/workflash/cont003/n4h00003/gaston  # this dir is just an example
        # n4h00003 is the project identifier I am in: use your own instead
        pcocc image import docker-archive:brainvisa-5.0.4.tar n4h00003:brainvisa-5.0.4
    
Then the image should be in pcocc:

    pcocc image list
    
should show it.

### Copy Brainvisa databases with rsync

To copy your already existing Brainvisa database to a TGCC file system, you can use rsync but you may need to add some options:

    rsync -av --copy-unsafe-links --chmod=Dg+s --chown=:YOUR_PROJECT brainvisa_database_directory tgcc:brainvisa_database_tgcc_directory

### Note about BrainVisa / Soma-Workflow

Normally [BrainVisa](https://brainvisa.info) and [Soma-Workflow](https://populse.github.io/soma-workflow) can run in "client/server mode" from your local machine, and can connect to a computing resource like the TGCC to run jobs. However in this specific facility, it's not recommended to use it this way (actually we don't know if it's allowed or if it would work, plus slurm is currently not supported in Soma-Workflow), because it would run a server on the TGCC login node, which could be seen as a security issue (even if it's running through a secure ssh tunnel). Moreover the number of jobs that can be submitted by a user there is limited. Thus we will not use this mode, but [the MPI mode](https://populse.github.io/soma-workflow/MPI_workflow_runner.html) instead, which runs entirely in a single parallel job.

### Configuring MPI for casa-distro images

Brainvisa/Soma-workflow can run using the MPI scheduler mode in order to run a workflow inside a single parallel job. So we must grant access to MPI inside the container.

pcocc called with the correct options will mount the MPI library directory, but we must setup the PATHS in the image. We use the hook in casa-distro images which sources `/casa/start_scripts/init.sh` if it is present

    mkdir $CCCWORKDIR/casa_ini_scripts
    cat << EOF > $CCCWORKDIR/casa_ini_scripts/init.sh
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
This command will run a [soma-workflow](https://populse.github.io/soma-workflow) workflow file in the job, using the MPI scheduler mode, using the configured resource `irene_MPI`.

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

The above help explains how to run a Soma-Workflow "workflow file". If you are using BrainVisa, Soma-Workflow is normally integrated in BrainVisa run engine. However the MPI mode is not integrated the same way as other scheduler modes (at least for now) and cannot be used transparently. Actually from a local machine you should not use the client/server mode of Soma-Workflow, which means: do not connect the TGCC resource (`irene_MPI` as above, for instance). You will have to save a workflow file from BrainVisa, then transfer it to the TGCC (vias `scp` or `rsync`), and run it from the TGCC cluster login machine. This means also that the file transfer features of Soma-Workflow will not work, because we do not connect via the client/server mode. This is because we avoid to make a server run on the TGCC login node (we're not sure it's allowed there). Another consequence is that configuring the workflow data from the local client machine will not be that easy, unless the [path translation feature](https://populse.github.io/soma-workflow/concepts.html#shared-resource-path) is used. But the data thould then be visible from the client machine (maybe using a sshfs mount).

Otherwise the workflow should be configured from the TGCC also.

#### using the GUI

There are 2 ways: using a remote X11 display, or using a remote desktop application.

##### Remote X11 display

* On the login node

You must ssh to the TGCC using the ``-Y`` option to allow X11 forwarding.
Then on the login node you can use graphical commands, like ``gedit``.
pcocc containers must be run using the ``-e DISPLAY`` option:

    pcocc run -s -e DISPLAY -I brainvisa-5.0.4
    # then in the interactive shell, you are in the container, for instance:
    soma_workflow_gui

* In an interactive node with X11 display

It is not recommended to run things on the login node. It is rather recommended to use an interactive computing node. This is done using:

    ccc_mprun -Xfirst -s -p v100l

this opens an interactive shell on a computing node, where graphical commands can be run. **But pcocc does not work in this mode.**

##### Using a remote desktop application

The TGCC has setup a NiceDV access to an interactive computing node:

[see their doc (needs VPN or CEA intra network)](https://www-fr.ccc.cea.fr/docs/irene/fr/html/toc/fulldoc/Interactive_access.html#remote-desktop-system-service-nicedcv)

To run it, from the login node:

    ccc_visu virtual -p v100l

This opens an interactive shell in a computing node. It also prints an URL to connect to, that you can paste in a web browser. After logging in, a gnome desktop will display in the web browser, with all interactive GUI features. If the node has a GPU, GPU and OpenGL will work.

For containers, in this desktop, **a local pcocc will work**:

    pcocc run -s -e DISPLAY -M nvidia -I brainvisa-5.0.4
    
This container will allow Cuda to work, as nvidia libraries are exported by pcocc inside it.

However, in our BrainVisa / casa-distro containers, OpenGL rendering does not work directly. ``glxinfo`` prints errors about "no matching fbConfigs or visuals found".

The solution is to use ``vglrun``: inside the pcocc container (be sure to use the pcocc option ``-M nvidia``), run:

    vglrun glxgears

or:

    vglrun anatomsit

... **Then hardware 3D does work !**

Otherwise, without ``virtualgl``, the other workaround we is to get back to software rendering, by setting ``/usr/local/lib/mesa`` first in the ``LD_LIBRARY_PATH`` env variable in he container:

    export LD_LIBRARY_PATH=/usr/local/lib/mesa:"$LD_LIBRARY_PATH"

In this case it can also be needed to set the variable ``MESA_GLX_FORCE_ALPHA`` to avoid a "transparent rendering" problem. Note that using ``glxgears`` the problem seems only partly solved, but it looks better in ``anatomist``.
    
 This can be done in an init script, possibly via automatic detection: for instance the following line will setup software rendering only if ``glxinfo`` does not work by itself:
 
    glxinfo > /dev/null 2>&1 || (export LD_LIBRARY_PATH=/usr/local/lib/mesa:"$LD_LIBRARY_PATH" && export MESA_GLX_FORCE_ALPHA=1)

#### using the commandline

We can use the `axon-runprocess` (or `python -m brainvisa.axon.runprocess`) command from a pcocc container, with the (new in 5.1) `-w / --write-workflow-only` option to write a workflow

### Monitoring workflows execution

As for workflow creation, we cannot monitor from a remote client machine. We have to monitor on the TGCC login machine of from an interactive node.
You an either use a visualization node to run `soma_workflow_gui` (not tried yet), or use an interactive pcocc container, and use the client API:

    pcocc run -s -I brainvisa-5.0.4
    % ipython
    [] import soma_workflow.client as swc
    [] wc = swc.WorkflowController('irene_MPI')
    soma-workflow starting in local mode
    new LocalConnection <soma_workflow.connection.LocalConnection object at 0x2b6bc723b510>
    start engine command: /usr/bin/python2 -m soma_workflow.start_workflow_engine irene_MPI workflow_engine_rivierdi 
    Communication with engine...
    -> Communication with engine OK
    Local connection established
    Workflow controller initialised
    [] wc.workflows()
    {5: (None, datetime.datetime(2022, 2, 11, 14, 14, 40))}
    [] wc.workflow_status(5)
    'workflow_done'
    [] wc.workflow_elements_status(5)
    ... -> lists jobs and statuses


