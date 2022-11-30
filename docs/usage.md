---
title: Basic Usage
description: >-
    An overview of PhyloNext parameters you might need to adjust to meet your needs.
---

To show a help message, run

    nextflow run vmikk/phylonext -r main --helpMsg

Test run using Docker enginge

    nextflow run vmikk/phylonext -r main -profile test,docker


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


