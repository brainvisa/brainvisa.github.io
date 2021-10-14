---
name: "[Reserved for BrainVISA admins: Release check-list]"
about: For the @brainvisa/admin team to coordinate a release
title: Make the BrainVISA [x.y.z] release
labels: ''
assignees: ''

---

# Release check-list

- [ ] Check that all projects have the correct version in their sources: `bv_tag_release.py --fix-source-version-numbers`

- [ ] Check that tests are successful on https://brainvisa.info/builds/

- [ ] Update changelogs and [the list of known issues](https://github.com/brainvisa/brainvisa.github.io/issues?q=%22Known+issues+of+BrainVISA%22+is%3Aissue+is%3Aopen)
  - [ ] https://github.com/brainvisa/aims-free/blob/5.0/aimsdata/doc/html/en/changelog.html
  - [ ] https://github.com/brainvisa/axon/blob/5.0/doc/en/help/changelog.html
  - [ ] https://github.com/brainvisa/anatomist-free/blob/5.0/doc/html/en/changelog.html

- [ ] Run a last build to update the changelogs 
  - [ ] `bv_maker sources doc` in the `brainvisa` distro
  - [ ] `bv_maker sources doc` in the `cea` distro

- [ ] Create, test, and publish/deploy the images
  - `sif` image for the `brainvisa` distro
    - [ ] Create the image with `casa_distro_admin create_user_image`
    - [ ] Verify that the image works
      - Install the image
      - Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
    - [ ] Publish the image on the BrainVISA web site

  - `ova` image for the `brainvisa` distro
    - [ ] Create the image with `casa_distro_admin create_user_image`
    - [ ] Verify that the image works
      - Install the image
      - Use it to run `AimsFileInfo`, `anatomist`, `brainvisa`
    - [ ] Publish the image on the BrainVISA web site

  - `sif` image for the `cea` distro
    - [ ] Create the image with `casa_distro_admin create_user_image`

  - `ova` image for the `cea` distro
    - [ ] Create the image with `casa_distro_admin create_us

- [ ] Create tags with `bv_tag_release.py`

- [ ] Edit the website to announce the new release

- [ ] Deploy the `cea` release:
  - [ ] Copy `brainvisa-cea-*.sif` and the associated `.json` into `/i2bm/brainvisa`
  - [ ] Create a new directory `/i2bm/brainvisa/brainvisa-cea-x.y.z` and install the Singularity release in there (`singularity run --bind ...`)
  - [ ] Edit `/i2bm/brainvisa/brainvisa-cea-x.y.z/conf/casa_distro.json` and add `"container_failure_message": "Singularity could not start. BrainVISA needs singularity version %(singularity_version)s or later. You can install it (as admin) by typing in a terminal:\n. /etc/os-release && sudo dpkg -i /i2bm/brainvisa/singularity-latest-${ID}-${VERSION_ID}_$(dpkg --print-architecture).deb"`
    - [ ] By the way, check that the `/i2bm/brainvisa/singularity-latest*` symlinks are working and up-to-date
  - [ ] Verify that the deployment works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)
  - [ ] Make it the default version: `ln -sfT brainvisa-cea-x.y.z/ /i2bm/brainvisa/brainvisa`
  - [ ] Copy `brainvisa-cea-*.ova` and the associated `.json` into `/i2bm/brainvisa`
  - [ ] Install the `ova` on a non-Linux machine and verify that it works (use it to launch `AimsFileInfo --info`, `anatomist`, `brainvisa`...)

- [ ] Make an announcement [on the GitHub discussions](https://github.com/brainvisa/brainvisa.github.io/discussions/new)

- [ ] If the releases has significant new features, announce it on the mailing list
