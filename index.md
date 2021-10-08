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

* [Stable doc](https://brainvisa.info/casa-distro/index.html)
* [Devel (master) doc](https://brainvisa.github.io/casa-distro/)

* [brainvisa-cmake](https://brainvisa.info/brainvisa-cmake/index.html): compilation, testing etc.

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

* [casa-distro](https://brainvisa.info/casa-distro/)
  * [Sources repository](https://github.com/brainvisa/casa-distro)
  * [master branch doc]
  * [brainvisa-cmake](https://brainvisa.info/brainvisa-cmake/index.html) (cmake, python)
    * [Sources repository](https://github.com/brainvisa/brainvisa-cmake)
    * [master branch doc]
* [aims and pyaims:](https://brainvisa.info/aimsdata/user_doc/index.html)
  * [pyaims](https://brainvisa.info/pyaims/sphinx/index.html) (python)
    * [Sources repository](https://github.com/brainvisa/aims-free)
  * [aimsdata](https://brainvisa.info/aimsdata/doxygen/index.html) (C++)
    * [Sources repository](https://github.com/brainvisa/aims-free)
  * [aimsalgo](https://brainvisa.info/aimsalgo/doxygen/index.html) (C++)
    * [Sources repository](https://github.com/brainvisa/aims-free)
  * [cartodata](https://brainvisa.info/cartodata/doxygen/index.html#cartodata) (C++)
    * [Sources repository](https://github.com/brainvisa/aims-free)
  * [soma-io](https://brainvisa.info/soma-io/doxygen/index.html#somaio) (C++)
    * [Sources repository](https://github.com/brainvisa/soma-io)
  * [cartobase](https://brainvisa.info/cartobase/doxygen/index.html#cartobase) (C++)
    * [Sources repository](https://github.com/brainvisa/soma-io)
  * [graph](https://brainvisa.info/graph/doxygen/index.html#graph) (C++)
    * [Sources repository](https://github.com/brainvisa/aims-free)
  * [soma-base](https://brainvisa.info/soma-base/sphinx/index.html) (python)
    * [Sources repository](https://github.com/populse/soma-base)
    * [master branch doc]
* [anatomist](https://brainvisa.info/anatomist/dev_doc/index.html)
  * [anatomist](https://brainvisa.info/anatomist/doxygen/index.html) (C++)
    * [Sources repository](https://github.com/brainvisa/anatomist-free)
  * [pyanatomist](https://brainvisa.info/pyanatomist/sphinx/index.html) (python)
    * [Sources repository](https://github.com/brainvisa/anatomist-gpl)
* [capsul](https://brainvisa.info/capsul/index.html)
  * [capsul](https://brainvisa.info/capsul/index.html) (python)
    * [Sources repository](https://github.com/populse/capsul)
    * [master branch doc]
  * [soma-workflow](https://brainvisa.info/soma-workflow/sphinx/index.html) (python)
    * [Sources repository](https://github.com/populse/soma-workflow)
    * [master branch doc]
* [axon](https://brainvisa.info/web/infrastructure.html#)
  * [API doc](https://brainvisa.info/axon/dev_doc/)
  * [Sources repository](https://github.com/brainvisa/axon)
  * [Programming doc](https://brainvisa.info/axon/Programming_with_brainvisa.pdf)


## Support

[Support and questions](https://github.com/brainvisa/brainvisa.github.io/discussions)

-old -

[Porting projects to Qt5 and Python3](porting_projects.md)
