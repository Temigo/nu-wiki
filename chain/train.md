---
title: Training the full chain
description: Some instructions and descriptions (hopefully helpful)
published: true
date: 2020-12-09T02:14:52.096Z
tags: 
---

## Running a standard configuration

> To run it, make sure you have the latest code version from `DeepLearnPhysics/lartpc_mlreco3d` on the branch `develop`.
{.is-info}

> TODO: update weights to no-ghost weights.
{.is-warning}


### Configuration
This is meant to be an example of a typical configuration: no ghost points, with all the steps of the full chain currently available (UResNet + PPN + CNN/DBSCAN clustering + GNN clustering for showers, tracks, interactions + Kinematics and particle flow + cosmic discrimination). Let us break it down to explain a few things. 

First comes the I/O configuration block. 
> Technical Note: here we specify `particle_mpv_tree` to be used by `parse_cluster3d_clean_full` and `parse_cluster3d_kinematics_clean`, because the neutrino interaction id wasn't recorded in this sample yet. If you are using a newer sample, ask Laura about skipping this mitigation (not implemented yet).
{.is-info}

```
iotool:
  batch_size: 4
  shuffle: False
  num_workers: 4
  collate_fn: CollateSparse
  sampler:
    batch_size: 4
    name: RandomSequenceSampler
  dataset:
    name: LArCVDataset
    data_keys:
      - /gpfs/slac/staas/fs1/g/neutrino/kterao/data/mpvmpr_2020_01_v04/train.root
    limit_num_files: 10
    schema:
      input_data:
        - parse_sparse3d_scn
        - sparse3d_pcluster
      segment_label:
        - parse_sparse3d_scn
        - sparse3d_pcluster_semantics
      cluster_label:
        - parse_cluster3d_clean_full
        - cluster3d_pcluster
        - particle_pcluster
        - particle_mpv
        - sparse3d_pcluster_semantics
      particles_label:
        - parse_particle_points
        - sparse3d_pcluster
        - particle_corrected
      kinematics_label:
        - parse_cluster3d_kinematics_clean
        - cluster3d_pcluster
        - particle_corrected
        - particle_mpv
        - sparse3d_pcluster_semantics
      particle_graph:
        - parse_particle_graph_corrected
        - particle_corrected
        - cluster3d_pcluster
```

Then we start configuring the full chain. The `chain` block defines which parts of the chain you want to use. This is also where you should turn on `enable_ghost` if your sample has ghost points. 
```
model:
  name: ghost_chain
  modules:
    chain:
      enable_uresnet: True
      enable_ppn: True
      enable_cnn_clust: True
      enable_gnn_shower: True
      enable_gnn_tracks: True
      enable_gnn_int: True
      enable_kinematics: False
      enable_cosmic: True
      enable_ghost: False
      use_ppn_in_gnn: True
      verbose: False
```
After that, it is time to specify the config for each block of the chain. 
#### 1. UResNet + PPN
This is a UResNet architecture with depth 6 (`num_strides`) and 16 initial filters (`filters`). `features` specifies how many features the input has (in this example, just one, an energy deposit for each voxel). `ghost` should be enabled if your input has ghost points and you require the "5+2" architecture (predicts a binary ghost/non-ghost mask in addition to the semantic segmentation mask). `spatial_size` defines a cube (in voxels) in which all coordinates should fit. `data_dim` is 3 since the sample is three-dimensional. `num_classes` refers to the semantic classes in your labels (not counting the label for ghost point, if applicable).

In the `ppn` block, the eponymous configuration parameters should be identical to the `uresnet_lonely` block (the PPN layers feed off the UResNet network).
`downsample_ghost` should be True if you have ghost points, False otherwise.
```
    # UResNet + PPN
    uresnet_ppn:
      uresnet_lonely:
        num_strides: 6
        filters: 16
        num_classes: 5
        data_dim: 3
        spatial_size: 768
        ghost: False
        features: 1
        model_path: '/gpfs/slac/staas/fs1/g/neutrino/ldomine/chain/new/weights_cnn_clustering1/snapshot-50999.ckpt'
        model_name: 'uresnet_lonely'
      ppn:
        num_strides: 6
        filters: 16
        num_classes: 5
        data_dim: 3
        downsample_ghost: True
        weight_ppn: 0.9
        score_threshold: 0.5
        ppn1_size: 24
        ppn2_size: 96
        spatial_size: 768
        model_path: '/gpfs/slac/staas/fs1/g/neutrino/ldomine/chain/new/weights_cnn_clustering1/snapshot-50999.ckpt'
        model_name: 'ppn'
```


Let's see what are the outputs of this configuration.

### Outputs
Assuming that the previous configuration is stored in a string `cfg`:
```python
from mlreco.main_funcs import process_config, prepare
cfg=yaml.load(cfg,Loader=yaml.Loader)
# pre-process configuration (checks + certain non-specified default settings)
process_config(cfg)
# prepare function configures necessary "handlers"
hs=prepare(cfg)
```
We can run the full chain for one iteration using
```python
data, output = hs.trainer.forward(hs.data_io_iter)
```
Now we have access to both the input data to the network `data` and the outputs of the full chain in `output`.

> Jupyter notebooks visualizing outputs for ghost/noghost: see for now `/gpfs/slac/staas/fs1/g/neutrino/ldomine/chain/Output_Chain_Ghost.ipynb` and `/gpfs/slac/staas/fs1/g/neutrino/ldomine/chain/Output_Chain_NoGhost.ipynb`.

### Analysis / Inference
Available post-processing scripts to record metrics on all stages of the chain at the same time:

```
post_processing:
  cluster_gnn_metrics:
    ghost: True
    store_method:
      - per-iteration
      - per-iteration
      #- per-iteration
    clusts:
      - particles
      - fragments
      #- track_fragments
    edge_pred:
      - inter_edge_pred
      - frag_edge_pred
      #- track_edge_pred
    edge_index:
      - inter_edge_index
      - frag_edge_index
      #- track_edge_index
    column:
      - 7
      - 6
      #- 6
    chain:
      - interaction_gnn
      - particle_gnn
      #- track_gnn
    filename:
      - cluster-gnn-metrics-inter
      - cluster-gnn-metrics-shower
      #- cluster-gnn-metrics-track
  uresnet_metrics:
    store_method: per-iteration
    num_classes: 5
  deghosting_metrics:
    store_method: per-iteration
    method: '5+2'
  ppn_metrics:
    store_method: per-iteration
    num_classes: 5
    mode: select
  cluster_cnn_metrics:
    store_method: per-iteration
    ghost: True
    p_thresholds:
      - 0.95
      - 0.95
      - 0.95
      - 0.95
    s_thresholds:
      - 0.0
      - 0.0
      - 0.0
      - 0.35
```

## Training step by step
For better results the chain should be trained step by step. The order is usually the following:

1. UResNet
2. PPN (can be combined with 1.)
3. CNN clustering (if applicable) followed by GNN clustering of tracks (not currently in the `full_chain` model)
4. GNN clustering for EM showers
5. Interaction clustering (GNN again)


Note: 3. and 4. can be done in parallel.