---
title: Input data
description: >-
    Description of the input data used by PhyloNext.
---

# Description of the input data used by PhyloNext




## Obtaining a local snapshot of species occurrences from GBIF

If you would like to run the pipeline locally, you may download the GBIF occurrence dump from the [Amazon AWS cloud](https://registry.opendata.aws/gbif/) using the [AWS CLI program](https://aws.amazon.com/cli/) (no AWS account required). E.g., to download `2022-05-01` dump to the `GBIF` directory in your home folder run:
```
aws s3 sync \
  s3://gbif-open-data-eu-central-1/occurrence/2022-05-01/occurrence.parquet/ \
  ~/GBIF/ \
  --no-sign-request
```

Alternative GBIF snapshot is also [hosted by the Microsoft AI for Earth program](https://github.com/microsoft/AIforEarthDataSets/blob/main/data/gbif.md). To download it using the [AzCopy command-line utility](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10) run:
```
azcopy copy \
  "https://ai4edataeuwest.blob.core.windows.net/gbif/occurrence/2022-05-01/occurrence.parquet/*" \
  "~/GBIF/" \
  --recursive=true
```
