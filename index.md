---
title: BrainVISA developers site
---

# {{page.title}}

Neuroimaging software platform for mass data analysis

Users site: http://brainvisa.info

## Generalities

BrainVISA contains a set of projects, each project may be divided into several components. About 50 components are currently part of the BrainVISA environment.

Main applicative projects:

* [Morphologist](http://brainvisa.info/web/morphologist.html): brain segmentation and sulcal morphometry
* Several other processing tools and toolboxes
* [Anatomist](http://brainvisa.info/web/anatomist.html): Interactive 3D neuroimaging data visualization
* [Axon](http://brainvisa.info/axon/user_doc/index.html): Software and data organization
* [AIMS](http://brainvisa.info/aimsdata/user_doc/index.html): neuroimaging data manipulation, image processing

In addition to applicative projects, some infrastructure projects help managing compilation and cross-compilation, porting, testing, packaging, deployment, and releases of BrainVISA distributions:

* [brainvisa-cmake](http://brainvisa.info/brainvisa-cmake/index.html): compilation, testing etc.
* [brainvisa-installer](http://brainvisa.info/brainvisa-installer/index.html): packaging and installation
* [casa-distro](http://brainvisa.info/casa-distro/index.html): virtualization in containers of build and packaging systems

This developer site focuses more on the latter infrastructure tools, and on topics of how to develop in BrainVISA development environment, how to release the software packages etc.

There is also a [developer section](http://brainvisa.info/web/infrastructure.html) on BrainVisa website, which also deals about the various libraries APIs to develop applications.

## Topics

[Developing in BrainVISA environment](developing.md)
[Release policy](release_policy.md)
