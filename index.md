---
title: BrainVISA developers' website
---

# {{page.title}}

## Overview

**BrainVISA** is a neuroimaging software platform for mass data analysis

**All the information for users of BrainVISA is on [brainvisa.info](https://brainvisa.info/).** This website (dev.brainvisa.info) is meant for developers. It focuses on building BrainVISA from source, programming APIs, and packaging.

BrainVISA consists of an ecosystem of software projects, each of which may be divided into several components. These projects are interdependent and require coordinated releases: therefore, we strongly recommend following the guidelines below for setting up a development environment.

<!-- TODO: add info on
- installing a nightly build
- branches (main/master vs stable)
- lack of dev conda packages for now
-->


## Setting up a development environment

> [!IMPORTANT]
> BrainVISA is transitioning from a monolithic container-based distribution (BrainVISA 5) to a modular Conda/pixi-based distribution (BrainVISA 6).

* [Development environment for BrainVISA 6 (in progress)](devenv_pixi.md)
* [Development environment for BrainVISA 5](devenv_casa-distro.md)


## Programming in BrainVISA

### Tutorials

<!-- TODO links -->

* pyaims
* pyanatomist

### Programming APIs

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


<!-- TODO add a HOWTO for developing a toolbox and packaging it? -->

## Making BrainVISA releases

* [Packaging for Conda/pixi](packaging.md)

* [Release policy](release_policy.md)


## Support

[Support and questions](https://github.com/brainvisa/brainvisa.github.io/discussions)

## Misc helps

[Using clusters and the TGCC center](cluster.md)

[Irene specifications](irene.md)
