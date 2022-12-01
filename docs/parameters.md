---
title: Pipeline parameters
description: >-
    A complete list and description of PhyloNext parameters.
---

# Pipeline parameters

PhyloNext includes numerous configurable parameters.  
For convenience, they are grouped into several categories: 

- Input/output options
- Data subsetting:
    * Taxonomic scope
    * Spatial scope
- Spatial outliers removal
- Occurrence filtering and binning
- Diversity estimation
- Visualization
    * Interactive ([Leaflet](https://leafletjs.com/)-based)
    * Static (maps in pdf format)
- Phylogenetic tree-related parameters
- Generic options

## Input/output options

Define where the pipeline should find input data and save output data.

| Parameter   | Description                                                          | Type     | Default   |
| ----------- | -------------------------------------------------------------------- | -------- | --------- |
| `--input`   | Path to the directory with parquet files.                            | `string` |           |
|             | (GBIF occurrence dump in the Parquet format)                         |          |           |
|             | Could be stored locally or in the cloud (S3 or Azure Blob storage).  |          |           |
|             |                                                                      |          |           |
| `--outdir`  | The output directory where the results will be saved                 | `string` | ./results |
|             |                                                                      |          |           |
| `--phytree` | Custom phylogenetic tree in Newick format (optional).                | `string` |           |
|             | Tips should be labeled either with Latin binomials                   |          |           |
|             | (e.g., "Homo_sapiens"), or with Open Tree IDs (e.g, "ott359899").    |          |           |
|             | Please adjust the `--phylabels` parameter correspondingly.           |          |           |

