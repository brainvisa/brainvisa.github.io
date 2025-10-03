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

* [pyaims](https://brainvisa.info/pyaims/sphinx/pyaims_tutorial_nb.html]
* [pyanatomist](https://brainvisa.info/pyanatomist/sphinx/pyanatomist_tutorial.html)

### Programming APIs

* Base libraries
  * pyaims (Python) [[API docs](https://brainvisa.info/pyaims/sphinx/)] [[sources](https://github.com/brainvisa/aims-free)]
  * aimsdata (C++) [[API docs](https://brainvisa.info/aimsdata/doxygen/)] [[sources](https://github.com/brainvisa/aims-free)]
  * aimsalgo (C++) [[API docs](https://brainvisa.info/aimsalgo/doxygen/)] [[sources](https://github.com/brainvisa/aims-free)]
  * cartodata (C++) [[API docs](https://brainvisa.info/cartodata/doxygen/)] [[sources](https://github.com/brainvisa/aims-free)]
  * soma-io (C++) [[API docs](https://brainvisa.info/soma-io/doxygen/)] [[sources](https://github.com/brainvisa/soma-io)]
  * cartobase (C++) [[API docs](https://brainvisa.info/cartobase/doxygen/)] [[sources](https://github.com/brainvisa/soma-io)]
  * graph (C++) [[API docs](https://brainvisa.info/graph/doxygen/)] [[sources](https://github.com/brainvisa/aims-free)]
  * soma-base (Python) [[API docs](https://brainvisa.info/soma-base/sphinx/)] [[sources](https://github.com/populse/soma-base)]
* Anatomist [[developer docs](https://brainvisa.info/anatomist/dev_doc/)]
  * anatomist (C++) [[API docs](https://brainvisa.info/anatomist/doxygen/)] [[sources](https://github.com/brainvisa/anatomist-free)]
  * pyanatomist (Python) [[API docs](https://brainvisa.info/pyanatomist/sphinx/)] [[sources](https://github.com/brainvisa/anatomist-gpl)]
* Pipelining
  * capsul (Python) [[docs](https://brainvisa.info/capsul/)] [[sources](https://github.com/populse/capsul)]
  * soma-workflow (Python) [[docs](https://brainvisa.info/soma-workflow/sphinx/)] [[sources](https://github.com/populse/soma-workflow)]
  * axon (Python, deprecated for new pipelines) [[API docs](https://brainvisa.info/axon/dev_doc/)] [[sources](https://github.com/brainvisa/axon)]



<!-- TODO add a HOWTO for developing a toolbox and packaging it? -->

## Making BrainVISA releases

* [Packaging for Conda/pixi](packaging.md)

* [Release policy](release_policy.md)


## Support

[Support and questions](https://github.com/brainvisa/brainvisa.github.io/discussions)

## Misc helps

[Using clusters and the TGCC center](cluster.md)

[Irene specifications](irene.md)
