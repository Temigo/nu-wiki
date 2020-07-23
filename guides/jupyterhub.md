---
title: Jupyterhub (Deprecated)
description: 
published: true
date: 2020-07-23T21:43:12.585Z
tags: 
---

# Jupyterhub (deprecated)
You are recommended to use [Open On-Demand (OOD)](/guides/OOD/howto) for accessing SLAC compute using a web-browser. Before OOD, our initial attempt was made using [Jupyterhub](https://jupyterhub.readthedocs.io/en/stable/) and [Kubernetes](https://kubernetes.io). The server is still active and you can use to access SLAC computing resources, but you should be using OOD which supports more flexibility and features.

That being said, below is how you can use Jupyterhub to access SLAC compute.

* Go to [SLAC jupyterhub](https://jupyter.slac.stanford.edu/) + log in with your unix username.
* Spawn `ub18.04-gpu-ana0-ml-larcv2` DeepLearnPhysics image
* Use terminal, Python console, or Jupyter notebook for any interactive work
* Note you get one NVIDIA 2080Ti GPU if you use a jupyterhub option. See FAQ below for more questions.
  * `/scratch` is a local SSD mount with fast IO (not networking involved) available with a few TB space. Create `/scratch/$USER` space for your own use. The space may be wiped out after your job is finished (or could remain for arbitrary period).
  * `/gpfs/slac/staas/fs1/g/neutrino/training` is available as your `$HOME` (or `/home/$USER`), and is a network mounted storage space. You can read/write from this space from your job (and also from ssh log-in nodes). However, unlike the case of `ssh+slurm`, you cannot access other `/gpfs` space that does not belong to the mounted path.
