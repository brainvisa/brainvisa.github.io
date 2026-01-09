---
title: Packaging for conda how to
---

# {{page.title}}

Conda packaging of brainvisa projects is based on the [soma-env](https://github.com/brainvisa/soma-env) project.
For maintenability purpose, two distinct branches of the brainvisa projects can be packaged :
* The stable branches (using soma-env version 6.0). It is used to release stable versions. Only minor fixes are done on these project branches.
* The development branches (using soma-env version 0.0). It is used to release latest developments. Unstable fixes or features can be integrated to development packages.

Publication of neuro-forge packages to [brainvisa.info](https://brainvisa.info) is managed using the [neuro-forge](https://github.com/neurospin/neuro-forge) project.

## Stable release packaging
When we want to perform a release and publish conda packages of a distribution, the following steps should be done:

* setup a soma-env-6.0 environment (cf https://github.com/brainvisa/soma-env) or use the one setup on rosette
```
	ssh a-sac-ns-brainvisa@rosette
	cd /home_local/a-sac-ns-brainvisa/bbi-daily/soma-env-6.0
```
* update sources and build software tree
```
	pixi run bv_maker
```
* update sources and build software tree
```
	pixi run bv_maker
```
* create a packaging plan: create a packaging plan that determine which conda packages should be built and published to /drf/neuro-forge.
```
	pixi run soma-env packaging_plan --release
```
* apply packaging plan: build changed conda packages and publish packages to .
```
	pixi run soma-env apply_plan
```

* setup a neuro-forge environment (cf https://github.com/neurospin/neuro-forge) or use the one setup on rosette
```
	ssh a-sac-ns-brainvisa@rosette
	cd /home_local/a-sac-ns-brainvisa/bbi-daily/neuro-forge
```

* build conda packages index and publish packages to brainvisa.info.
```
	pixi run neuro-forge publish
```
	

## Development packaging
When we want to package and publish development conda packages, the following steps should be done:

* setup a soma-env-0.0 environment (cf https://github.com/brainvisa/soma-env) or use the one setup on rosette
```
	ssh a-sac-ns-brainvisa@rosette
	cd /home_local/a-sac-ns-brainvisa/bbi-daily/soma-env-0.0
```
* update sources and build software tree
```
	pixi run bv_maker
```
* create a packaging plan: create a packaging plan that determine which conda packages should be built and published to /drf/neuro-forge.
```
	pixi run soma-env packaging_plan --release
```
* apply packaging plan: build changed conda packages and publish packages to .
```
	pixi run soma-env apply_plan
```

* setup a neuro-forge environment (cf https://github.com/neurospin/neuro-forge) or use the one setup on rosette
```
	ssh a-sac-ns-brainvisa@rosette
	cd /home_local/a-sac-ns-brainvisa/bbi-daily/neuro-forge
```

* build conda packages index and publish packages to brainvisa.info.
```
	pixi run neuro-forge publish
```
