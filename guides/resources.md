---
title: Resources
description: 
published: true
date: 2020-09-08T17:28:44.726Z
tags: 
---

# Overview
Computing resources are made available at SLAC Data Facility (SDF). The [official documentation of SDF](https://ondemand-dev.slac.stanford.edu/public/doc/#/getting-started) is available though it is also under development still as of September 2020. This page provides a complementary (and a bit of duplicate) documentation to the official SDF one with an aim to guide neutrino users. In particular this covers 4 categories...

* Gateway: how can I access computing servers for doing my work?
* Storage: where is my space? where to keep my data files?
* Softwares: how can I set up a software stuck? how can I add more softwares?
* Scaling: how to submit batch (=unattended, automated) job to run your computing script?

## Gateway / Access
You can access to SDF either from a web-browser or a terminal. These methods are complementary to each other in strengthe, and you are encouraged to try both methods at least once.

* Access via `ssh` (a terminal-based method). 
  * From your laptop/desktop terminal, you can access SDF via [secure shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell). There are several gateway machines at SLAC but the most relevant one is probably `sdf-login` and [submit slurm jobs](https://ondemand-dev.slac.stanford.edu/public/doc/#/batch-compute?id=slurm-basics).
```
ssh $USER@sdf-login.slac.stanford.edu
```

* [Access via Open On-Demand](/guides/ood) (a web-browser based method)

## Storage space 
On each server machine, you typically have 3 types of space to work.
* `$HOME` ... 25GB space by default.
    * Network mounted = accessible from different machines.
* `/gpfs` ... [GPFS](https://en.wikipedia.org/wiki/IBM_Spectrum_Scale) disk arrays (HDD), TBs, network mounted and accessible across server machines.
    * Create "your `gpfs` space" and feel free to store files underneath.
    ```
    mkdir /gpfs/slac/staas/fs1/g/neutrino/$USER
    ```
    * 10 Gbps on the host, 1 Gbps on a client.
    * 40TB allocated for the whole neutrino group. We can expand at ~$60/TB/year ([how to](https://github.com/NuSLAC/ComputingCookbook/wiki/2.-Request-%22I-want-X%22)). 
* `/lscratch` ... local RAID0 SSD space for fast read/write (no networking involved).
  * Local disk = not accessible from different machines.
  * Files written in this space will be purged after you log out (i.e. per job).
  * This is `$LSCRATCH` in [official documentation](https://ondemand-dev.slac.stanford.edu/public/doc/#/getting-started?id=disk).
  * Create "your local scratch space" and feel free to store files underneath.
  ```
  mkdir /lscratch/$USER
  ```
* `/scratch` ... global (network mounted) scratch space.
  * Network mounted = accessible from different machines
  * Faster than `/gpfs` but could be slower than `/lscratch`. 
  * Purged approximately once a month. 
	* This is `$SCRATCH` in [official documentation](https://ondemand-dev.slac.stanford.edu/public/doc/#/getting-started?id=disk).
  * Create "your global scratch space" and feel free to store files underneath.
  ```
  mkdir /scratch/$USER
  ``` 
* `/sdf/group/neutrino` ... global (network mounted) group space.
  * Network mounted = accessible from different machines
  * Faster than `/gpfs` but could be slower than `/lscratch`. 
  * No purging policy. Keep your usage < 1TB (system does not enforce this limit) 
	* This is `$GROUP` in [official documentation](https://ondemand-dev.slac.stanford.edu/public/doc/#/getting-started?id=disk).
  * Create "your group space" and feel free to store files underneath.
  ```
  mkdir /sdf/group/neutrino/$USER
  ``` 

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

