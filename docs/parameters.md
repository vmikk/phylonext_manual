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
| `--input`   | Path to the directory with parquet files (GBIF occurrence dump in the Parquet format).<br>Could be stored locally or in the cloud (S3 or Azure Blob storage).                           | `directory` |           |
| `--outdir`  | The output directory where the results will be saved                 | `directory` | ./results |
| `--phytree` | Custom phylogenetic tree in Newick format (optional).<br>Tips should be labeled either with Latin binomials (e.g., "Homo_sapiens"), or with Open Tree IDs (e.g, "ott359899").<br>Please adjust the `--phylabels` parameter correspondingly.   | `file` |           |

## Taxonomic scope

Define which taxa should be analyzed.

| Parameter        | Description                                                                                      | Type      | Example            | Default |
| ---------------- | ------------------------------------------------------------------------------------------------ | --------- | ------------------ | ------- |
| `--phylum`       | Phylum to analyze [^1]                                                                           | `string`  | "Chordata"         |         |
| `--classis`      | Class to analyze [^1]                                                                            | `string`  | "Mammalia"         |         |
| `--order`        | Order to analyze [^1]                                                                            | `string`  | "Carnivora"        |         |
| `--family`       | Family to analyze [^1]                                                                           | `string`  | "Felidae,Canidae"  |         |
| `--genus`        | Genus to analyze [^1]                                                                            | `string`  | "Felis,Canis,Lynx" |         |
| `--specieskeys`  | Custom list of GBIF specieskeys (file with a single column)                                      | `file`    | "Carnivora"        |         |
| `--noextinct`    | File with extinct species specieskeys for their removal (file with a single column, with header) | `file`    | "Carnivora"        |         |
| `--excludehuman` | Exclude genus "Homo" from occurrence data                                                        | `boolean` | True               | True    |

[^1]: Multiple comma-separated values allowed (e.g., "Felidae,Canidae" for the Family rank).  

Unfortunately, `class` is a reserved keyword in Nextflow. Therefore, Latin `classis` is used as a parameter name.

## Spatial scope

Spatial filters.

| Parameter   | Description                                   | Type     | Example                       | Default |
| ----------- | --------------------------------------------- | -------- | ----------------------------- | ------- |
| `--latmin`  | Minimum latitude of species occurrences [^2]  | `number` | 5.1                           |         |
| `--latmax`  | Maximum latitude of species occurrences [^2]  | `number` | 15.5                          |         |
| `--lonmin`  | Minimum longitude of species occurrences [^2] | `number` | 47.0                          |         |
| `--lonmax`  | Maximum longitude of species occurrences [^2] | `number` | 55.5                          |         |
| `--country` | Country code, ISO 3166 [^3]                   | `string` | "DE,PL,CZ"                    |         |
| `--wgsrpd`  | Polygons of World Geographical Regions [^4]   | `file`   |                               |         |
| `--regions` | Names of World Geographical Regions [^5]      | `string` | "L1_EUROPE,L1_ASIA_TEMPERATE" |         |

[^2]: Coordinates should be provided in decimal degrees

[^3]:
    Country codes should be provided in ISO 3166 format (two-letter codes), 
    see details [here](https://www.iso.org/obp/ui/#search) (column `Alpha-2` code).

[^4]:
    PhyloNext ships [WGSRPD](https://www.tdwg.org/standards/wgsrpd/) shapefile as a built-in data.
    To use it, specify a full path to the data (e.g., `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/WGSRPD.RData")`)

[^5]: Multiple comma-separated values allowed 

It is possible to use custom polygons instead of WGSRPD file. 
Polygons should be in the simple feature collection format (class `sf` of the [`sf` package](https://r-spatial.github.io/sf/), 
geometry type: `MULTIPOLYGON`) and must contain `LevelName` column.
Data should be saved as a serialized R object (with `saveRDS`).
