---
title: Training the full chain
description: Some instructions and descriptions (hopefully helpful)
published: true
date: 2020-08-27T00:54:56.697Z
tags: 
---

## Configuration (w/o ghost points)
An example of configuration using the model `full_chain` (CNN clustering, not DBSCAN) made by Francois:

```
iotool:
  batch_size: 32
  shuffle: False
  num_workers: 4
  collate_fn: CollateSparse
  sampler:
    name: RandomSequenceSampler
  dataset:
    name: LArCVDataset
    data_keys:
      - /gpfs/slac/staas/fs1/g/neutrino/kterao/data/mpvmpr_2020_01_v04/train.root
    limit_num_files: 1
    schema:
      input_data:
        - parse_sparse3d_scn
        - sparse3d_pcluster
      cluster_labels:
        - parse_cluster3d_clean_full
        - cluster3d_pcluster
        - particle_pcluster
        - sparse3d_pcluster_semantics
      ppn_labels:
        - parse_particle_points
        - sparse3d_pcluster
        - particle_pcluster
model:
  name: full_chain
  modules:
    uresnet_lonely:
      model_path: 'weights/full_chain/uresnet_ppn/snapshot-195499.ckpt'
      #freeze_weights: True
      num_strides: 6
      filters: 16
      num_classes: 5
      data_dim: 3
      spatial_size: 768
      ghost: False
      features: 1
    ppn:
      model_path: 'weights/full_chain/uresnet_ppn/snapshot-195499.ckpt'
      #freeze_weights: True
      num_strides: 6
      filters: 16
      num_classes: 5
      data_dim: 3
      downsample_ghost: False
      use_encoding: False
      ppn_num_conv: 1
      score_threshold: 0.5
      ppn1_size: 24
      ppn2_size: 96
      spatial_size: 768
    network_base:
      model_path: 'weights/full_chain/cluster_cnn/snapshot-55199.ckpt'
      spatial_size: 768
      data_dim: 3
      features: 4
      leakiness: 0.33
    spatial_embeddings:
      model_path: 'weights/full_chain/cluster_cnn/snapshot-55199.ckpt'
      seediness_dim: 1
      sigma_dim: 1
      embedding_dim: 3
      coordConv: True
    uresnet:
      model_path: 'weights/full_chain/cluster_cnn/snapshot-55199.ckpt'
      filters: 64
      input_kernel_size: 7
      num_strides: 6
      reps: 2
    clustering_loss:
      name: se_lovasz_inter
      seediness_weight: 1.0
      embedding_weight: 1.0
      smoothing_weight: 1.0
    fragment_clustering:
      s_thresholds: [0.8, 0.9, 0.55, 0.8]
      p_thresholds: [0.13, 0.24, 0.48, 0.48]
      cluster_all : False
    node_encoder:
      name: 'geo'
      use_numpy: True
    edge_encoder:
      name: 'geo'
      use_numpy: True
    particle_gnn:
      model_path: 'weights/full_chain/part_gnn_only/snapshot-24999.ckpt'
      node_type: 0
      node_min_size: 10
    interaction_gnn:
      model_path: 'weights/full_chain/inter_gnn_only/snapshot-17999.ckpt'
      node_type: -1
      node_min_size: 10
      source_col: 6
      target_col: 7
    particle_edge_model:
      name: modular_nnconv
      edge_feats: 19
      node_feats: 22
      edge_output_feats: 64
      node_output_feats: 64
      node_classes: 2
      edge_classes: 2
      aggr: 'add'
      leak: 0.1
      num_mp: 3
    interaction_edge_model:
      name: modular_nnconv
      edge_feats: 19
      node_feats: 28
      edge_output_feats: 64
      node_output_feats: 64
      node_classes: 2
      edge_classes: 2
      aggr: 'add'
      leak: 0.1
      num_mp: 3
    full_chain:
      merge_batch: False
      merge_batch_mode: 'const'
      merge_batch_size: 4
    full_chain_loss:
      name: se_lovasz_inter
      spatial_size: 768
      segmentation_weight: 1.0
      ppn_weight: 1.0
      clustering_weight: 1.0
      particle_gnn_weight: 1.0
      interaction_gnn_weight: 1.0
  network_input:
    - input_data
  loss_input:
    - cluster_labels
    - ppn_labels
trainval:
  seed: 144
  gpus: ''
  weight_prefix: weights/snapshot
  unwrapper: unwrap_3d_scn
  concat_result: ['fragments','frag_edge_index','frag_edge_pred','frag_node_pred','frag_group_pred','particles','inter_edge_index','inter_edge_pred']
  iterations: 100000
  report_step: 1
  checkpoint_step: 100
  log_dir: logs
  model_path: ''
  train: True
  debug: False
  optimizer:
    name: Adam
    args:
      lr: 0.001
```

## Training step by step
For better results the chain should be trained step by step. The order is usually the following:

1. UResNet
2. PPN (can be combined with 1.)
3. CNN clustering (if applicable) followed by GNN clustering of tracks
4. GNN clustering for EM showers
5. Interaction clustering (GNN again)

3. and 4. can be done in parallel.