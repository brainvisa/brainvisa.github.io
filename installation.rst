
====================
Installing BrainVISA
====================

From release 6, BrainVISA is using a Conda-based distrubution. Only Linux systems are directly supported, although it will also work in Windows WSL2.
Mac installation however, is not supported and will need a Linux virtual machine.

Actually, instead of Conda, we will be using in this doc Pixi, which is an alternative (much faster than Conda) but which is using the same repositories ("channels") as Conda.

Installing in a Pixi environment
================================

There are 2 alternatives: one is simpler but will not use Cuda in deep-learning applications. The second will add the channel definitions and rules for Cuda and PyTorch with Cuda.

Simplest without cuda:
----------------------

.. code-block:: bash

    # Create a workspace directory
    mkdir ~/workspace

    # Setup workspace
    cd ~/workspace
    pixi init -c https://brainvisa.info/neuro-forge -c conda-forge

    # Enter workspace
    pixi shell

    # Install anatomist
    pixi add anatomist

    # Run anatomist
    anatomist


With Cuda
---------

.. code-block:: bash

    # Create a workspace directory
    mkdir ~/workspace

    # Setup workspace
    cd ~/workspace
    pixi init -c https://brainvisa.info/neuro-forge -c pytorch -c nvidia -c conda-forge

    echo "" >> pixi.toml
    echo "[pypi-dependencies]" >> pixi.toml
    echo 'dracopy = ">=1.4.2"' >> pixi.toml

    # Enter workspace
    pixi shell

    # Install anatomist
    pixi add anatomist

    # Run anatomist
    anatomist


Automatic environment activation
--------------------------------

Pixi / Conda is a compatimented environment, you need to enter it and activate it before using its contents, which is normally done via the `pixi` or `conda`, or `mamba` command, which basically runs a shell in a new environment.
To automatize this, we provide a script to enter the environment, and links for all commandlines. This is not installed by default (mainly becuase Pixi does not provide a convenient way to perform post-install operations). To do it, simply and run the script `bv_update_bin_links` inside the conda environment::

    pixi run bv_update_bin_links

It will create (if it doesn't exist yet) in the environment dir, a `bin/` subdirectory, which will contain a script `bv`, and all executable commandlines as a symbolic link to this script.
This is completely similar to what was provided with Casa-Distro installations in BrainVisa 5. Indeed the script is also called `bv` because it has the same role and functionalities, even if it is completely different internally.

You may call the `bv_update_bin_links` script later to update the links to commandlines if you install new packages.


Installing in a containerized environment
=========================================

In some cases a container installation is useful, either because the conda install will not run on a specific system (this should be rare), or because a particular facility requires to install software as containers (in a cluster or cloud for instance).

Casa-Distro is not dead... We also provide a Casa-distro containerized installation with Pixi inside.

Here again we have 2 alternatives:

Container with only pixi
------------------------

This container allows to installs modular packages on the local filesystem:

.. code-block:: bash

    curl https://brainvisa.info/download/brainvisa-6.0.sif
    mkdir brainvisa-6.0
    apptainer run -c -B brainvisa-6.0:/casa/setup brainvisa-6.0.sif
    ./brainvisa-6.0/bin/bv bash
    % cd /casa/host/install
    % pixi add morhologist

The container provides a pixi environment already setup (in /casa/host/install), with BrainVisa, Cuda and Pytorch channels configured.

Monolithic self-contained container
-----------------------------------

These containers are basically the same, but embed the full brainvisa installation in the `.sif` file, thus are self-contained and can be installed for instance on a cluster with no internet network access.

In order to reduce the maintenance burden on our (very small) team, we do not provide and support those containers, but they are very easy to build.

See https://dev.brainvisa.info/pixi_in_container.html, Creating a self-contained container from the modular one


