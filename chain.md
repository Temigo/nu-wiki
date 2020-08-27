---
title: Full Chain Reconstruction
description: 
published: true
date: 2020-08-27T00:29:55.475Z
tags: 
---

## Reconstruction stages
1. [UResNet](/chain/uresnet) [Laura/Patrick]
2. [PPN](/chain/ppn) [Laura/Patrick]
3. [Dense clustering](/chain/clustering/dense) [Dae Heun]
4. [Shower clustering](/chain/clustering/shower) [François]
5. [Particle start point and direction](/chain/direction) [none]
6. [Vertex identification](/chain/vertex) [none]
7. [Interaction clustering](/chain/interaction) [Qing/François]
8. [Particle kinematics](/chain/kinematics) [none]

## [Questions and Answers](/chain/questions)

## Citations
Dominé, Laura, Kazuhiro Terao, and DeepLearnPhysics Collaboration. "Scalable deep convolutional neural networks for sparse, locally dense liquid argon time projection chamber data." Physical Review D 102.1 (2020): 012005.
```
@article{SSCN,
  title={Scalable deep convolutional neural networks for sparse, locally dense liquid argon time projection chamber data},
  author={Domin{\'e}, Laura and Terao, Kazuhiro and DeepLearnPhysics Collaboration and others},
  journal={Physical Review D},
  volume={102},
  number={1},
  pages={012005},
  year={2020},
  publisher={APS}
}

```
Koh, Dae Heun, et al. "Scalable, Proposal-free Instance Segmentation Network for 3D Pixel Clustering and Particle Trajectory Reconstruction in Liquid Argon Time Projection Chambers." arXiv preprint arXiv:2007.03083 (2020).
```
@article{koh2020scalable,
  title={Scalable, Proposal-free Instance Segmentation Network for 3D Pixel Clustering and Particle Trajectory Reconstruction in Liquid Argon Time Projection Chambers},
  author={Koh, Dae Heun and de Soux, Pierre C{\^o}te and Domin{\'e}, Laura and Drielsma, Fran{\c{c}}ois and Itay, Ran and Lin, Qing and Terao, Kazuhiro and Tsang, Ka Vang and Usher, Tracy},
  journal={arXiv preprint arXiv:2007.03083},
  year={2020}
}

```

## Full chain diagram

## Full chain movie

## Event displays
> File: `/gpfs/slac/staas/fs1/g/neutrino/ldomine/.scn_paper/shower_relabel/test_768px.root`
> Index: 66

![data(2).png](/event_displays/data(2).png =300x) ![labels(1).png](/event_displays/labels(1).png =300x)
*Left: input data. Right: semantic labels.*