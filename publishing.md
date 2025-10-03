
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

* Option 2: monolothic self-contained container:

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
