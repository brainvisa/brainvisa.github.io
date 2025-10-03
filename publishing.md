
# Publishing documentation

How to build BrainVisa packages and publish them for end users

## Creating an Apptainer/Singularity container with a pixi installation inside

* Install apptainer

Make sure that the apptainer distribution works with the "fakeroot" features

* Install casa-dstro

* pull an Unbintu-24.04 image for apptainer:

```
apptainer pull ubuntu-24.04.sif docker://ubuntu:24.04
```

* Create a casa-distro "base image" for pixi:

```
export CASA_BASE_DIRECTORY=$(pwd)
casa_distro_admin create_base_image type=pixi image_version=5.4 base=ubuntu-24.04.sif
```

* Option 1: lightweight modular container: the brainvisa installation will be done at setup time by the user:

```
casa_distro_admin create_user_image container_type=apptainer_pixi image_version=5.4 base_image=casa-pixi-5.4.sif version=6.0 distro=brainvisa install=0
```

* Option 2: monolithic self-contained container:

```
casa_distro_admin create_user_image container_type=apptainer_pixi image_version=5.4 base_image=casa-pixi-5.4.sif version=6.0 distro=brainvisa
```

### Installing an apptainer/pixi environment (user side):

(note: this doc section should end up in a user installation doc)

* download the `.sif` image (let's call it `brainvisa-6.0.sif`

* run the image setup, as in casa-distro. However, as pixi uses a cache directory, the cache directory has to be mounted in the image read-write. This amounts to mounting (using the `-B` option) either `$XDG_CACHE_HOME`, `$RATTLER_CACHE_DIR`, or `$HOME`.

```
mkdir brainvisa-6.0
apptainer run -c -B $HOME -B brainvisa-6.0:/casa/setup brainvisa-6.0.sif
```

### Creating a self-contained container from the modular one

* install a lightweight image, run its setup

* install whatever packages you need in the installed casa-distro environment in /casa/host/install

* if needeed, update the binary links by running the command:

```
bv_update_bin_links
```

* exit the container

* `cd` into the environment directory

* save the following recipe file in the current environment dir. Let's call it here `brainvisa-monolithic.recipe`:

```
Bootstrap: localimage

From: ../brainvisa-6.0.sif

%post
cd /casa/install
pixi add brainvisa=6.0
pixi run bv_update_bin_links
```

* build the container image from this directory. The current dir is important as files will be copied from the local installation into the image from the relative current directory.

```
apptainer build build ../brainvisa_monolithic-6.0.sif ../rainvisa-monolithic.recipe
```

* OK you can try installing an environment from it:

```
mkdir brainvisa-monomithic-6.0
apptainer run -c -B brainvisa-monlithic-6.0:/casa/setup brainvisa-monolithic-6.0.sif
```
