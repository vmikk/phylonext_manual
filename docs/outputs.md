---
title: Output data
description: >-
    Description of the output data produced by PhyloNext.
---

# PhyloNext output

## Diversity estimates in tabular format

Table `03.Plots/Biodiverse_results_merged.txt`

## Interactive map with diversity values

Leaflet-based map `03.Plots/Choropleth.html`

## GeoPackage

Diversity estimates and H3 polygons are also exported into the GeoPackage file (`03.Plots/Diversity_estimates.gpkg`). 
[OGC GeoPackage](https://www.geopackage.org/) is a relatively new, open standard data format for storing geospatial data. 
It is designed to be an efficient, portable, compact, and self-contained format that can store and exchange data within a single file. 

GeoPackage is supported in most modern GIS software packages (e.g., [QGIS](https://www.qgis.org)). 
See the example of opening GeoPackage in QGIS [in the post-processing section](post.md).

!!! info "Why not shapefiles?"
    More information on the comparisons of different formats for storing geospatial data can be found here:  
    [http://switchfromshapefile.org/](http://switchfromshapefile.org/)
