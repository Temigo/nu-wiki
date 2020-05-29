---
title: Resources
description: 
published: true
date: 2020-05-18T21:54:19.132Z
tags: 
---

# Overview (please update this!)
There are CPU and GPU resources at SLAC, which you can access with your unix account. Largely there are 3 types of machines.
* `ssh` client servers ... gateway machines to be accessed via ssh but not for computing, could be for slurm batch job submission.
* `gifs` storage servers ... network-mounted HDD storage server. Network speed is limited to 1Gb/s upload (write) and 10Gb/s download (read).
* `slurm` batch servers ... many computing servers (CPU/GPU) put together to which you can submit your jobs.
* `bullet` cluster ... a small scale HPC system with distributed filesystem.

## Typical workflow
Most (if not all) computing by our group at SLAC is for machine learning research using GPUs. A typical workflow is to have a software to develop, data access to a storage server, There are two typical user workflows:
* **SSH**
    * `ssh $USER@ocio-gpu01.slac.stanford.edu` then `module load slurm`.
    * Use `srun` to launch an interactive session (job) for an interactive R&D
    * Use `sbatch` to submit batch jobs for streamlined workflow
    * Use our `Singularity` images (`/gpfs/slac/staas/fs1/g/neutrino/images`) to run your job with `run` or `sbatch`. See [here](https://github.com/DeepLearnPhysics/playground-singularity/wiki) for getting started with `Singularity`.
    * See [slurm at SLAC](https://confluence.slac.stanford.edu/display/SCSPub/Slurm+Batch) for more details.
    * Storage
        * `/scratch` is a local SSD mount with fast IO (not networking involved) available with a few TB space. Create `/scratch/$USER` space for your own use. The space may be wiped out after your job is finished (or could remain for arbitrary period).
        * `/gpfs/slac/staas/fs1/g/neutrino` is available as a network mounted storage space. You can read/write from this space from your job (and also from ssh log-in nodes).
* **Jupyterhub**
    * Go to [SLAC jupyterhub](https://jupyter.slac.stanford.edu/) + log in with your unix username.
    * Spawn `ub18.04-gpu-ana0-ml-larcv2` DeepLearnPhysics image
    * Use terminal, Python console, or Jupyter notebook for any interactive work
    * Note you get one NVIDIA 2080Ti GPU if you use a jupyterhub option. See FAQ below for more questions.
        * `/scratch` is a local SSD mount with fast IO (not networking involved) available with a few TB space. Create `/scratch/$USER` space for your own use. The space may be wiped out after your job is finished (or could remain for arbitrary period).
        * `/gpfs/slac/staas/fs1/g/neutrino/training` is available as your `$HOME` (or `/home/$USER`), and is a network mounted storage space. You can read/write from this space from your job (and also from ssh log-in nodes). However, unlike the case of `ssh+slurm`, you cannot access other `/gpfs` space that does not belong to the mounted path.

### Permission issues (do this first if you ssh!)
AFAIK, when you log into an interactive node with slac.stanford.edu domains, you have to run `kinit` and `aklog` (in respective order) to be able to actually execute your **write** permission in your AFS space which includes your `$HOME` directory. To be extra clear, without those commands, even if you have a write permission to a file or a directory, you cannot execute your permission. **This becomes a problem when you use a GPU** because some part of CUDA requires you to have write permission in your `$HOME` directory.

## Storage space 
On each server machine, you typically have 3 types of space to work.
* `SSH $HOME` ... `afs` mounted servers, up to 20GB space (default 2GB, [go here](https://www.slac.stanford.edu/comp/unix/auth/afs-self.shtml) to increase).
* `/scratch` ... local SSD space for fast read/write (no networking involved), available on most servers, all `slurm` workers.
* `/gpfs` ... [GPFS](https://en.wikipedia.org/wiki/IBM_Spectrum_Scale) disk arrays (HDD), TBs, network mounted
    * 10 Gbps on the host, 1 Gbps on a client.
    * You can buy space from a shared SLAC GPFS server at ~$60/TB/year ([how to](https://github.com/NuSLAC/ComputingCookbook/wiki/2.-Request-%22I-want-X%22)). Or you can purchase your dedicated GPFS server (about $70k for 500TB). Note, if you use a shared server, your network is also shared (so the speed depends on other sharing users).

## Login/Submission servers
The servers from which you can submit `slurm` jobs (i.e. submit jobs to queues, more in the [SLAC slurm manual](https://confluence.slac.stanford.edu/display/SCSPub/Slurm+Batch) are `ocio-gpu01` temporarily. If this does not work, [contact kazu](mailto:kterao@slac.stanford.edu).

A generic gateway for all SLAC users is `centos7.slac.stanford.edu` (as far as I know). Logging into this domain will be subject to a _load balancing_ among 4 underlying server machines including `cent7a`, `cent7b`, `cent7c`, and `cent7d`.

## `slurm` batch resources
The details can be found in the [SLAC slurm manual](https://confluence.slac.stanford.edu/display/SCSPub/Slurm+Batch). But here's a surface summary of available resource.

* `nu-gpu0X` ... 3 servers, each server with x4 NVIDIA V100 (32GByte) with NVLINK2 interconnect.
* `ocio-gpu5X` ... 2 servers, each with x4 NVIDIA V100 (32GByte) with NVLINK2 interconnect. 
* `hep-gpu0X` ... 3 servers, one server (`hep-gpu01`) with x10 NVIDIA 1080Ti (11GByte) and two servers with x10 NVIDIA 2080Ti (11GByte) with PCI-e interconnect.
* `ml-gpuXX` ... 11 servers, each with x10 NVIDIA 2080Ti (11GByte) with PCI-e interconnect.
* `cryoem-gpuXX` ... 10 servers with x10 NVIDIA 1080Ti (11GByte) + 5 servers with x10 NVIDIA 2080Ti (11GByte) with PCI-e interconnect. 1 server (`cryoem-gpu50`) with x4 NVIDIA v100 (32GByte) with NVLINK2 interconnect.
* `lcls-gpu01` ... this I actually don't know...

In summary, there are 290 NVIDIA 1080Ti/2080Ti GPUs across 29 servers + 24 NVIDIA V100 GPUs across 6 servers available to `slurm` users.  You can find a list of servers to available *slurm partition* by executing `sinfo` on `ocio-gpu01` submission node.

## Bullet cluster
I have no freaking idea. Leon can maybe help us...