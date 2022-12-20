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
- Nextflow-specific parameters

## Input/output options

Define where the pipeline should find input data and save output data.

| Parameter   | Description                                                   | Type        | Default   |
| ----------- | ------------------------------------------------------------- | ----------- | --------- |
| `--input`   | Path to the directory with parquet files (Parquet format) ^1^ | `directory` |           |
| `--outdir`  | The output directory where the results will be saved          | `directory` | ./results |
| `--phytree` | Custom phylogenetic tree in Newick format (optional) ^2^      | `file`      |           |

^1^:
    GBIF occurrence dump in the Parquet format. 
    Could be stored locally or in the cloud (S3 or Azure Blob storage).

^2^:
    Optionally, user can supply a custom phylogenetic tree. 
    In this case, tips of the phylogenetic should be labeled either 
    with Latin binomials (e.g., "Homo_sapiens"), 
    or with Open Tree IDs (e.g, "ott359899"). 
    Please adjust the `--phylabels` parameter correspondingly. 

## Taxonomic scope

Define which taxa should be analyzed.

| Parameter        | Description                                                                                      | Type      | Example            | Default |
| ---------------- | ------------------------------------------------------------------------------------------------ | --------- | ------------------ | ------- |
| `--phylum`       | Phylum to analyze ^1^                                                                            | `string`  | "Chordata"         |         |
| `--classis`      | Class to analyze ^1^                                                                             | `string`  | "Mammalia"         |         |
| `--order`        | Order to analyze ^1^                                                                             | `string`  | "Carnivora"        |         |
| `--family`       | Family to analyze ^1^                                                                            | `string`  | "Felidae,Canidae"  |         |
| `--genus`        | Genus to analyze ^1^                                                                             | `string`  | "Felis,Canis,Lynx" |         |
| `--specieskeys`  | Custom list of GBIF specieskeys (file with a single column)                                      | `file`    | "Carnivora"        |         |
| `--noextinct`    | File with extinct species specieskeys for their removal (file with a single column, with header) | `file`    | "Carnivora"        |         |
| `--excludehuman` | Exclude genus "Homo" from occurrence data                                                        | `boolean` | True               | True    |

^1^ Multiple comma-separated values allowed (e.g., "Felidae,Canidae" for the Family rank).  

Unfortunately, `class` is a reserved keyword in Nextflow. Therefore, Latin `classis` is used as a parameter name.

## Spatial scope

Spatial filters.

| Parameter   | Description                                  | Type     | Example                       | Default |
| ----------- | -------------------------------------------- | -------- | ----------------------------- | ------- |
| `--latmin`  | Minimum latitude of species occurrences ^1^  | `number` | 5.1                           |         |
| `--latmax`  | Maximum latitude of species occurrences ^1^  | `number` | 15.5                          |         |
| `--lonmin`  | Minimum longitude of species occurrences ^1^ | `number` | 47.0                          |         |
| `--lonmax`  | Maximum longitude of species occurrences ^1^ | `number` | 55.5                          |         |
| `--country` | Country code, ISO 3166 ^2^                   | `string` | "DE,PL,CZ"                    |         |
| `--wgsrpd`  | Polygons of World Geographical Regions ^3^   | `file`   |                               |         |
| `--regions` | Names of World Geographical Regions ^4^      | `string` | "L1_EUROPE,L1_ASIA_TEMPERATE" |         |

^1^: Coordinates should be provided in decimal degrees

