# Irene specifications

## Partitions

An overview of partitions specifications of the supercomputer Irene, as described in the official documentation, and some insights for Brainvisa use.

### Skylake

Intended for regular computation.

#### Specs

- __Partion name__: skylake
- __CPUs__:  2x24-cores Intel Skylake@2.7GHz (AVX512)
- __Cores/Node__: 48
- __Nodes__: 1 656
- __Total cores__: 79 488
- __RAM/Node__: 180GB
- __RAM/Core__: 3.75GB

### KNL

Intended for regular computation.

#### Specs

- __Partition name__: knl
- __CPUs__: 1x68-cores Intel KNL@1.4GHz
- __Cores/Node__: 64 (With 4 additional cores reserved for the operating system. They are referenced by the scheduler but not taken into account for hours consumption.)
- __Nodes__: 828
- __Total cores__ : 52 992
- __RAM/Node__: 96GB
- __RAM/Core__: 1.4GB
- Cluster mode is set to quadrant
- MCDRAM (Multi-Channel Dynamic Random Access Memory) is set as a last-level cache (cache mode)

### Rome

Intended for regular computation.

#### Specs

- __Partition name__ : Rome
- __CPUs__: 2x64 AMD Rome@2.6Ghz (AVX2)
- __Core/Node__: 128
- __Nodes__: 2292
- __Total core__: 293376
- __RAM/Node__: 256GB
- __RAM/core__ : 2GB

### Hybrid

Intended for GPU computing and graphical usage.

#### Specs

- __Partition name__: hybrid
- __CPUs__: 2x24-cores Intel Skylake@2.7GHz (AVX512)
- __GPUs__: 1x Nvidia Pascal P100
- __Cores/Node__: 48
- __Nodes__: 20
- __Total cores__: 960 (+ 20 GPU)
- __RAM/Node__: 192GB
- __RAM/Core__: 4GB
- __I/O__: 1 HDD 250 GB + 1 SSD 800 GB/NVMe

### Fat

Intended for high shared memory needs but the computation should last a reasonable amount of time and use only one node.

#### Specs

- __Partition name__: xlarge
- __CPUs__: 4x28-cores Intel Skylake@2.1GHz
- __GPUs__: 1x Nvidia Pascal P100
- __Cores/Node__: 112
- __Nodes__: 5
- __Total cores__: 560
- __RAM/Node__: 3TB
- __RAM/Core__: 27GB
- __IO__: 2 HDD de 1 TB + 1 SSD 1600 GB/NVMe

### V100

Intended for GPU computing and AI.

#### Specs

- __Partition name__: V100
- __CPUs__: 2x20-cores Intel Cascadelake@2.1GHz (AVX512)
- __GPUs__: 4x Nvidia Tesla V100
- __Cores/Node__: 40
- __Nodes__: 32
- __Total cores__: 1280 (+ 128 GPU)
- __RAM/Node__: 180 GB
- __RAM/Core__: 4.5 GB

### ARM A64FX

Intended for regular computation.

#### Specs

- __Partition name__: A64FX
- __CPUs__: 1x48 A64FX Armv8.2-A SVE @2.2Ghz
- __Core/Node__: 48
- __Nodes__: 80
- __Total core__: 3840
- __RAM/Node__: 34GB
- __RAM/core__: 698MB

## Comments

- Skylake, Knl, Fat and Hybrid are only visible from Irene login, although Rome and V100 partitions are only visible from Irene AMD login.
