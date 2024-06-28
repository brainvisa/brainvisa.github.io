# Release check-list

- [x] Check that all projects have the correct version in their sources: `bv_tag_release --fix-source-version-numbers`
- [x] ~If needed, fix project versions in their sources: `GIT_AUTHOR_NAME='<author_name>' GIT_COMMITTER_NAME='<committer_name>' GIT_AUTHOR_EMAIL='<author_email>' GIT_COMMITTER_EMAIL='<committer_email>' bv_tag_release --fix-source-version-numbers --really-do-it`~

- [x] Check that tests are successful on https://brainvisa.info/builds/
  - [x] Fix capsul tests (soma-workflow `sqlite3.OperationalError: no such table: db_version`)

- [x] Update changelogs and [the list of known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+of+BrainVISA%22+is%3Aissue+is%3Aopen)
  - [x] https://github.com/brainvisa/aims-free/blob/5.1/aimsdata/doc/html/en/changelog.html
  - [x] https://github.com/brainvisa/axon/blob/5.1/doc/en/help/changelog.html
  - [x] https://github.com/brainvisa/anatomist-free/blob/5.1/doc/html/en/changelog.html
  - [x] https://bioproj.extra.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/changelog.rst

- [x] Run a last build to update the changelogs 
  - [x] `bv_maker sources doc` in the `brainvisa` distro
  - [x] `bv_maker sources doc` in the `cea` distro

- [ ] Create, test, and publish/deploy the images
  - `sif` image for the `brainvisa` distro
    - [x] Create the image with:
           `export CASA_BASE_DIRECTORY=/volatile/a-sac-ns-brainvisa/bbi-nightly`
           `export TMPDIR=/volatile/tmp`
           `./brainvisa-5.1-5.3/bin/casa_distro_admin create_user_image version=5.1.2 container_type=singularity distro=brainvisa image_version=5.3 branch=5.1 install_thirdparty=file:///neurospin/brainvisa/thirdparty/thirdparty.json`
    - [x] Verify that the image works
      - [x] Install the image
	        `mkdir -p /tmp/test-brainvisa-5.1.2;apptainer run -ce --bind /tmp/test-brainvisa-5.1.2:/casa/setup /volatile/a-sac-ns-brainvisa/bbi-nightly/brainvisa-5.1.2.sif`
      - [x] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
    - [x] Publish the image on the BrainVISA web site
	      `casa_distro_admin publish_user_image image=brainvisa-5.1.2.sif`

  - `ova` image for the `brainvisa` distro
    - [ ] Create the image with `./brainvisa-5.1-5.3/bin/casa_distro_admin create_user_image version=5.1.2 install=no container_type=vbox base_image=casa-run-5.3.ova environment_name=brainvisa-5.1-5.3`
    - [ ] Verify that the image works
      - [ ] Install the image
      - [ ] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
    - [ ] Publish the image on the BrainVISA web site

  - `sif` image for the `cea` distro
    - [x] Create the image with:
           `export CASA_BASE_DIRECTORY=/volatile/a-sac-ns-brainvisa/bbi_nightly`
           `export TMPDIR=/volatile/tmp/a-sac-ns-brainvisa`
           `./cea-5.1-5.3/bin/casa_distro_admin create_user_image version=5.1.2 name=brainvisa-cea-5.1.2 container_type=singularity distro=cea image_version=5.3 branch=5.1 install_thirdparty=file:///neurospin/brainvisa/thirdparty/thirdparty.json` (beware to name the image brainvisa-cea- and not just cea-)

  - ~`ova` image for the `cea` distro~ we will only do these images if users request them
    - [ ] ~Create the image with `./cea-5.1-5.3/bin/casa_distro_admin create_user_image name=brainvisa-cea version=5.1.2 base_directory=/volatile/a-sac-ns-brainvisa/bbi_nightly install=no container_type=vbox base_image=casa-run-5.3.ova environment_name=cea-5.1-5.3` (beware to name the image `brainvisa-cea-` and not just `cea-`)~

