---
title: Basic Usage
description: >-
    An overview of PhyloNext parameters you might need to adjust to meet your needs.
---

## Input data

!!! info "Input data"
    For mode details, see the [input data](inputdata.md) description section in the documentation.

## Getting help

To show a help message, run:
``` bash
nextflow run vmikk/phylonext -r main --helpMsg
```

## Run test example

Test run the pipeline using test data and Docker engine:
``` bash
nextflow run vmikk/phylonext -r main -profile test,docker
```

## Example command

``` bash
nextflow run vmikk/phylonext -r main \
  --input "/mnt/GBIF/Parquet/2022-01-01/occurrence.parquet/" \
  --classis "Mammalia" --family  "Felidae,Canidae" \
  --country "DE,PL,CZ"  \
  --minyear 2000  \
  --dbscan true  \
  --phytree  $(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/test_data/phy_trees/Mammals.nwk") \
  --iterations 100  \
  --outdir "$PWD" \
  -resume
```

## Passing in an input parameter file

## Workflow processes

Workflow managers such as Nextflow are designed to help users run complex pipelines of processes, 
potentially involving multiple steps and dependencies between them, efficiently and reproducibly. 
One of the key features of such tools is the ability to run processes in parallel, 
which can reduce the overall time taken to complete a workflow by distributing the workload across multiple computational resources. 
To run the pipeline effectively and maximize resource utilization, Nextflow will:
- Effectively and automatically allocate CPUs on the available computational resources (local, a cluster, or a cloud environment);
- Use a scheduling algorithm to determine the order in which processes should be run based on the dependencies between them and the availability of resources.

During the pipeline execution, you will see a progress indicator in the output in your terminal:

```
[ad/02689a] process > occ_filter            [100%] 1 of 1 ✔
[b7/48eb93] process > record_count          [100%] 1 of 1 ✔
[cc/af139c] process > outl_low              [100%] 1 of 1 ✔
[88/197e96] process > outl_high (5219243)   [100%] 6 of 6 ✔
[85/8425c9] process > merge_occ             [100%] 1 of 1 ✔
[b6/bcf57e] process > prep_biodiv           [100%] 1 of 1 ✔
[aa/bbb119] process > phylodiv (1)          [100%] 1 of 1 ✔
[18/d367ac] process > rand_filelist         [100%] 1 of 1 ✔
[6b/b1d08b] process > aggregate_rnds_biodiv [100%] 1 of 1 ✔
[c6/ecf435] process > div_to_csv            [100%] 1 of 1 ✔
[ae/7de318] process > plot_pd               [100%] 1 of 1 ✔
[ba/141853] process > plot_leaflet          [100%] 1 of 1 ✔
[6a/ca28a8] process > derived_datasets      [100%] 1 of 1 ✔
```


| Process name            | Description                                                                                              | CPUs per task ^1^ |
| ----------------------- | -------------------------------------------------------------------------------------------------------- | ----------------- |
| `occ_filter`            | Occurrence filtering of parquet file                                                                     | 4                 |
| `record_count`          | Counting the total number of records per H3 cell                                                         | 4                 |
| `outl_high`             | Per-species outlier removal for species with high occurrence (optionally, with DBSCAN), spatial binning  | 1                 |
| `outl_low`              | Spatial outlier removal for species with high occurrence (DBSCAN disabled), spatial binning              | 4                 |
| `prep_ott_ids`          | Preparation of Open Tree IDs for a phylogenetic tree (optional)                                          | 4                 |
| `get_ott_tree`          | Automatic fetching of a phylogenetic tree from Open Tree of Life (optional)                              | 1                 |
| `merge_occ`             | Merging filtered species occurrences, species name matching, phylogenetic tree pruning                   | 4                 |
| `prep_biodiv`           | Creation of Biodiverse input files and estimation of the observed diversity                              | 1                 |
| `phylodiv`              | Randomization of species communities and estimation of standardized effect sizes of biodiversity metrics | 1                 |
| `rand_filelist`         | Preparation of randomization results                                                                     | 1                 |
| `aggregate_rnds_biodiv` | Randomization results pooling                                                                            | 1                 |
| `div_to_csv`            | Export of Biodiverse results into CSV format                                                             | 1                 |
| `plot_pd`               | Plotting diversity indices (non-interactive maps in PDF format)                                          | 1                 |
| `plot_leaflet`          | Plotting diversity indices (interactive maps - Leaflet-based choropleth)                                 | 1                 |
| `derived_datasets`      | Preparation of a list of DOIs for the GBIF datasets involved in the analysis (optional)                  | 4                 |

^1^:
    The default number of CPUs per task is configured in the `nextflow.config`.

Processes are executed by Nextflow independently and are isolated from each other, 
which allows running the pipeline on different nodes at HPC or a cloud platform.
Most workflow processes support multi-threading (e.g., species occurrence filtering). 
In some cases, the task will be divided into chunks and run in parallel 
(e.g., estimation of standardized effect sizes - 10 processes with 100 randomization iterations each). 
It is possible to customize the number of CPUs required by the process task (see `Configuration` below). 
The total number of CPUs allowed to use by PhyloNext can be adjusted with `-qs` parameter (by default, all available CPUs will be used).


It is possible to pass the pipeline parameters via YAML or JSON file, e.g.:
``` bash
nextflow run vmikk/phylonext -r main -resume -params-file Mammals.yaml
```

The YAML file could contain the following:
``` yaml
input      : "/mnt/GBIF/Parquet/2022-01-01/occurrence.parquet/"
classis    : "Mammalia"
family     : "Felidae,Canidae"
country    : "DE,PL,CZ"
minyear    : 2000
dbscan     : true
phytree    : "/path/to/the/phylogenetic/tree.nwk"
iterations : 100
```

## The other helpful commands

Download or update the pipeline.  
By default, the pipeline is stored in the `~/.nextflow/assets/vmikk/PhyloNext` directory
``` bash
nextflow pull vmikk/phylonext
```

Run the latest development version of the pipeline
``` bash
nextflow run vmikk/phylonext -r main ...
```
Run the tagged version (e.g., v1.0) of the pipeline
``` bash
nextflow run vmikk/phylonext -r v1.0 ...
```
Print the pipeline and system runtime information
``` bash
nextflow info
nextflow info vmikk/phylonext
```

Delete the local copy of the pipeline
``` bash
nextflow drop vmikk/phylonext
```



## What's next?

- Explore interactive maps (see Output file description)  
- Manually configure static map using the output in GeoPackage format (see Postprocessing)  
- Explore the results using Biodiverse program (see Postprocessing)  
- Prepare citable derived dataset with unique DOI (see Postprocessing)  

