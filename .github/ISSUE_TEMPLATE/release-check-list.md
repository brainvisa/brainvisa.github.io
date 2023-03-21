---
name: Release check-list
about: RESERVED for the @brainvisa/admin team to coordinate a release
title: Make the BrainVISA [x.y.z] release
labels: ''
assignees: ''

---

# Release check-list

- [ ] In this new issue, replace templated versions
  - [ ] Replace tag release version x.y and x.y.z with the new release version (as an example: 5.1 / 5.1.2)
  - [ ] Replace base image version x.i with the new base image version (as an example: 5.3)

- [ ] Check that all projects have the correct version in their sources: `bv_tag_release --fix-source-version-numbers`
- [ ] If needed, fix project versions in their sources: `GIT_AUTHOR_NAME='<author_name>' GIT_COMMITTER_NAME='<committer_name>' GIT_AUTHOR_EMAIL='<author_email>' GIT_COMMITTER_EMAIL='<committer_email>' bv_tag_release --fix-source-version-numbers --really-do-it`

- [ ] Check that tests are successful on https://brainvisa.info/builds/

- [ ] Update changelogs and [the list of known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+of+BrainVISA%22+is%3Aissue+is%3Aopen)
  - [ ] https://github.com/brainvisa/aims-free/blob/x.y/aimsdata/doc/html/en/changelog.html
  - [ ] https://github.com/brainvisa/axon/blob/x.y/doc/en/help/changelog.html
  - [ ] https://github.com/brainvisa/anatomist-free/blob/x.y/doc/html/en/changelog.html
  - [ ] https://bioproj.extra.cea.fr/redmine/projects/brainvisa-commu/repository/web/revisions/master/entry/sphinx/changelog.rst

- [ ] Run a last build to update the changelogs 
  - [ ] `bv_maker sources doc` in the `brainvisa` distro
  - [ ] `bv_maker sources doc` in the `cea` distro

- [ ] Create, test, and publish/deploy the images
  - `sif` image for the `brainvisa` distro
    - [ ] Create the image with `./brainvisa-x.y-x.i/bin/casa_distro_admin create_user_image version=x.y.z container_type=singularity distro=brainvisa image_version=x.i branch=x.y install_thirdparty=file:///neurospin/brainvisa/thirdparty/thirdparty.json`
    - [ ] Verify that the image works
      - [ ] Install the image
	        `mkdir -p /tmp/test-brainvisa-x.y.z;singularity run -ce --bind /tmp/test-brainvisa-x.y.z:/casa/setup /volatile/a-sac-ns-brainvisa/bbi_nightly/brainvisa-x.y.z.sif`
      - [ ] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
    - [ ] Publish the image on the BrainVISA web site
	      `casa_distro_admin publish_user_image image=brainvisa-x.y.z.sif`

  - `ova` image for the `brainvisa` distro
    - [ ] Create the image with `./brainvisa-x.y-x.i/bin/casa_distro_admin create_user_image version=x.y.z base_directory=/volatile/a-sac-ns-brainvisa/bbi-brainvisa-x.y install=no container_type=vbox base_image=casa-run-x.i.ova environment_name=brainvisa-x.y-x.i`
    - [ ] Verify that the image works
      - [ ] Install the image
      - [ ] Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
    - [ ] Publish the image on the BrainVISA web site

  - `sif` image for the `cea` distro
    - [ ] Create the image with `./cea-x.y-x.i/bin/casa_distro_admin create_user_image version=x.y.z name=brainvisa-cea-x.y.z container_type=singularity distro=cea image_version=x.i branch=x.y install_thirdparty=file:///neurospin/brainvisa/thirdparty/thirdparty.json` (beware to name the image brainvisa-cea- and not just cea-)

  - ~`ova` image for the `cea` distro~ we will only do these images if users request them
    - [ ] ~Create the image with `./cea-x.y-x.i/bin/casa_distro_admin create_user_image name=brainvisa-cea version=x.y.z base_directory=/volatile/a-sac-ns-brainvisa/bbi_nightly install=no container_type=vbox base_image=casa-run-x.i.ova environment_name=cea-x.y-x.i` (beware to name the image `brainvisa-cea-` and not just `cea-`)~

