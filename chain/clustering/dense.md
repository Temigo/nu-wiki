---
title: Voxel Clustering
description: Track + shower fragments
published: true
date: 2021-04-04T09:51:55.263Z
tags: 
editor: markdown
dateCreated: 2020-05-18T21:02:31.963Z
---


# Voxel Clustering

Voxel clustering refers to the task of clustering point cloud data into different particle IDs. 
Currently there are two neural-network based models for voxel clustering: SPICE, GraphSPICE, and Sparse Mask-RCNN. 

## SPICE

### 1. Network architecture
![spice_architecture.png](/architectures/spice_architecture.png)

### 2. Performance
#### PILArNet (w/o ghost points)
![f32d6_boxplot.png](/performance/f32d6_boxplot.png){.align-center}

#### ICARUS simulation (w/ ghost points)
From January 2021, by Laura
![spice_icarus.png](/performance/icarus/spice_icarus.png =500x){.align-center}

## 3. Event displays

[event_test_25_cnn_clust_label_vs_pred.pdf](/event_displays/event_test_25_cnn_clust_label_vs_pred.pdf)