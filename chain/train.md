---
title: Training the full chain
description: Some instructions and descriptions (hopefully helpful)
published: true
date: 2020-09-02T18:43:13.898Z
tags: 
---

## Running a standard configuration
An typical configuration would be the following (no ghost points, UResNet + PPN + CNN clustering + GNN clustering for showers + GNN interaction clustering):

```
iotool:
  batch_size: 32
  shuffle: False
  num_workers: 4
  collate_fn: CollateSparse
  sampler:
    batch_size: 32
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
        - sparse3d_pcluster_semantics
      particles_label:
        - parse_particle_points
        - sparse3d_pcluster
        - particle_corrected
model:
  name: ghost_chain
  modules:
    # ----- Global chain configuration -----
    chain:
      enable_uresnet: True
      enable_ppn: True
      enable_gnn_shower: True
      enable_gnn_tracks: False
      enable_gnn_int: True
      enable_ghost: False
      use_ppn_in_gnn: False
    full_chain_loss:
      name: se_lovasz_inter
      spatial_size: 768
      segmentation_weight: 1.
      clustering_weight: 1.
      ppn_weight: 1.
      particle_gnn_weight: 1.
      inter_gnn_weight: 1.
    # ---- Shower clustering GNN config -----
    particle_gnn:
      node_type: 0
      node_min_size: 10
      model_path: '/gpfs/slac/staas/fs1/g/neutrino/drielsma/clustering/train/prod_meta/weights/cluster_full_gnn/dbscan/snapshot-36199.ckpt'
      model_name: 'chain.edge_predictor'
    dbscan:
      epsilon: 1.999
      minPoints: 1
      num_classes: 5
      data_dim: 3
    node_encoder:
      name: 'geo'
      use_numpy: True
    edge_encoder:
      name: 'geo'
      use_numpy: True
    particle_edge_model:
      name: modular_meta
      edge_feats: 19
      node_feats: 16 #22 #w/ start point and direction
      node_classes: 2
      edge_classes: 2
      node_output_feats: 64
      edge_output_feats: 64
      aggr: 'add'
      leakiness: 0.1
      num_mp: 3
    # ----- Interaction GNN config -----
    interaction_gnn:
      node_type: -1
      node_min_size: 10
      network: 'complete'
      edge_max_dist: -1
      edge_dist_metric: 'set'
      edge_dist_numpy: False #True
      add_start_point: False #True
      add_start_dir: True
      start_dir_max_dist: 5
      group_pred: 'score'
      loss: 'CE'
      reduction: 'sum'
      balance_classes: False
      target: 'group'
      source_col: 6
      target_col: 7
      high_purity: True
      #model_path: '/gpfs/slac/staas/fs1/g/neutrino/drielsma/clustering/train/prod_meta/weights/cluster_full_gnn/dbscan/snapshot-36199.ckpt'
      #model_path: '/gpfs/slac/staas/fs1/g/neutrino/ldomine/chain/weights_shower_clustering0/snapshot--21749.ckpt'
    interaction_edge_model:
      name: modular_meta
      edge_feats: 19
      node_feats: 28 #w/ start point and direction
      node_classes: 2
      edge_classes: 2
      node_output_feats: 64
      edge_output_feats: 64
      aggr: 'add'
      leakiness: 0.1
      num_mp: 3
    # ----- CNN Clustering config -----
    network_base:
      spatial_size: 768
      data_dim: 3
      features: 4
      leakiness: 0.33
    spatial_embeddings:
      seediness_dim: 1
      sigma_dim: 1
      embedding_dim: 3
      coordConv: True
      model_path: '/gpfs/slac/staas/fs1/g/neutrino/koh0207/weights/SCN/new_labels/final2/with_seed/snapshot-71499.ckpt'
    uresnet:
      filters: 64
      input_kernel_size: 7
      num_strides: 7
      reps: 2
    clustering_loss:
      name: se_lovasz_inter
      seediness_weight: 1.0
      embedding_weight: 1.0
      smoothing_weight: 1.0
    # ----- UResNet config -----
    uresnet_lonely:
      freeze: False
      num_strides: 6
      filters: 16
      num_classes: 5
      data_dim: 3
      spatial_size: 768
      ghost: False
      features: 1
      model_path: '/gpfs/slac/staas/fs1/g/neutrino/drielsma/clustering/train/prod_meta/weights/uresnet_ppn/snapshot-195499.ckpt'
    # ---- PPN config -----
    ppn:
      num_strides: 6
      filters: 16
      num_classes: 5
      data_dim: 3
      downsample_ghost: False
      use_encoding: False
      ppn_num_conv: 1
      #weight_seg: 5.0
      weight_ppn: 0.9
      score_threshold: 0.5
      ppn1_size: 24
      ppn2_size: 96
      spatial_size: 768
      model_path: '/gpfs/slac/staas/fs1/g/neutrino/drielsma/clustering/train/prod_meta/weights/uresnet_ppn/snapshot-195499.ckpt'
    fragment_clustering:
      s_thresholds: [0., 0., 0., 0.35]
      p_thresholds: [0.95, 0.95, 0.95, 0.95]
      cluster_all: False
  network_input:
    - input_data
  loss_input:
    - segment_label
    - cluster_label
    - particles_label
trainval:
  seed: 123
  unwrapper: unwrap_3d_scn
  concat_result: ['seediness', 'margins', 'embeddings', 'fragments','frag_edge_index','frag_edge_pred','frag_node_pred','frag_group_pred','particles','inter_edge_index','inter_edge_pred']
  gpus: '0'
  weight_prefix: ./weights_trash/snapshot
  iterations: 2000
  report_step: 1
  checkpoint_step: 100
  model_path: ''
  log_dir: ./log_trash
  train: True
  debug: False
  minibatch_size: -1
  optimizer:
    name: Adam
    args:
      lr: 0.001
```
To run it, make sure you have the latest code version from `Temigo/lartpc_mlreco3d` on the branch `temigo`.

> TODO merge this code into `DeepLearnPhysics/lartpc_mlreco3d`


## Training step by step
For better results the chain should be trained step by step. The order is usually the following:

1. UResNet
2. PPN (can be combined with 1.)
3. CNN clustering (if applicable) followed by GNN clustering of tracks (not currently in the `full_chain` model)
4. GNN clustering for EM showers
5. Interaction clustering (GNN again)


Note: 3. and 4. can be done in parallel.