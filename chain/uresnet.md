---
title: UresNet
description: 
published: true
date: 2021-03-05T16:48:44.799Z
tags: 
editor: markdown
dateCreated: 2020-05-18T20:57:28.191Z
---

## 1. Network architecture
### Basic UResNet
![uresnet_architecture.png](/architectures/uresnet_architecture.png){.align-center}

### Deghosting
![uresnet_deghosting_architecture(1).png](/architectures/uresnet_deghosting_architecture(1).png){.align-center}

## 2. Performance
### PILArNet dataset (w/o ghost points)
From NIPS 2020 workshop paper, by François
![uresnet_noghost.png](/performance/pilarnet/uresnet_noghost.png =500x){.align-center}
### ICARUS simulation (w/ ghost points)
As of January 2021, by Laura

![uresnet_icarus_confusion_matrix.png](/performance/icarus/uresnet_icarus_confusion_matrix.png =500x){.align-center}

## 3. Michel analysis
Finding Michel electrons using UResNet semantic segmentation output only is done as follows:
1. Extract track-like and Michel-like semantic predictions.
2. Run DBSCAN to form particle clusters.
3. Identify Michel candidates that are touching the end of a muon track cluster

Pixel count spectrum using ICARUS simulation (2020):

![michel_spectrum_after_deghosting.png](/performance/michel_spectrum_after_deghosting.png =500x){.align-center}

## 4. Event displays 
(HTML? +CSV? container + config file + run script?)

![data(1).png](/event_displays/ghost/data(1).png =200x) ![ghost_labels.png](/event_displays/ghost/ghost_labels.png =200x) ![semantic_labels.png](/event_displays/ghost/semantic_labels.png =200x)