- [ ] Create tags with `bv_tag_release`

- [ ] Edit the website to announce the new release
  - [ ] web project sources
  - [ ] log on the web server, rebuild the web site in the casa-distro installed there: `ssh web`, then in the server:
    - [ ] `web_build/bin/bv bv_maker`
    - [ ] publish the web site using the publish script: `./web-build/src/communication/web/x.y/scripts/bv_publish_web /var/www/html/brainvisa.info`

- [ ] Deploy the `cea` release:
  - [ ] Copy `brainvisa-cea-*.sif` and the associated `.json` into `/i2bm/brainvisa` and set their permissions (`chmod 444`)
  - [ ] Create a new directory `/i2bm/brainvisa/brainvisa-cea-x.y.z` and install the Singularity release in there (`singularity run --bind ...`)
  - [ ] Remove the home directory to enable per-user home: `rm -r /i2bm/brainvisa/brainvisa-cea-x.y.z/home/`
  - [ ] Edit `/i2bm/brainvisa/brainvisa-cea-x.y.z/conf/casa_distro.json` and add `"container_failure_message": "Singularity could not start. BrainVISA needs singularity version %(singularity_version)s or later. You can install it (as admin) by typing in a terminal:\n. /etc/os-release && sudo dpkg -i /i2bm/brainvisa/singularity-latest-${ID}-${VERSION_ID}_$(dpkg --print-architecture).deb"`
    - [ ] By the way, check that the `/i2bm/brainvisa/singularity-latest*` symlinks are working and up-to-date
  - [ ] Verify that the deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
  - [ ] Make it the default version: `ln -sfT brainvisa-cea-x.y.z/ /i2bm/brainvisa/brainvisa`
  - [ ] ~Copy `brainvisa-cea-*.ova` and the associated `.json` into `/i2bm/brainvisa` and set their permissions (`chmod 444`)~
  - [ ] ~Install the `ova` on a non-Linux machine and verify that it works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)~


- [ ] Build pip packages for the python projects soma-workflow, soma-base, populse_db and capsul which are distributed in pip:
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m build /casa/host/src/soma/soma-workflow/x.y`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m build /casa/host/src/soma/soma-base/x.y`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m build /casa/host/src/populse/populse_db/x.y`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m build /casa/host/src/capsul/x.y`

- [ ] Publish them to `test.pypi.org`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/soma/soma-workflow/x.y/dist/*`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/soma/soma-base/x.y/dist/*`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/populse/populse-db/x.y/dist/*`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload --repository testpypi /casa/host/src/capsul/x.y/dist/*`

- [ ] Create a virtualenv test environment and test packages install:
  - [ ] `mkdir -p /tmp/brainvisa-x.y.z/testenv && python3 -m venv /tmp/brainvisa-x.y.z/testenv`
  - [ ] `/tmp/brainvisa-x.y.z/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ soma-workflow`
  - [ ] `/tmp/brainvisa-x.y.z/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ soma-base`
  - [ ] `/tmp/brainvisa-x.y.z/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ populse-db`
  - [ ] `/tmp/brainvisa-x.y.z/testenv/bin/python3 -m pip install --index-url https://test.pypi.org/simple/ capsul`

- [ ] Publish them to `pypi.org`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload /casa/host/src/soma/soma-workflow/x.y/dist/*`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload /casa/host/src/soma/soma-base/x.y/dist/*`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload /casa/host/src/populse/populse-db/x.y/dist/*`
  - [ ] `./brainvisa-x.y-x.i/bin/bv python3 -m twine upload /casa/host/capsul/x.y/dist/*`
  
- [ ] [Open a new GitHub issue for known issues](https://github.com/brainvisa/brainvisa.github.io/issues/new?template=known-issues-of-a-brainvisa-release.md&title=Known+issues+for+BrainVISA+x.y.z)

- [ ] Close [the old GitHub known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+of+BrainVISA%22+is%3Aissue+is%3Aopen)

- [ ] Make an announcement [on the GitHub discussions](https://github.com/brainvisa/brainvisa.github.io/discussions/new)

- [ ] If the releases has significant new features, announce it on the mailing list
