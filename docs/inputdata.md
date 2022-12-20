---
title: Input data
description: >-
    Description of the input data used by PhyloNext.
---

# Obtaining the input data for PhyloNext

## GBIF-mediated data in Parquet format

GBIF provides monthly-based periodic snapshots of species occurrence records. 
These snapshots are available on [Amazon S3](https://registry.opendata.aws/gbif/), [Google GCS](https://console.cloud.google.com/storage/browser/public-datasets-gbif), and [Microsoft Azure](https://planetarycomputer.microsoft.com/dataset/gbif) cloud storages. 
Data are stored in [Apache Parquet](https://parquet.apache.org/) format files. 
Parquet format allows running queries quickly and efficiently.

!!! info "Data format"
    For more information on the data format, see [the description of GBIF Public Datasets](https://github.com/gbif/occurrence/blob/master/aws-public-data.md).

## Obtaining a local snapshot of species occurrences from GBIF

If you would like to run the pipeline locally, you may download the full GBIF occurrence dump from the [Amazon AWS cloud](https://registry.opendata.aws/gbif/) using the [AWS CLI program](https://aws.amazon.com/cli/) (no AWS account required). E.g., to download `2022-05-01` dump to the `GBIF` directory in your home folder run:
``` bash
aws s3 sync \
  s3://gbif-open-data-eu-central-1/occurrence/2022-05-01/occurrence.parquet/ \
  ~/GBIF/ \
  --no-sign-request
```

Alternative GBIF snapshot is also [hosted by the Microsoft AI for Earth program](https://github.com/microsoft/AIforEarthDataSets/blob/main/data/gbif.md). To download it using the [AzCopy command-line utility](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10) run:
``` bash
azcopy copy \
  "https://ai4edataeuwest.blob.core.windows.net/gbif/occurrence/2022-05-01/occurrence.parquet/*" \
  "~/GBIF/" \
  --recursive=true
```

!!! warning "Limitations"
    Currently, `arrow` v.10.0.0 does not support filtering of array-type columns. 
    E.g., it could be useful for removal of common geospatial issues in the data (see column `issue` in the parquet files).

!!! example "For developers"
    See the related issues [ARROW-16641](https://issues.apache.org/jira/browse/ARROW-16641) 
    and [ARROW-16702](https://issues.apache.org/jira/browse/ARROW-16702) 
    at Apache Arrow issue tracker.

If you would like to remove records with geospatial issues from the data, please see the following section.

## Subsetting occurrence data via GBIF API

Working with a subset of occurrences could increase the processing speed of the pipeline.
It is possible to obtain a subset of species occurrence from GBIF programmatically. For this purpose, one may use an application programming interface (API) provided by GBIF. API is a set of rules and protocols that allow different software programs to communicate with each other. Therefore, APIs enable developers to access the data and functionality of a website or web-based service in a controlled, programmatic way.

First, you must specify a set of filters that should be applied to the data. For this purpose, create a simple text file in JSON format (see the example below). Please replace `creator` and `notification_address` with your own.

``` json
{
 "format": "SIMPLE_PARQUET",
 "creator": "USERNAME",
 "notification_address": [ "USEREMAIL" ],
 "predicate": {
  "type": "and",
  "predicates": [
   {
    "type": "equals",
    "key": "OCCURRENCE_STATUS",
    "value": "PRESENT"
   },
   {
    "type": "equals",
    "key": "HAS_GEOSPATIAL_ISSUE",
    "value": "false"
   },
   {
    "type": "not",
    "predicate": {
     "type": "in",
     "key": "ESTABLISHMENT_MEANS",
     "values": [
      "MANAGED",
      "INTRODUCED",
      "INVASIVE",
      "NATURALISED"
     ]
    }
   },
   {
    "type": "not",
    "predicate": {
     "type": "in",
     "key": "BASIS_OF_RECORD",
     "values": [
      "FOSSIL_SPECIMEN",
      "LIVING_SPECIMEN"
     ]
    }
   },
   {
    "type": "not",
    "predicate": {
     "type": "in",
     "key": "ISSUE",
     "values": [
      "TAXON_MATCH_HIGHERRANK"
     ]
    }
   }
  ]
 }
}
```

If you would like to specify taxonomic or spatial scopes, you may add additional **predicates** (query expressions to retrieve occurrence record downloads), e.g.:
``` json
   {
    "type": "equals",
    "key": "GENUS_KEY",
    "value": "2978223"
   },
   {
    "type": "equals",
    "key": "COUNTRY",
    "value": "AU"
   }
```

!!! note "GBIF predicates"
    An extensive list of supported predicates and query parameters could be found here:  
    [https://www.gbif.org/developer/occurrence](https://www.gbif.org/developer/occurrence)


To send download request, please fill in your user name and password and run:
``` bash
USER="USERNAME"
PASSWORD="PASSWORD"

curl -Ssi \
  --user "$USER":"$PASSWORD" \
  -H "Content-Type: application/json" \
  -X POST -d @get_parquet.json \
  https://api.gbif.org/v1/occurrence/download/request
```

Please note the download ID returned by `curl`.  
To check the status of the request and get the download link, use:
``` bash
curl -Ss https://api.gbif.org/v1/occurrence/download/0003936-220831081235567 | jq .
```
(don't forget to replace the download ID!)


To download the results (zip archive with parquet files), use:
``` bash
mkdir -p ~/GBIF_dumps
cd ~/GBIF_dumps
aria2c \
  https://api.gbif.org/v1/occurrence/download/request/0003936-220831081235567.zip \
  -o gbif_dump.zip
```

!!! info "GBIF API beginners guide"
    A very nice introduction to the GBIF APIs can be found in the ["GBIF API beginners guide"](https://data-blog.gbif.org/post/gbif-api-beginners-guide/) by John Waller.


!!! example "Required software"
    To run the commands mentioned above, you may need to install the following software:  
    [`curl`](https://curl.se/), [jq](https://stedolan.github.io/jq/), and [`aria2`](https://aria2.github.io/).  
    If you are using [`conda`](https://docs.conda.io/en/latest/) package manager, it could be done in a singe command:  
    `conda install -c conda-forge curl aria2 jq`