^2^:
    Country codes should be provided in ISO 3166 format (two-letter codes), 
    see details [here](https://www.iso.org/obp/ui/#search) (column `Alpha-2` code).

^3^:
    PhyloNext ships [WGSRPD](https://www.tdwg.org/standards/wgsrpd/) shapefile as a built-in data.
    To use it, specify a full path to the data (e.g., `$(realpath "${HOME}/.nextflow/assets/vmikk/phylonext/pipeline_data/WGSRPD.RData")`)

^4^: Multiple comma-separated values allowed 

!!! info "Custom polygons"
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

| Parameter                | Description                                             | Type      | Example              | Default                           |
| ------------------------ | ------------------------------------------------------- | --------- | -------------------- | --------------------------------- |
| `--minyear`              | Minimum year of record's occurrences                    | `integer` | 2000                 | 1945                              |
| `--basisofrecordinclude` | Round spatial coordinates to N decimal places ^1^       | `string`  | "PRESERVED_SPECIMEN" |                                   |
| `--basisofrecordexclude` | Round spatial coordinates to N decimal places ^1^       | `string`  | "FOSSIL_SPECIMEN"    | "FOSSIL_SPECIMEN,LIVING_SPECIMEN" |
| `--h3resolution`         | Spatial resolution of the H3 geospatial indexing system | `integer` | 4                    | 4                                 |
| `--roundcoords`          | Round spatial coordinates to N decimal places ^2^       | `integer` | 2                    | 2                                 |


For spatial binning of species occurrences, PhyloNext uses H3 geospatial indexing system [developed by Uber](https://h3geo.org/docs). 
H3 represents a hierarchical geospatial index, where each hexagonal grid cell has a unique index (e.g., `8a1f05835a37fff` for GBIF headquarter). 
H3 supports 16 resolutions (from 1 to 16), which can be selected in PhyloNext using `--h3resolution` parameter. 
By default, PhyloNext uses resolution `4`, which corresponds to a hexagon with edge length of 22.6 km and cell area of 1170 km<sup>2</sup>. 
More details on H3 resolutions could be found [here](https://h3geo.org/docs/core-library/restable/).  

^1^:
    For details, see description of a Darwin Core term [Basis of record](https://docs.gbif.org/course-data-use/en/basis-of-record.html) 
    and a short explanation [here](https://data-blog.gbif.org/post/living-specimen-to-preserved-specimen-understanding-basis-of-record/).  
    Multiple comma-separated values allowed.  

^2^: 
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

## Diversity estimation

Diversity estimation is performed using [Biodiverse](https://shawnlaffan.github.io/biodiverse/) program by Shawn Laffan. 
Therefore, parameter values should correspond to the Biodiverse values.  

| Parameter             | Description                                                                | Type      | Example                         | Default                                                                                                                                   |
| --------------------- | -------------------------------------------------------------------------- | --------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `--indices`           | Diversity and endemism indices to estimate ^1^                             | `string`  | "calc_richness,calc_pd,calc_pe" | "calc_richness,calc_simpson_shannon,calc_endemism_whole,calc_pd,calc_pe,calc_phylo_rpd1,calc_phylo_rpd2,calc_phylo_rpe1,calc_phylo_rpe2"  |
| `--iterations`        | Number of randomisation iterations for standardized effect size estimation | `integer` | 1000                            | 1000                                                                                                                                      |
| `--biodiversethreads` | Number of Biodiverse threads                                               | `integer` | 10                              | 10                                                                                                                                        |
| `--randname`          | Randomisation scheme type                                                  | `string`  | "rand_structured"               | "rand_structured"                                                                                                                         |

^1^: 
    Comma-seprated list of metrics. More than 350 indices are supported. 
    For details see [Biodiverse manual](https://github.com/shawnlaffan/biodiverse/wiki/Indices).

## Visualization - interactive

Interactive visualization depends on [Leaflet library](https://leafletjs.com/).

| Parameter              | Description                                                                   | Type      | Example     | Default                                                                              |
| ---------------------- | ----------------------------------------------------------------------------- | --------- | ----------- | ------------------------------------------------------------------------------------ |
| `--leaflet_var`        | Diversity and endemism indices to estimate ^1^                                | `string`  | "PD,SES_PD" | "RICHNESS_ALL,PD,SES_PD,PD_P,ENDW_WE,SES_ENDW_WE,PE_WE,SES_PE_WE,CANAPE,Redundancy" |
| `--leaflet_color`      | Color scheme for continuous variables  ^2^                                    | `string`  | "RdYlBu"    | "RdYlBu"                                                                             |
| `--leaflet_palette`    | Color palette for continuous variables ^3^                                    | `string`  | "quantile"  | "quantile"                                                                           |
| `--leaflet_bins`       | Number of color bins for continuous variables                                 | `integer` | 5           | 5                                                                                    |
| `--leaflet_redundancy` | Redundancy threshold for hiding the grid cells with low number of records ^4^ | `number`  | 85          | 0                                                                                    |

^1^:
    For a list of indices available in Biodiverse, see [here](https://github.com/shawnlaffan/biodiverse/wiki/IndicesDevVersion).  
    Note: to show the index on a map, please include appropriate name of the Biodiverse subroutine with `--indices` parameter.  
    To display Standardized-Effect-Size-based variables, add `SES_` prefix to the index name (e.g., `SES_PD`).  
    For CANAPE (categorical analysis of neo- and paleoendemism; Mishler et al., 2014), add `CANAPE` to the variables list.  
    To display redundancy index (measure of sampling completeness; see [Mishler et al., 2020](https://onlinelibrary.wiley.com/doi/full/10.1111/jse.12590)), add `Redundancy` to the variables list.  

^2^: 
    The name of a a color scheme from the [`ColorBrewer`](https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3) designed by Cynthia Brewer. 
    For more options see [here](https://r-graph-gallery.com/38-rcolorbrewers-palettes.html).

^3^:
   Defines mapping of data values to colors . See [Leaflet docs](https://rstudio.github.io/leaflet/colors.html):  

   - `colorNumeric` is a simple linear mapping from continuous numeric data to an interpolated palette
   - `colorBin` also maps continuous numeric data, but performs binning based on value (see the cut function)
   - `colorQuantile` similarly bins numeric data, but via the quantile function

^4^:
    Sampling redundancy is defined as [1 – (richness / number of specimens)] in a grid cell. 
    By default, parameter `--leaflet_redundancy` is set to 0, which will display all grid cells.

!!! info "CANAPE"
    CANAPE (Categorical Analysis of Palaeo and Neo Endemism) 
    is able to distinguish different types of centres of endemism, and can thus give insights 
    into different evolutionary and ecological processes that may be responsible for these patterns. 
    
    - The centres of paleo-endemism indicate places where there are over-representation of long branches that are rare across the landscape.
    - The centres of neo-endemism indicate an area where there is an over-representation of short branches that are rare on the landscape.
    - Mixture of both paleo-endemism and neo-endemism
    - Super-endemic sites

## Visualization - static

| Parameter      | Description                      | Type     | Example | Default                               |
| -------------- | -------------------------------- | -------- | ------- | ------------------------------------- |
| `--plotvar`    | Variables to plot ^1^            | `string` | "PD"    | "RICHNESS_ALL,PD,PD_P"                |
| `--plottype`   | Plot type                        | `string` |         | raw                                   |
| `--plotformat` | Plot format (jpg,pdf,png)        | `string` |         | pdf                                   |
| `--plotwidth`  | Plot width (default, 18 inches)  | `number` |         | 18                                    |
| `--plotheight` | Plot height (default, 18 inches) | `number` |         | 18                                    |
| `--plotunits`  | Plot size units (in,cm)          | `string` |         | in                                    |
| `--world`      | World basemap ^2^                | `file`   |         | enabled                               |

^1^: Multiple comma-separated values allowed. For details see `--leaflet_var` parameter notes.

^2^:
    World base map is based on [Natural Earth](https://www.naturalearthdata.com/) data 
    and is located in the `pipeline_data/WorldMap_NaturalEarth_Medium.RData` file.

## Phylogenetic tree-related parameters

| Parameter     | Description                                                             | Type      | Default  |
| ------------- | ----------------------------------------------------------------------- | --------- | -------- |
| `--phylabels` | Type of tip labels on a phylogenetic tree ("OTT" or "Latin") ^1^        | `string`  | Latin    |
| `--taxgroup`  | Specific taxonomy group in Open Tree of Life (default, "All_life") ^2^  | `string`  | All_life |
| `--maxage`    | Manually assign root age for a tree obtained from Open Tree of Life ^3^ | `number`  |          |
| `--phyloonly` | Prune Open Tree tips for which there are no phylogenetic inputs ^4^     | `boolean` | false    |

^1^: 
    If a custom phylogenetic tree was provided by user (with `--phytree` parameter),
    it's important to specify the type of tip labels. 
    Currently, two labeling schemes are supported:

- Latin binomials in `Genus_species` format (e.g., "Homo_sapiens"). NB! whitespace is not allowed!
- Open Tree of Life IDs (e.g., ["ott770315"](https://tree.opentreeoflife.org/taxonomy/browse?id=770315))

^2^:
    In the case if tree tips are in OTT-format, 
    it is possible to limit the query scope at [Taxonomic Name Resolution Service](https://tree.opentreeoflife.org/curator/tnrs/) of Open Tree of Life. 
    List available taxonomic contexts in [`rotl`](https://docs.ropensci.org/rotl/) v.3.0.12 package:

- All life (default)
- Animals 
    - Birds
    - Tetrapods
    - Mammals
    - Amphibians
    - Vertebrates
    - Arthropods
    - Molluscs
    - Nematodes
    - Platyhelminthes
    - Annelids
    - Cnidarians
    - Arachnids
    - Insects
- Land plants 
    - Hornworts
    - Mosses
    - Liverworts
    - Vascular plants
    - Club mosses
    - Ferns
    - Seed plants
    - Flowering plants
    - Monocots
    - Eudicots
    - Rosids
    - Asterids
    - Asterales
    - Asteraceae
    - Aster 
    - Symphyotrichum
    - Campanulaceae
    - Lobelia
- Fungi 
    - Basidiomycetes
    - Ascomycetes
- Bacteria
- Archaea
- SAR group
    - Excavata
    - Amoebozoa
    - Centrohelida
    - Haptophyta
    - Apusozoa
    - Diatoms
    - Ciliates
    - Forams

!!! info "Taxonomic context"
    Whitespace is not allowed in taxonomic context! 
    Please replace space with underscore (e.g., "Seed plants" should be speciefied as `--taxgroup Seed_plants`).

^3^:
    In some cases, when there are no age estimates available for the phylogenetic tree nodes, 
    automatic tree retrieval could fail. Therefore, it could be helpful to override the maximum node age to obtain the tree. 
    Note, however, that in this case, units of some of the diversity indices (e.g., PD) become arbitrary.

## Generic options

| Parameter          | Description                                                                | Type      | Default |
| ------------------ | -------------------------------------------------------------------------- | --------- | ------- |
| `--deriveddataset` | Export list of GBIF dataset keys for the filtered species occurrences. ^1^ | `boolean` | True    |
| `--helpMsg`        | Display help text (pipeline)                                               | `boolean` |         |

^1^: Could be used for citation and preparing a derived dataset with unique DOI.

## Nextflow-specific parameters

!!! info "Parameter types"
    PhyloNext-specific parameters are specified using double dash prefix (e.g., `--something value`). 
    Parameters related to the Nextflow workflow manager starts with a single dash prefix (e.g., `-work-dir wd`)


| Parameter  | Description                                                                                                   | Type      | Example     | Default                  |
| ---------- | ------------------------------------------------------------------------------------------------------------- | --------- | ----------- | ------------------------ |
| `-qs`      | Queue size (max number of processes that can be executed in parallel)                                         | `integer` | 8           | number of available CPUs |
| `-w`       | Path to the working directory to store intermediate results                                                   | `string`  | "$(pwd)/wd" | "work"                   |
| `-resume`  | Execute the pipeline using the cached results.<br>Useful to continue executions that was stopped by an error. |           |             |                          |
| `-profile` | Configuration profile                                                                                         | `string`  | test,docker |                          |

