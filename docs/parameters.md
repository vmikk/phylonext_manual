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

## Spatial outliers removal

| Parameter              | Description                                           | Type      | Example | Default |
| ---------------------- | ----------------------------------------------------- | --------- | ------- | ------- |
| `--dbscan`             | Remove spatial outliers with density-based clustering | `boolean` | True    | False   |
| `--dbscannoccurrences` | Minimum species occurrence to perform DBSCAN          | `integer` | 30      | 30      |
| `--dbscanepsilon`      | DBSCAN parameter epsilon, km                          | `integer` | 1500    | 1500    |
| `--dbscanminpts`       | DBSCAN min number of points                           | `integer` | 3       | 3       |

Currently, only density-based clustering (DBSCAN) algorithm is implemented for removal of spatial outliers.  
The DBSCAN algorithm requires 2 parameters:  
- `eps` (`--dbscanepsilon`), which specifies how close points should be to each other to be considered a part of a cluster  
  If the distance between two points is lower or equal to this value, these points are considered neighbors.  
- `minPoints` (`--dbscanminpts`), the minimum number of points to form a dense region.  

Parameter `--dbscannoccurrences` is used to skip DBSCAN filtering for species with low number of unique points (e.g., <30).  

For more details, see the blog post ["Outlier Detection Using DBSCAN"](https://data-blog.gbif.org/post/outlier-detection-using-dbscan/) by John Waller.


## Occurrence filtering and binning

| Parameter        | Description                                             | Type      | Example | Default |
| ---------------- | ------------------------------------------------------- | --------- | ------- | ------- |
| `--minyear`      | Minimum year of record's occurrences                    | `integer` | 2000    | 1945    |
| `--h3resolution` | Spatial resolution of the H3 geospatial indexing system | `integer` | 4       | 4       |
| `--roundcoords`  | Round spatial coordinates to N decimal places [^6]      | `integer` | 2       | 2       |

For spatial binning of species occurrences, PhyloNext uses H3 geospatial indexing system [developed by Uber](https://h3geo.org/docs). 
H3 represents a hierarchical geospatial index, where each hexagonal grid cell has a unique index (e.g., `8a1f05835a37fff` for GBIF headquarter). 
H3 supports 16 resolutions (from 1 to 16), which can be selected in PhyloNext using `--h3resolution` parameter. 
By default, PhyloNext uses resolution `4`, which corresponds to a hexagon with edge length of 22.6 km and cell area of 1170 km<sup>2</sup>. 
More details on H3 resolutions could be found [here](https://h3geo.org/docs/core-library/restable/).  

[^6]: 
    As DBSCAN filtering is very computationally intensive, 
    it is possible to reduce dataset size (almost without loosing precision) by rounding record coordinates. 
    By default, coordinates are rounded to 2 decimal places, 
    which corresponds to an accuracy of ~1.11 km at the equator. 
    To disable coordinate rounding, set `--roundcoords` to a negative values (e.g., `-1`).


## Removal of common spatial errors

PhyloNext implements several filters analogous to the filters 
in [`CoordinateCleaner`](https://ropensci.github.io/CoordinateCleaner/) R package by Alexander Zizka:

- Country and province centroids
- Capital coordinates
- Coordinates of biodiversity institutions
- Urban areas
- Seas

| Parameter              | Description                                             | Type   | Default  |
| ---------------------- | ------------------------------------------------------- | ------ | -------- |
| `--terrestrial`        | Land polygon for removal of non-terrestrial occurrences | `file` | enabled  |
| `--rmcountrycentroids` | Polygons with country and province centroids            | `file` | disabled |
| `--rmcountrycapitals`  | Polygons with country capitals                          | `file` | disabled |
| `--rminstitutions`     | Polygons with biological institutions and museums       | `file` | disabled |
| `--rmurban`            | Polygons with urban areas                               | `file` | disabled |

 PhyloNext provides files for removal of common spatial errors, 
 the data are built-in in the `pipeline_data` direcory and can be selected in the following way:
``` bash
--terrestrial `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/Land_Buffered_025_dgr.RData")`
--rmcountrycentroids `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/CC_CountryCentroids_buf_1000m.RData")`
--rmcountrycapitals `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/CC_Capitals_buf_10000m.RData")`
--rminstitutions `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/CC_Institutions_buf_100m.RData")`
--rmurban `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/CC_Urban.RData")`
```
Alternatively, users can provide own files with custom polygons.
