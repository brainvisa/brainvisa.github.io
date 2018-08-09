---
title: Release policy
---

# {{page.title}}

To be able to release a group of dependent projects, we need to be sure they all work together with marked versions. There may be several groups of projects, some working together, some other not working.

* Distribution: group of projects. In Brainvisa we currently have 3 "distributions":
  * "cea" which is the whole set of projects (internal distributions)
  * "brainvisa" is the projects set which are publicly distributed in binary packages
  * "cati" which is the CATI infrastructure projects (includes catidb, not anatomist etc)
* release: set of fixed versons for a projects, which are either officially released as sources or via pypi etc, or / and in a binary packjage of a given distribution.
* for instance "brainvisa" and "cati" distributions may not feature the same versions of some projetcs

## Release pipeline

The release pipeline its the procedure and tools which allow to get to a stable release, and build the wanted distributions.
