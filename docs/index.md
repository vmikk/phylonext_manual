# PhyloNext ![PhyloNext logo](/assets/PhyloNext_logo.webp){: style="height:100px;width:125px" align=right}

PhyloNext is an automated pipeline for the analysis of phylogenetic diversity using [GBIF occurrence data](https://www.gbif.org/occurrence/search?occurrence_status=present), species phylogenies from [Open Tree of Life](https://tree.opentreeoflife.org), and [Biodiverse software](https://shawnlaffan.github.io/biodiverse/).

## Introduction

PhyloNext pipeline brings together two critical research data infrastructures, the Global
Biodiversity Information Facility [(GBIF)](https://www.gbif.org/) and Open Tree of Life [(OToL)](https://tree.opentreeoflife.org), to make them more accessible to non-experts.

The pipeline is built using [Nextflow](https://www.nextflow.io), a workflow tool to run tasks across multiple compute infrastructures in a very portable manner. It uses [Docker](https://www.docker.com/) containers making installation trivial and results highly reproducible. The [Nextflow DSL2](https://www.nextflow.io/docs/latest/dsl2.html) implementation of this pipeline uses one container per process which makes it much easier to maintain and update software dependencies.

The pipeline could be launched in a cloud environment (e.g., the [Microsoft Azure Cloud Computing Services](https://azure.microsoft.com/en-us/), [Amazon AWS Web Services](https://aws.amazon.com/), and [Google Cloud Computing Services](https://cloud.google.com/)).


## Pipeline summary

1. Filtering of GBIF species occurrences for various taxonomic clades and geographic areas
2. Removal of non-terrestrial records and spatial outliers (using density-based clustering)
3. Preparation of phylogenetic tree (currently, only pre-constructed phylogenetic trees are available; with the update of OToL, phylogenetic trees will be downloaded automatically using API) and name-matching with GBIF species keys
4. Spatial binning of species occurrences using Uber’s H3 system (hexagonal hierarchical spatial index)
5. Estimation of phylogenetic diversity and endemism indices using [Biodiverse program](https://shawnlaffan.github.io/biodiverse/)
6. Visualization of the obtained results

## PhyloNext Workflow

<a class="zoom" href="/assets/PhyloNext_Workflow.webp">
![PhyloNext Workflow](/assets/PhyloNext_Workflow.webp)
</a>

## Documentation Overview

[Installation](installation.md)  
More detailed information for getting PhyloNext set up on your system.

[Usage instructions](usage.md)  
A subset of parameters users may commonly adjust.

[Input data](inputdata.md)  
Description of the input data used by pipeline.

[Output Overview](outputs.md)  
Overview of PhyloNext output.

[Pipeline parameters](parameters.md)  
The full set of parameters that users can tweak in PhyloNext.

[Diversity indices](biodiverse.md)  
A list of the most common diversity indices.

[Troubleshooting](troubleshooting.md)  
Handling errors.

[Web GUI](webgui.md)  
Web-based graphical user interface for PhyloNext.

[Post processing](post.md)  
Post processing output files.

[Use case examples](usecases.md)  
A few use cases analysed by PhyloNext.  

[Acknowledgments](acknowledgements.md)  
Acknowledgments and a list of software packages used by PhyloNext.  

## Contributions and Support

If your ran into any issues, please let us know by submitting a [GitHub Issue](https://github.com/vmikk/PhyloNext/issues).

If you would like to contribute to this pipeline, please see the [contributing guidelines](https://github.com/vmikk/PhyloNext/blob/master/CONTRIBUTING.md).


PhyloNext source code is deposited [here](https://github.com/vmikk/PhyloNext).