- [ ] Edit the website to announce the new release
  - [ ] web project sources
  - [ ] log on the web server, rebuild the web site in the casa-distro installed there: `ssh web`, then in the server:
    - [ ] `web_build/bin/bv bv_maker`
    - [ ] publish the web site using the publish script: `./web-build/src/communication/web/5.1/scripts/bv_publish_web /var/www/html/brainvisa.info`

- [ ] Deploy the `cea` release:
  - [x] Copy `brainvisa-cea-*.sif` and the associated `.json` into `/drf/brainvisa` and set their permissions (`chmod 444`)
  - [x] Create a new directory `/drf/brainvisa/brainvisa-cea-5.1.2` and install the Singularity release in there 
		`mkdir -p /drf/brainvisa/test-brainvisa-5.1.2;apptainer run -ce --bind /drf/brainvisa/brainvisa-5.1.2:/casa/setup /drf/brainvisa/brainvisa-5.1.2.sif`
  - [x] Remove the home directory to enable per-user home: `rm -r /drf/brainvisa/brainvisa-cea-5.1.2/home/`
  - [x] Verify that the deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
        `for __f in $(find /casa/install/share/ -type f -name '*.ima'); do AimsFileInfo -v -i "${__f}"; done`
        `anatomist $(find /casa/install/share/ -type f -name '*.nii')`
		`brainvisa`
  - [ ] Make it the default version: `ln -sfT brainvisa-cea-5.1.2/ /drf/brainvisa/brainvisa` and `ln -sfT brainvisa-cea-5.1.2/ /drf/brainvisa/brainvisa-5.1`
  - [ ] ~Copy `brainvisa-cea-*.ova` and the associated `.json` into `/drf/brainvisa` and set their permissions (`chmod 444`)~
  - [ ] ~Install the `ova` on a non-Linux machine and verify that it works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)~


- [x] Build pip packages for the python projects soma-workflow, soma-base, populse_db and capsul which are distributed in pip:
  - [x] `./brainvisa-5.1-5.3/bin/bv python3 -m build /casa/host/src/soma/soma-workflow/5.1`
  - [x] `./brainvisa-5.1-5.3/bin/bv python3 -m build /casa/host/src/soma/soma-base/5.1`
  - [x] `./brainvisa-5.1-5.3/bin/bv python3 -m build /casa/host/src/populse/populse_db/5.1`
  - [x] `./brainvisa-5.1-5.3/bin/bv python3 -m build /casa/host/src/capsul/5.1`

- [ ] Publish them to `test.pypi.org`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/soma/soma-workflow/5.1/dist/*`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/soma/soma-base/5.1/dist/*`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/populse/populse-db/5.1/dist/*`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/capsul/5.1/dist/*`

- [ ] Create a virtualenv test environment and test packages install:
  - [ ] `mkdir -p /tmp/brainvisa-5.1.2/testenv && python3 -m venv /tmp/brainvisa-5.1.2/testenv`
  - [ ] `/tmp/brainvisa-5.1.2/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ soma-workflow`
  - [ ] `/tmp/brainvisa-5.1.2/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ soma-base`
  - [ ] `/tmp/brainvisa-5.1.2/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ populse-db`
  - [ ] `/tmp/brainvisa-5.1.2/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ capsul`

- [ ] Publish them to `pypi.org`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload /casa/host/src/soma/soma-workflow/5.1/dist/*`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload /casa/host/src/soma/soma-base/5.1/dist/*`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload /casa/host/src/populse/populse-db/5.1/dist/*`
  - [ ] `./brainvisa-5.1-5.3/bin/bv python3 -m twine upload /casa/host/capsul/5.1/dist/*`
  
- [ ] Create tags with `bv_tag_release`

- [ ] [Open a new GitHub issue for known issues](https://github.com/brainvisa/brainvisa.github.io/issues/new?template=known-issues-of-a-brainvisa-release.md&title=Known+issues+for+BrainVISA+5.1.2)

- [ ] Close [the old GitHub known issues](https://github.com/brainvisa/brainvisa.github.io/issues/133)

- [ ] Make an announcement [on the GitHub discussions](https://github.com/brainvisa/brainvisa.github.io/discussions/new)

- [ ] If the releases has significant new features, announce it on the mailing list
