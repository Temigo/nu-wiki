---
title: Resources
description: 
published: true
date: 2020-07-23T22:30:14.610Z
tags: 
---

# Overview
We break down "how to use SLAC computing?" into 4 steps/categories...

* Gateway: how can I access computing servers for doing my work?
* Storage: where is my space? where to keep my data files?
* Softwares: how can I set up a software stuck? how can I add more softwares?
* Scaling: how to submit batch (=unattended, automated) job to run your computing script?

## Gateway / Access
You can access to the SLAC computing servers either from a web-browser or a terminal. These methods are complementary to each other in strengthe, and you are encouraged to try both methods at least once.

* Access via `ssh` (a terminal-based method). 
  * From your laptop/desktop terminal, you can access SLAC computing servers via [secure shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell). There are several gateway machines at SLAC but the most relevant one is probably `ocio-gpu01`.
```
ssh $USER@ocio-gpu01.slac.stanford.edu
```
* [Access via Open On-Demand](/guides/ood) (a web-browser based method)
* [Access via Jupyterhub](/guides/jupyterhub) (a web-browser based method ... **DEPRECATED**, going away)

## Storage space 
On each server machine, you typically have 3 types of space to work.
* `$HOME` ... `afs` mounted servers, up to 20GB space (default 2GB, [go here](https://www.slac.stanford.edu/comp/unix/auth/afs-self.shtml) to increase for free). While you can access this space across all server machines, it is not recommended to use this space. Use `/gpfs` space below instead.
* `/scratch` ... local RAID0 SSD space for fast read/write (no networking involved), available on most servers but files written in this space will be purged after you log out.
* `/gpfs` ... [GPFS](https://en.wikipedia.org/wiki/IBM_Spectrum_Scale) disk arrays (HDD), TBs, network mounted and accessible across server machines.
    * Create "your `gpfs` space" and feel free to store files underneath.
    ```
    mkdir /gpfs/slac/staas/fs1/g/neutrino/$USER
    ```
    * 10 Gbps on the host, 1 Gbps on a client.
    * 40TB allocated for the whole neutrino group. We can expand at ~$60/TB/year ([how to](https://github.com/NuSLAC/ComputingCookbook/wiki/2.-Request-%22I-want-X%22)). Or we can purchase your dedicated GPFS server (about $70k for 500TB). Note, if you use a shared server, your network is also shared (so the speed depends on other sharing users).

## Software stacks

* **Singularity**
  * At SLAC we recommend the use of a [Singularity container](https://sylabs.io/singularity/) to carry around and setup a software environment. Note if you are using `Open On-Demand`, your session lives inside a singularity container and you should not be following the instructions below. If you logged in using `ssh` or launching `slurm` job, this instruction is relevant.
  * Shared `Singularity` container images can be found at `/gpfs/slac/staas/fs1/g/neutrino/images`.
  * If you aren't sure about `Singularity` or which container to use, you can just try:
  ```
  $> singularity exec --nv --bind /gpfs --bind /scratch /gpfs/slac/staas/fs1/g/neutrino/images/ub18.04-cuda10.2-extra-ME.sif bash
  ```
  * See the official documentation or [here](https://github.com/DeepLearnPhysics/playground-singularity/wiki) for getting started with `Singularity`.

* **CernVM File System (CVMFS)**
  * [CVMFS](https://cernvm.cern.ch/portal/filesystem) is also available at SLAC. Anyone who uses it should help documenting here.

## Scaling data processing
You can use [slurm](https://slurm.schedmd.com/documentation.html) to submit your computing job to many computing servers. [Here](https://confluence.slac.stanford.edu/display/SCSPub/Slurm+Batch) is a documentation of how to use `slurm` at SLAC. You can submit an interactive job (literally it's like `ssh` into another machine) using `srun` or submit unattended jobs to run in the background using `sbatch`.

