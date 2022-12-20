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

