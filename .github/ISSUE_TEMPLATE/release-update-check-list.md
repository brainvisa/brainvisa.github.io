---
name: BrainVISA release check-list
about: RESERVED for the @brainvisa/admin team to release BrainVISA projects
title: BrainVISA x.y release check-list
labels: ''
assignees: ''
---

# Release x.y check-list

- [ ] Check that tests are successful on https://brainvisa.info/builds/

- [ ] Update changelogs and [the list of known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+for+BrainVISA%22+is%3Aissue+is%3Aopen)
  - [ ] https://github.com/brainvisa/aims-free/blob/soma-env-x.y/aimsdata/sphinx/user_doc/changelog.md
  - [ ] https://github.com/brainvisa/axon/blob/soma-env-x.y/sphinxdoc/user_doc/changelog.md
  - [ ] https://github.com/brainvisa/anatomist-free/blob/soma-env-x.y/sphinxman/changelog.md
  - [ ] https://bioproj.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/changelog.md
  - [ ] https://bioproj.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/news.md

- [ ] Connect to rosette using
        `ssh a-sac-ns-brainvisa@rosette`
- [ ] Create public and brainvisa-cea packages for conda
  - [ ] Change directory
          `cd /home_local/a-sac-ns-brainvisa/bbi-daily/soma-env-x.y`
  - [ ] Update sources and build software tree
          `pixi run bv_maker`
  - [ ] Update release version and generate packaging plan
          `pixi run soma-env packaging-plan --release`
  - [ ] Apply packaging plan, build conda packages and publish to /drf/neuro-forge
          `pixi run soma-env apply-plan`
  - [ ] Change directory
          `cd /home_local/a-sac-ns-brainvisa/bbi-daily/neuro-forge`
  - [ ] Reindex packages on /drf/neuro-forge and publish packages to brainvisa.info
          `pixi run neuro-forge publish`
    
- [ ] Create, test, and publish/deploy the images (optional)
      (cannot be done on rosette at the moment because of priviledges issues)
	- `sif` monolothic brainvisa install
		- [ ] Change directory
		        `cd /home_local/a-sac-ns-brainvisa/bbi-daily/casa-distro`
		- [ ] Update casa distro
		        `git pull`
				`export PATH=$(pwd)/bin:"$PATH"`
		- [ ] Change directory
		        `cd /home_local/a-sac-ns-brainvisa/bbi-daily`		
		- [ ] Pull a "casa-pixi" apptainer image from the BrainVISA server:
				`export CASA_BASE_DIRECTORY=$(pwd)`
				`casa_distro pull_image image=casa-pixi-5.4.sif`
		- [ ] Create the monolithic image
				`casa_distro_admin create_user_image container_type=apptainer_pixi image_version=5.4 base_image=casa-pixi-5.4.sif version=x.y.z distro=brainvisa`
		- [ ] Verify that the image works
		  - [ ] Install the image
				`mkdir -p /tmp/test-brainvisa-x.y.z;apptainer run -ce --bind /tmp/test-brainvisa-x.y.z:/casa/setup /home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-x.y.z.sif`
		  - [ ] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
		- [ ] Publish the image on the BrainVISA web site
			  `casa_distro_admin publish_user_image image=brainvisa-x.y.z.sif`
    - [ ] deploy it in /drf
        - [ ] `cd /drf/brainvisa`
              `wget https://brainvisa.info/download/brainvisa-x.y.z.sif`
              `mkdir brainvisa-x.y.z`
              `apptainer run -ce --bind brainvisa-x.y.z:/casa/setup brainvisa-x.y.z.sif`
        - [ ] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`

- [ ] Edit the website to announce the new release
  - [ ] web project sources
  - [ ] log on rosette, rebuild the web site in the brainvisa-web build: `ssh a-sac-ns-brainvisa@rosette`, then in the server:
    - [ ] `/home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-web/bin/bv_maker`
    - [ ] publish the web site using the publish script: `/home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-web/bin/bv /home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-web/src/communication/web/master/scripts/bv_publish_web brainvisa@brainvisa.info:/var/www/html/brainvisa.info`

- [ ] Deploy the `cea` pixi release:
  - [ ] Update the existing install directory:
        `cd /drf/brainvisa/brainvisa-x.y`
        `pixi update brainvisa`
        `pixi run brainvisa -b --setup`
        `pixi run bv_update_bin_links`
  - [ ] Verify that the deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
        `/drf/brainvisa/brainvisa-x.y/bin/bv bash`
        `for __f in $(find /drf/brainvisa/brainvisa-x.y/.pixi/envs/default/share/brainvisa-share-x.y -type f -name '*.ima'); do AimsFileInfo -v -i "${__f}"; done`
        `anatomist $(find /drf/brainvisa/brainvisa-x.y/.pixi/envs/default/share/brainvisa-share-x.y -type f -name '*.nii')`
		`brainvisa`

- [ ] Build pip packages for the python projects soma-workflow, soma-base, populse-db and capsul which are distributed in pip, if they have changed:
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/soma/soma-workflow`
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/soma/soma-base`
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/populse/populse-db`
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/capsul`

- [ ] Publish them to `test.pypi.org`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/soma/soma-workflow/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/soma/soma-base/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/populse/populse-db/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/capsul/dist/*`

- [ ] Create a virtualenv test environment and test packages install:
  - [ ] `mkdir -p /tmp/brainvisa-x.y/testenv && python3 -m venv /tmp/brainvisa-x.y/testenv`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ soma-workflow`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple soma-base`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple populse-db`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple capsul`

- [ ] Publish them to `pypi.org`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/src/soma/soma-workflow/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/src/soma/soma-base/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/src/populse/populse-db/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/capsul/dist/*`
