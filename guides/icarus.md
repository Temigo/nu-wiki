---
title: Instructions to setup and produce ICARUS data
description: 
published: true
date: 2020-05-18T22:01:00.907Z
tags: 
---

## 0. Connect to the ICARUS machine
0. kinit xxx@FNAL.GOV
1. ssh xxx@icarusbuild01.fnal.gov

`icarusbuild01` is a physical server with real local space in `/scratch` unlike all other machines used by the collaboration `icarusgpvm0X` where X = 1..N. On these machines people use `/icarus/data` and `/icarus/app` but the space is small and you can crush the filesystem. We will be using `/scratch` on `icarusbuild01` machine.

## 1. Setup your workspace
2. Create a space under `/scratch/icarus/xxx`
3. Copy setup scripts: `scp -r /scratch/icarus/ldomine/setup /scratch/icarus/xxx/`
	
4. Modify setup/setup.sh script: EDITOR and MY_LARSOFT_VERSION to latest
5. `cd /scratch/icarus/xxx && source setup/setup.sh` will create a directory `mrbdev_vxx_xx_xx` depending on your larsoft version.

**Any time you connect to the machine, begin with `source /scratch/icarus/xxx/setup/setup.sh`.**

## 2. Setup `icaruscode`
6. `cd mrbdev_vXX_XX_XX`
7. `mrbsetenv`
8. `mrb g icaruscode`
9. Edit `srcs/icaruscode/ups/product_deps` to get the right versions of products (according to `ups active`)

## 3. Setup `Supera`
10. `cd srcs/icaruscode/icaruscode`
11. `git clone https://github.com/DeepLearnPhysics/Supera.git`
12. Edit CMakeLists in this directory, add `add_subdirectory(Supera)` at the end
13. `cd Supera && git checkout icarus && sh setup.sh icarus`
14. Create CMakeLists.txt inside `job/` and put one line `install_fhicl()` inside

Try building with `mrb i -j12`

## 4. Setup `larcv2`
15. `cd $MRB_TOP/srcs && git clone https://github.com/DeepLearnPhysics/larcv2.git`
16. `cd larcv2 && git checkout develop && source configure.sh && make -j4`

Then we need to manually copy larcv libraries to the localProducts directory:

`scp build/lib/* $MRB_TOP/localProducts_larsoft_vXX_XX_XX_e17_prof/icaruscode/vXX_XX_XX/slf6.x86_64.e17.prof/lib/`
	
## 5. Build
**Any time you build, make sure you have sourced the `larcv2/configure.sh` and `sh setup.sh icarus` in Supera folder.**

17. `mrb i -j12` to build everything

Note: `mrb z` will clean out the build.

## 6. Launch jobs
PNFS (dcache disk storage) is exposed and recommended to use for grid jobs. This is where we should put our code in a tarball, and where the logs/output will be created. It is much bigger than `/icarus/data`. Regular UNIX command like `mkdir` work but you should learn to use `ifdh` commands. 

**Avoid using \<tab\> completion and `ls` command on PNFS, it can hammer the filesystem.**

The tarball will be copied to the worker node.
`/pnfs/icarus/scratch` are subject to auto-cleanup for files that haven't been touched in a long time. There is also `/pnfs/icarus/permanent` but not recommended (space is limited and shared by everyone, you easily forget to remove files).

18. Create your directories on PNFS filesystem if needed:
`mkdir /pnfs/icarus/scratch/users/xxx && mkdir /pnfs/icarus/scratch/users/xxx/tars`
19. Go back to mrb: `cd $MRB_TOP` and copy other useful scripts: 
	* `scp -r /scratch/icarus/ldomine/v08_30_01/make_tar_uboone.sh $MRB_TOP/`
	* `scp -r /scratch/icarus/ldomine/v08_30_01/link_maker.py $MRB_TOP/`
	* `scp -r /scratch/icarus/ldomine/v08_30_01/v08_30_01.xml $MRB_TOP/`
20. Make tar with all code and put it on PNFS filesystem: 
`sh make_tar_uboone.sh -d localProducts_larsoft_vXX_XX_XX_e17_prof/ /pnfs/icarus/scratch/users/xxx/tars/vXX_XX_XX.tar`
21. Edit the XML file `$MRB_TOP/vXX_XX_XX.xml` to define your jobs/xml file/output name etc.
22. Submit jobs: `project.py --xml vXX_XX_XX.xml --stage larcv --submit`
Monitor jobs: `jobsub_q --user=xxx`
Cancel jobs: `jobsub_rm --job-id=XXXXX or --user=xxx`

## 7. Fetch data
23. Once the jobs are done, make symbolic links and eliminate bad output files: `python link_maker.py vXX_XX_XX.xml`
24. Download @ local/slac machine: 
`rsync -e ssh -r -L --update --progress -v xxx@icarusbuild01.fnal.gov:/icarus/data/users/xxx/dl_production_symlink_v01/mpv_mpr_YOURNAME ./`