---
title: PPN
description: Point Proposal Network
published: true
date: 2021-03-05T17:14:29.439Z
tags: 
editor: markdown
dateCreated: 2020-05-18T21:00:58.510Z
---

## 1. Network Architecture
![uresnet_+_ppn_architecture.png](/architectures/uresnet_+_ppn_architecture.png){.align-center}

## 2. Performance
### PILArNet (old, w/o ghost points)
From PPN paper
![distance_to_closest.png](/performance/pilarnet/distance_to_closest.png =500x){.align-center}

### PILArNet (new, w/o ghost points)
From NIPS 2020 workshop paper, by François
![ppn_noghost.png](/performance/pilarnet/ppn_noghost.png =500x){.align-center}

### ICARUS simulation (w/ ghost points)
As of January 2021, by Laura
![ppn_icarus_distance_plot.png](/performance/icarus/ppn_icarus_distance_plot.png =500x){.align-center}

### Unknown
Need to clarify where this comes from
![ppn_true2reco.png](/performance/ppn_true2reco.png =500x){.align-center}

## 3. Event displays
From PPN paper = PILArNet old dataset, data and labels:

![data.png](/event_displays/pilarnet/data.png =300x){.align-left}
![uresnet_ppn_labels.png](/event_displays/pilarnet/uresnet_ppn_labels.png =300x)


Example of PPN predictions:

![uresnet_ppn_predictions.png](/event_displays/pilarnet/uresnet_ppn_predictions.png =300x)