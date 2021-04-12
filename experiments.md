---
title: LArTPC Experiments
description: Useful numbers
published: true
date: 2021-04-12T23:51:41.647Z
tags: 
editor: markdown
dateCreated: 2020-05-18T23:17:11.101Z
---

# Accessing samples
Fermilab uses SAM to interact with samples. You need to authenticate to use it:
```
$ kinit
$ kx509
```
https://sbnsoftware.github.io/icaruscode_wiki/samples/MCproduction.html
You may need to "pre-stage" a sample before you can use it.
https://cdcvs.fnal.gov/redmine/projects/icarus-production/wiki/How_to_pre-stage_files_and_check_if_you_need_to_do_it

Also see [how to setup and produce ICARUS data](/guides/icarus).

# Experiment-specific numbers
* [ICARUS](/experiments/icarus)

### General
Cosmics rate: 30kHz


### Light and photons
1 MeV Deposition Photon Count: 24000 photons/MeV (for muon, particle dependent)

Liquid Argon Photon Property (prompt and late light):

* fraction $\alpha=0.23$ of photons goes to $\tau_f = 6 ns$ (**prompt light**).
* fraction $\beta=0.77$ of photons goes to $\tau_s = 1.5 \mu s$. (**late light**) 

Time probability distribution of photons
$\alpha \exp^{-t/\tau_f} + \beta \exp^{-t/\tau_s}$