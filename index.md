---
title: BrainVISA developers' website
---

# {{page.title}}

## Overview

**BrainVISA** is a neuroimaging software platform for mass data analysis

**All the information for users of BrainVISA is on [brainvisa.info](https://brainvisa.info/).** This website is meant for developers. It focuses on developers aspects: installing, building, developmlent documentations and APIs.

BrainVISA contains a set of projects, each project may be divided into several components. About 50 components are currently part of the BrainVISA environment.

### casa-distro:
  
Development, distribution infrastructure in a containerized environment (using Singularity or VirtualBox). Used to install BrainVisa, or install a developer environment, as explained in [BrainVisa download page](https://brainvisa.info/web/download.html).

* Stable doc: https://brainvisa.info/casa-distro/index.html
* Devel (master) doc: https://brainvisa.github.io/casa-distro/

* [brainvisa-cmake](httsp://brainvisa.info/brainvisa-cmake/index.html): compilation, testing etc.

### Aims:

Neuroimaging data manipulation, image processing (C++ and Python)

* [AIMS](https://brainvisa.info/aimsdata/user_doc/index.html)

### Anatomist

Interactive 3D neuroimaging data visualization

* [Anatomist](https://brainvisa.info/web/anatomist.html)

### Capsul / Soma-Workflow

Pipelining and distributed calculation (Python)

* [capsul](https://brainvisa.info/capsul/index.html): pipelining
* [soma-workflow](https://brainvisa.info/web/soma-workflow/index.html)

### Morphologist

Brain segmentation, cortical sulci analysis and morphometry. This is the primary applicative toolbox of BrainVisa.

* [Morphologist](https://brainvisa.info/web/morphologist.html)

### Axon

The older pipelining infrastructure and GUI, now obsolete, but still widely used...

* [Axon](https://brainvisa.info/axon/user_doc/index.html)


## Contributor's guide

[Developing in BrainVISA environment](developing.md)

[Contributor's guide](contributing.md)

[Release policy](release_policy.md)

## Tutorials

* pyaims
* pyanatomist

## API

* casa-distro
  * casa-distro (python)
    * Sources repository: https://brainvisa.info/casa-distro/
  * brainvisa-cmake (cmake, python)
* aims and pyaims:
  * aimsdata (C++)
  * pyaims (pythn)
  * aimsalgo (C++)
  * pyaimsalgo (python)
  * cartodata (C++)
  * soma-io (C++)
  * cartobase (C++)
  * soma-base (python)
  * pyaims (python)
* anatomist
  * anatomist (C++)
  * pyanatomist (python)
* capsul
  * capsul (python)
  * soma-workflow (python)
* axon

## Support

[Support and questions](https://github.com/brainvisa/brainvisa.github.io/discussions)

-old -

[Porting projects to Qt5 and Python3](porting_projects.md)
