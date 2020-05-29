---
title: Software Containers
description: 
published: true
date: 2020-05-18T21:09:05.949Z
tags: 
---

We maintain singularity containers for shared software development environment as well as reproducible data processing. This page summarizes a list of containers publicly available and maintained for the group. For ML work, Pytorch is our current base ML library (though we deviate, was tensorflow in 2018).

## Docker containers on the hub
You can find the full disclosure on [this docker hub page](https://hub.docker.com/repository/docker/deeplearnphysics/larcv2/general).
For GPU servers (larger image)
* **ub18.04-gpu** ... (**2.1GB**) ... the base image built on top of NVIDIA cuda10.0 + root v6.16.00
* **ub18.04-gpu-ana0** ... (**2.4GB**) ... + various typical python analysis libraries (scipy/scikit/matplotlib/h5py/plotly/dash/jupyter/etc.)
* **ub18.04-gpu-ana0-ml** ... (**3.2GB**) ... + pytorch v1.1.0, sparse convnet, pytorch_geometric
* **ub18.04-gpu-ana0-ml-larcv2** ... (**3.3GB**) ... + larcv2

For non-GPU servers
* **ub18.04-cpu** ... (**0.6GB**) ... the base image with root v6.16.00
* **ub18.04-cpu-ana0** ... (**0.9GB**) ... + various typical python analysis libraries (scipy/scikit/matplotlib/h5py/plotly/dash/jupyter/etc.)
* **ub18.04-cpu-ana0-larcv2** ... (**1.0GB**) ... + larcv2

Example:
```
$> sudo docker run deeplearnphysics/larcv2:ub18.04-cpu-ana0 python3 -c "import numpy;print(numpy.__version__)"
```

## Singularity containers on the hub
Some Docker containers are pulled and converted into Singularity containers and available on the hub:
* **ub18.04-cpu-ana0** ... [on the hub](https://singularity-hub.org/containers/11760)
    * e.g. `singularity pull ub18.04-cpu-ana0 shub://DeepLearnPhysics/larcv2-singularity:ub18.04-cpu-ana0` 
* **ub18.04-gpu-ana0-ml** ... [on the hub](https://singularity-hub.org/containers/11758)
    * e.g. `singularity pull ub18.04-gpu-ana0-ml shub://DeepLearnPhysics/larcv2-singularity:ub18.04-gpu-ana0-ml`
* **ub18.04-gpu-ana0-ml-larcv2** ... [on the hub](https://singularity-hub.org/containers/11757)
    * e.g. `singularity pull ub18.04-gpu-ana0-ml-larcv2 shub://DeepLearnPhysics/larcv2-singularity:ub18.04-gpu-ana0-ml-larcv2`

## Singularity containers copy for servers on SLAC network
The singularity container images are downloaded to `/gpfs/slac/staas/fs1/g/neutrino/images/` and are available to processes running on most server machines within the SLAC network.