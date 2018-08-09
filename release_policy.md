---
title: Release policy
---

# {{page.title}}

To be able to release a group of dependent projects, we need to be sure they all work together with marked versions. There may be several groups of projects, some working together, some other not working.

* Distribution: group of projects. In Brainvisa we currently have 3 "distributions":
  * "cea" which is the whole set of projects (internal distributions)
  * "brainvisa" is the projects set which are publicly distributed in binary packages
  * "cati" which is the CATI infrastructure projects (includes catidb, not anatomist etc)
* release: set of fixed versons for a projects, which are either officially released as sources or via pypi etc, or / and in a binary package of a given distribution.
* for instance "brainvisa" and "cati" distributions may not feature the same versions of some projetcs

## Release pipeline

The release pipeline its the procedure and tools which allow to get to a stable release, and build the wanted distributions. 
When we want to perform a release and publish one (or several) distributions, the following steps should be done:

* build a release plan: determine which projects should be released in the distribution, which versions for them, and check if versions match source revisions (typically if the source code has changed sice the previous release, then the version number must have changed also, or be changed before the release)
* create a "release candidate" (RC) branch for all the projects in the distribution. The RC branch is cloned from the current master/bug_fix branches.
* build and test the RC branch for all target architectures. During the process, some projects will be validated, others may not. The goal is now to stabilize the RC branch.
* If a project does not pass all tests, then it should be fixed and tests re-run.
  * Fixes are generally applied on master/bug_fix branches.
  * for this project *only*, the fixed version should be merged into the RC branch. If needed cherry-picking can be used to exclude other modifications not related to the current fixes.
  * If a project can be only fixed if modifications are done in an upstream project, then the upstream project modifications have to also be manually merged into RC.
  * Any time a project sources change in RC, its tests are automatically invalidated: they have to run again. Same for all downstream projects
  * tests should / may be run following projects dependencies: test projects which have actually changed, and their downstream projects. See [Tests handling](#tests-handling)
* when all projects are validated, the release is OK.
  
## Tests handling

* Run tests only when needed: if a project is validated and has not changed, do not re-run its tests. Inversely if it is not OK and has not been fixed, re-running its tests should / can not be done automatically (but it could be done manually to overcome a failure for external reasons)
* Take dependencies into account when running tests: if an upstream project has changed, then the current project has to be tested again.
* Packaging tests: some tests are run on binary packages. They may fail when thirdparty dependencies packages have failed or are not complete (typically: missing binary library). One option is to include thirdparty packages dependencies in the tests dependencies. Thirdparty packages have no tests (thus are always considered valid), but when they change, downstream projects are invalidated and should be tested again.
