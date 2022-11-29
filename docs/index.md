[PhyloNext source coce](https://github.com/vmikk/PhyloNext).

## Pipeline summary

1. Filtering of GBIF species occurrences for various taxonomic clades and geographic areas
2. Removal of non-terrestrial records and spatial outliers (using density-based clustering)
3. Preparation of phylogenetic tree (currently, only pre-constructed phylogenetic trees are available; with the update of OToL, phylogenetic trees will be downloaded automatically using API) and name-matching with GBIF species keys
4. Spatial binning of species occurrences using Uber’s H3 system (hexagonal hierarchical spatial index)
5. Estimation of phylogenetic diversity and endemism indices using [Biodiverse program](https://shawnlaffan.github.io/biodiverse/)
6. Visualization of the obtained results

## Commands

Test run using Docker enginge

    nextflow run vmikk/phylonext -r main -profile test,docker


## Help

To show a help message, run

    nextflow run vmikk/phylonext -r main --helpMsg

