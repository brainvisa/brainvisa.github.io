---
title: BBI nightly setup for brainvisa-web how to
version: 6.0
bbi_name: brainvisa-web
bbi_directory: /home_local/a-sac-ns-brainvisa/bbi-daily
---

# {{page.title}}

BrainVISA Build Infrastructure (BBI) is used to nightly build projects.
It is now based on the [soma-env](https://github.com/brainvisa/soma-env) project.

## BBI nightly setup
When we want to setup BBI nightly, the following steps should be done:

* create a soma-env-{{page.version}} environment (cf https://github.com/brainvisa/soma-env) to a new BBI directory on rosette
```
ssh a-sac-ns-brainvisa@rosette
cd {{page.bbi_directory}}
git clone -b {{page.version}} --single-branch https://github.com/brainvisa/soma-env {{page.bbi_name}}
cd {{page.bbi_name}}
pixi run soma-env update
```
* update bv_maker configuration
```
cat > conf/bv_maker.cfg <<EOF
[ source \$SOMA_ROOT/src ]
  soma-env
+ communication master \$SOMA_ROOT/src

[ build \$SOMA_ROOT/build ]
  default_steps = configure build doc
  make_options = -j\$NCPU
  cmake_options += -DPIXI=\$PIXI_PROJECT_ROOT
  build_type = Release
  clean_config = ON
  clean_build = ON
  test_ref_data_dir = \$SOMA_ROOT/test/ref
  test_run_data_dir = \$SOMA_ROOT/test/test

  soma-env
  communication master \$SOMA_ROOT/src
EOF
```
* add a crontab entry
```
( crontab -l 2>/dev/null ; cat <<EOF
47 5 * * * PATH=/home/a-sac-ns-brainvisa/bin:"\$PATH" XDG_CACHE_HOME=/home_local/a-sac-ns-brainvisa/.cache CASA_BASE_DIRECTORY={{page.bbi_directory}} TMPDIR=/home_local/a-sac-ns-brainvisa/tmp /usr/bin/python3 {{page.bbi_directory}}/{{page.bbi_name}}/src/brainvisa-cmake/bin/bbi-daily -b {{page.bbi_directory}} -e {{page.bbi_name}} -j 'https://brainvisa.info/builds' --no-dev_tests --no-user_tests --no-pack --no-update_neuroforge
EOF
) | crontab -
```
