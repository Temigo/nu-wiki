---
title: Open On-Demand
description: 
published: true
date: 2020-07-28T15:41:11.093Z
tags: 
---

# Open On-Demand (OOD)
[Open On-Demand](https://openondemand.org) (OOD) is a software to make HPC resource for an interactive development through a web browser. At SLAC, we use OOD to connect developers to SLAC/Stanford/Scientific Computing Facility (SDF) resources including CPU and GPU clusters. 

This page explains how to launch your Jupyterlab interactive development environment (IDE), which gives you an access to a terminal, Python interpreter, and Jupyter notebooks, all through single tab in your web-browser.

## Accessing OOD from your web-browser

> **One-time preparation**: you need to do below only before the 1st time use of OOD.
>   * Log into `ocio-gpu01` (`ssh $USER@ocio-gpu01.slac.stanford.edu`)
>   * Follow the commands below
>   ```
>   $> mkdir -p /gpfs/slac/staas/fs1/g/neutrino/$USER
>   $> sh /gpfs/slac/staas/fs1/g/jupyter/ood/convert.sh
>   ```

>**Step 1**: go to [SLAC ondemand](https://sdf.slac.stanford.edu) + log in with your unix username

>**Step 2**: on the page-top tabs, `Interactive Apps` => `Jupyter`.

>**Step 3**: configure your _job instance_ to run a Jupyter lab.
>   - **Jupyter Instance**: this drop-down menu let you choose which `singularity` container (=software stack and work environment) to use. Read more about `singularity` in the later sections. If you are unsure, select `neutrino-jupyter/ub18.04-cuda10.2-extra` option.
>   - **Use JupyterLab instead of Jupyter Notebook?**: mark this check-box (unless you know you shouldn't).
>   - **Disable JupyterLab extensions (Run with --core-mode)**: leave unchecked.
>   - **Account**: if unsure, type `ml`.
>   - **Number of hours**: unsure, type `2`. This is the number of hours your _job session_ lasts.
>   - **Number of CPU cores**: unsure, type `2`. This is the number of cores for your job.
>   - **Total Memory to allocate**: use a dropdown to navigate. Unsure, try 19968 (~20GB).
>   - **Number of GPUs**: choose `0` unless you need a GPU (`1` if you do).
>   - **I would like to receive an email when the session starts**: leave unchecked since your session probably starts immediately.

>* **Step 4**: launch your job by clicking **Launch** button!

After the last step, you should be automatically re-directed to a space that lists your live interactive sessions. Or you can follow, on the [SLAC ondemand](https://sdf.slac.stanford.edu) webpage, you can follow the top tab saying "[My Interactive Sessions](https://sdf.slac.stanford.edu/pun/sys/dashboard/batch_connect/sessions)". You can launch multiple `Jupyter` instances and they will be all listed here. They will last till your lifetime (i.e. what you set in configuration) expires or the system crashes.

**If you are done with your job, click "Delete" button** to release computing resources taken by your job, so it becomes available to others.

For questions about the configuration options, look at the FAQs at the bottom of this page.

## Using OOD
* You have to first spwan your Jupyter session (follow the steps in the above section). You can list your active sessions under the "[My Interactive Sessions](https://jupyterlab.readthedocs.io/en/stable/)" on the OOD page. Click "Connect to Jupyter" button to access an active session. 

* If you followed instructions, you should be in a [Jupyterlab](https://jupyterlab.readthedocs.io/en/stable/) session. The file browser on the left is listing the contents of your `/gpfs` area (see [Storage] section), or more precisely `/gpfs/slac/staas/fs1/g/neutrino/$USER`.

* You can spawn any of three process types within the `Jupyterlab`: terminal, python, and Jupyter notebook.
  
* You have an access to a network-mounted shared space (`/gpfs` area) as well as a local `/scratch` area, which is 6TB RAID0 SSD for fast disk I/O. You cannot write a file in your `$HOME` area, so create your work area under either `/gpfs` or `/scratch`. Your files under `/scratch` area will be automatically purged shortly after your session expres. Keep your important belongings under `/gpfs` area.

## FAQs
**Q: Do I need to keep my web-browser open to keep my session running?**
  * No. You can close your web-browser, re-open and your session remains there. You can access from any computer including your smartphone and a tablet.
  
**Q: What is the Account in OOD configuration?**
  * This is `slurm` account/partition that manages a set of GPUs for you to be able to access. `ml` partition lets you access a GPU cluster of 20 servers, each equipped with x10 NVIDIA RTX 2080Ti GPUs. `neutrino` partition allows you to access a cluster of 3+1 servers where 3 servers are equipped with x4 NVIDIA Tesla V100 GPUs and 1 server with x10 NVIDIA RTX 2080Ti GPUs. Unless you have a valid reason, stick with `ml` partition. Finally the `shared` partition can access all GPUs opportunistically, but **`shared` is not recommended** currently due to a bug in `slurm`. 

**Q: How long can I set the number of hours?**
  * You can set to what you need, but often you don't know that beforehand. I would say probably occupying a node with 1 GPU won't put pressure on available resource for everyone, so you can set it a longer time (like 8 to 12 hours) without worrying too much. If you have a good reason (i.e. training a ML model that may take days), feel free to set an appropraite number of hours.

**Q: How can I install additional softwares?**
  * In general three ways to go about it. 
    1. In general, you can download any software (and build, if necessary) at your local area. You cannot use `$HOME` area for installation but any non-`afs` areas can work including `/gpfs`.
    2. As a specific case of 1, you can use `pip install --user` (covered below).
    3. Build your own `singularity` image with custom software stack (covered below).

**Q: How can I use `pip` to install additional software?**
  * `pip install` commands by default attempts to install a software under system paths, which require you to have an admin priviledge (and you don't have it on OOD). You can instead use `--user` option flag to install your package under your user area, namely `$HOME/.local`. But recall your `$HOME` is `afs` area and you cannot install there. The trick is to separately prepare an installation destination somewhere else, then make `$HOME/.local` a symbolic link pointing to it. Here's an example:
    * If don't need to keep `$HOME/.local` or not sure whether this exists:
    ```
    rm -rf $HOME/.local
  	mkdir -p /gpfs/slac/staas/fs1/g/neutrino/$USER/python_libs
    ln -s /gpfs/slac/staas/fs1/g/neutrino/$USER/python_libs $HOME/.local
    ```
    * If you have `$HOME/.local` already and want to keep it:
    ```
    mv $HOME/.local /gpfs/slac/staas/fs1/g/neutrino/$USER/python_libs
    ln -s /gpfs/slac/staas/fs1/g/neutrino/$USER/python_libs $HOME/.local
    ```
	... by doing this, when `pip` attempts to install a software under `$HOME/.local`, it actually installs in the destination path which is under `/gpfs`. This avoids causing an error from attempting to write under `afs` mounted storage area such as `$HOME`.

**Q: Can I use my own `singularity` image?**
  * Yep. On the drop-down menu of "**Jupyter Instance**", you should seee "Custom Singularity Image". Choose that. Then you should see "**Commands to initiate Jupyter**" text area editable. There, you can type the commands that are executed by the ondemand process within your job. For instance, below is what's set for the `neutrino-jupyter/ub18.04-cuda10.2-extra` image. You see `$SINGULARITY_IMAGE_PATH` is set, and you can change that path to your image path to launch a session with your own image.
  ```
  $> export SINGULARITY_IMAGE_PATH=/gpfs/slac/staas/fs1/g/neutrino/images/larcv2_ub18.04-cuda10.2-extra.sif
  $> export MY_SINGULARITY_WORKDIR=/gpfs/slac/staas/fs1/g/neutrino/$USER
  $> function jupyter() { singularity exec --nv -B /gpfs,/scratch,/nfs,/afs ${SINGULARITY_IMAGE_PATH} jupyter $@ --notebook-dir $MY_SINGULARITY_WORKDIR; }
  ```

**Q: How many CPU cores can I request?**
  * Well, up to you. One consideration point is the total number of cores per a server, which ranges 32 to 48 (CPU HT cores). A server with 32 cores host 4 GPUs, and ones with 48 cores host 10 GPUs. A typical GPU job occupy 1 GPU, so these servers tend to host 4 and 10 GPU jobs respectively. As such, if those jobs split CPUs equally, typically 8 and 4 CPU per job respectively. So 4 CPU request would be totally reasonable. For a typical ML job in our group, you probably want 4 for data loaders to keep up with GPU compute.
  
**Q: How much memory can I request?**
  * Using a similar logic from our discussion about CPU cores, a reasonable request amount is the total memory divided by the number of GPUs in a server. Each server is equipped with 192GB memory so ~19GB is a reasonable request for each job on a server with x10 GPUs, or ~45GB fora server with x4 GPUs.
  
**Q: How many GPUs can I request?**
  * Well, up to you. For a typical ML job, you need only 1 GPU. 
  

