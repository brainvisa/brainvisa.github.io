---
name: BrainVISA update check-list
about: RESERVED for the @brainvisa/admin team to release BrainVISA projects
title: BrainVISA 6.0 release check-list
labels: ''
assignees: ''
---

# Update 6.0 check-list

- [ ] Check that tests are successful on https://brainvisa.info/builds/

- [ ] Update changelogs and [the list of known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+for+BrainVISA%22+is%3Aissue+is%3Aopen)
  - [ ] https://github.com/brainvisa/aims-free/blob/soma-env-6.0/aimsdata/sphinx/user_doc/changelog.md
  - [ ] https://github.com/brainvisa/axon/blob/soma-env-6.0/sphinxdoc/user_doc/changelog.md
  - [ ] https://github.com/brainvisa/anatomist-free/blob/soma-env-6.0/sphinxman/changelog.md
  - [ ] https://bioproj.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/changelog.md
  - [ ] https://bioproj.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/news.md

- [ ] Edit the website to announce the new release
  - [ ] Edit web project sources

- [ ] Connect to rosette using
        `ssh a-sac-ns-brainvisa@rosette`

- [ ] Create public and brainvisa-cea packages for conda
  - [ ] Change directory
          `cd /home_local/a-sac-ns-brainvisa/bbi-daily/soma-env-6.0`
  - [ ] Update release version and generate packaging plan
          `pixi run soma-env packaging-plan --release --nf-publish /home_local/a-sac-ns-brainvisa/bbi-daily/neuro-forge --install /drf/neurospin/brainvisa-6.0 --container /home_local/a-sac-ns-brainvisa/bbi-daily/casa-distro --web`
  - [ ] Apply packaging plan, build conda packages and publish to /drf/neuro-forge and web site, build containers and install them etc.
          `pixi run soma-env apply-plan`

- [ ] Verify that the deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
    `/drf/brainvisa/brainvisa-6.0/bin/bv bash`
    `for __f in $(find /drf/brainvisa/brainvisa-6.0/.pixi/envs/default/share/brainvisa-share-6.0 -type f -name '*.ima'); do AimsFileInfo -v -i "${__f}"; done`
    `anatomist $(find /drf/brainvisa/brainvisa-6.0/.pixi/envs/default/share/brainvisa-share-6.0 -type f -name '*.nii')`
    `brainvisa`
- [ ] Verify that the copntainer deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
    `/drf/brainvisa/brainvisa-casa-6.0.34/bin/anatomist`

- [ ] Build pip packages for the python projects soma-workflow, soma-base, populse-db and capsul which are distributed in pip, if they have changed:
  - [ ] `./soma-env-6.0/bin/bv python -m build ./soma-env-6.0/src/soma/soma-workflow`
  - [ ] `./soma-env-6.0/bin/bv python -m build ./soma-env-6.0/src/soma/soma-base`
  - [ ] `./soma-env-6.0/bin/bv python -m build ./soma-env-6.0/src/populse/populse-db`
  - [ ] `./soma-env-6.0/bin/bv python -m build ./soma-env-6.0/src/capsul`

- [ ] Publish them to `test.pypi.org`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload --repository testpypi ./soma-env-6.0/src/soma/soma-workflow/dist/*`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload --repository testpypi ./soma-env-6.0/src/soma/soma-base/dist/*`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload --repository testpypi ./soma-env-6.0/src/populse/populse-db/dist/*`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload --repository testpypi ./soma-env-6.0/src/capsul/dist/*`

- [ ] Create a virtualenv test environment and test packages install:
  - [ ] `mkdir -p /tmp/brainvisa-6.0/testenv && python3 -m venv /tmp/brainvisa-6.0/testenv`
  - [ ] `/tmp/brainvisa-6.0/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ soma-workflow`
  - [ ] `/tmp/brainvisa-6.0/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple soma-base`
  - [ ] `/tmp/brainvisa-6.0/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple populse-db`
  - [ ] `/tmp/brainvisa-6.0/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple capsul`

- [ ] Publish them to `pypi.org`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload ./soma-env-6.0/src/soma/soma-workflow/dist/*`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload ./soma-env-6.0/src/soma/soma-base/dist/*`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload ./soma-env-6.0/src/populse/populse-db/dist/*`
  - [ ] `./soma-env-6.0/bin/bv python -m twine upload ./soma-env-6.0/capsul/dist/*`
