---
name: BrainVISA release check-list
about: RESERVED for the @brainvisa/admin team to release BrainVISA projects
title: BrainVISA x.y release check-list
labels: ''
assignees: ''
---

# Release check-list

- [ ] Check that tests are successful on https://brainvisa.info/builds/

- [ ] Update changelogs and [the list of known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+of+BrainVISA%22+is%3Aissue+is%3Aopen)
  - [ ] https://github.com/brainvisa/aims-free/blob/soma-env-x.y/aimsdata/doc/html/en/changelog.html
  - [ ] https://github.com/brainvisa/axon/blob/soma-env-x.y/doc/en/help/changelog.html
  - [ ] https://github.com/brainvisa/anatomist-free/blob/soma-env-x.y/doc/html/en/changelog.html
  - [ ] https://bioproj.extra.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/changelog.rst

- [ ] Connect to rosette using
        `ssh a-sac-ns-brainvisa@rosette`
- [ ] Create public and brainvisa-cea packages for conda
  - [ ] Change directory
          `cd /home_local/a-sac-ns-brainvisa/bbi-daily/soma-env-x.y`
  - [ ] Update sources and build software tree
          `pixi run bv_maker`
  - [ ] Update release version and generate packaging plan
          `pixi run soma-env packaging_plan --release`
  - [ ] Apply packaging plan, build conda packages and publish to /drf/neuro-forge
          `pixi run soma-env apply_plan`
  - [ ] Change directory
          `cd /home_local/a-sac-ns-brainvisa/bbi-daily/neuro-forge`
  - [ ] Reindex packages on /drf/neuro-forge and publish packages to brainvisa.info
          `pixi run neuro-forge publish`
    
- [ ] Create, test, and publish/deploy the images (optional)
	- `sif` monolothic brainvisa install
		- [ ] Change directory
		        `cd /home_local/a-sac-ns-brainvisa/bbi-daily/casa-distro`
		- [ ] Update casa distro
		        `git pull`
				`export PATH=$(pwd)/casa-distro/bin:"$PATH"`
		- [ ] Change directory
		        `cd /home_local/a-sac-ns-brainvisa/bbi-daily`		
		- [ ] Pull a "casa-pixi" apptainer image from the BrainVISA server:
				`export CASA_BASE_DIRECTORY=$(pwd)`
				`casa_distro pull_image image=casa-pixi-5.4.sif`
		- [ ] Create the monolithic image
				`casa_distro_admin create_user_image container_type=apptainer_pixi image_version=5.4 base_image=casa-pixi-5.4.sif version=x.y distro=brainvisa`
		- [ ] Verify that the image works
		  - [ ] Install the image
				`mkdir -p /tmp/test-brainvisa-x.y;apptainer run -ce --bind /tmp/test-brainvisa-x.y:/casa/setup /home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-x.y.sif`
		  - [ ] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
		- [ ] Publish the image on the BrainVISA web site
			  `casa_distro_admin publish_user_image image=brainvisa-x.y.sif`

- [ ] Edit the website to announce the new release
  - [ ] web project sources
  - [ ] log on rosette, rebuild the web site in the brainvisa-web build: `ssh a-sac-ns-brainvisa@rosette`, then in the server:
    - [ ] `/home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-web/bin/bv_maker`
    - [ ] publish the web site using the publish script: `/home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-web/bin/bv /home_local/a-sac-ns-brainvisa/bbi-daily/brainvisa-web/src/communication/web/master/scripts/bv_publish_web brainvisa@brainvisa.info:/var/www/html/brainvisa.info`

- [ ] Deploy the `cea` pixi release:
  - [ ] Create a new directory `/drf/brainvisa/brainvisa-x.y` 
		`mkdir -p /drf/brainvisa/brainvisa-x.y`
  - [ ] Setup pixi workspace
```bash
cat > pixi.toml <<EOF
[workspace]
authors = ["sapetnioc <sapetnioc@users.noreply.github.com>"]
channels = ["https://brainvisa.info/neuro-forge", "conda-forge", "/drf/neuro-forge/brainvisa-cea"]
name = "brainvisa-x.y"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
brainvisa = ">=x.y"
soma-pytorch = "*"
bioprocessing = ">=x.y"
constellation = ">=x.y"
primatologist = ">=x.y"
morphologist-baby = ">=x.y"
brainrat = ">=x.y"
EOF
```
  - [ ] Install environment
        `pixi install`
		`pixi run brainvisa -b --setup`
		`pixi run bv_update_links`
  - [ ] Verify that the deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
        `/drf/brainvisa/brainvisa-x.y/bin/bv bash`
        `for __f in $(find /drf/brainvisa/brainvisa-x.y/.pixi/envs/default/share/brainvisa-share-x.y -type f -name '*.ima'); do AimsFileInfo -v -i "${__f}"; done`
        `anatomist $(find /drf/brainvisa/brainvisa-x.y/.pixi/envs/default/share/brainvisa-share-x.y -type f -name '*.nii')`
		`brainvisa`
  - [ ] Make it the default version: `ln -sfT brainvisa-x.y/ /drf/brainvisa/brainvisa`

- [ ] Build pip packages for the python projects soma-workflow, soma-base, populse_db and capsul which are distributed in pip:
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/soma/soma-workflow/x.y`
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/soma/soma-base/x.y`
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/populse/populse_db/x.y`
  - [ ] `./soma-env-x.y/bin/bv python -m build ./soma-env-x.y/src/capsul/x.y`

- [ ] Publish them to `test.pypi.org`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/soma/soma-workflow/x.y/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/soma/soma-base/x.y/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/populse/populse-db/x.y/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload --repository testpypi ./soma-env-x.y/src/capsul/x.y/dist/*`

- [ ] Create a virtualenv test environment and test packages install:
  - [ ] `mkdir -p /tmp/brainvisa-x.y/testenv && python -m venv /tmp/brainvisa-x.y/testenv`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ soma-workflow`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ soma-base`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ populse-db`
  - [ ] `/tmp/brainvisa-x.y/testenv/bin/python -m pip install --index-url https://test.pypi.org/simple/ capsul`

- [ ] Publish them to `pypi.org`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/src/soma/soma-workflow/x.y/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/src/soma/soma-base/x.y/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/src/populse/populse-db/x.y/dist/*`
  - [ ] `./soma-env-x.y/bin/bv python -m twine upload ./soma-env-x.y/capsul/x.y/dist/*`

- [ ] [Open a new GitHub issue for known issues](https://github.com/brainvisa/brainvisa.github.io/issues/new?template=known-issues-of-a-brainvisa-release.md&title=Known+issues+for+BrainVISA+x.y)

- [ ] Close [the old GitHub known issues](https://github.com/brainvisa/brainvisa.github.io/issues/133)

- [ ] Make an announcement [on the GitHub discussions](https://github.com/brainvisa/brainvisa.github.io/discussions/new)

- [ ] If the releases has significant new features, announce it on the mailing list
