---
title: Basic Usage
description: >-
    An overview of PhyloNext parameters you might need to adjust to meet your needs.
---

## Input data

## Help

To show a help message, run:

    nextflow run vmikk/phylonext -r main --helpMsg

## Run test example

Test run the pipilen using test data and Docker engine:

    nextflow run vmikk/phylonext -r main -profile test,docker

## Example command

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


## What's next?

- Explore interactive maps (see Output file description)  
- Manually configure static map using the output in GeoPackage format (see Postprocessing)  
- Explore the results using Biodiverse program (see Postprocessing)  
- Prepare citable derived dataset with unique DOI (see Postprocessing)  

