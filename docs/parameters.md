---
title: Pipeline parameters
description: >-
    A complete list and description of PhyloNext parameters.
---

# Pipeline parameters

PhyloNext includes numerous configurable parameters.  
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

