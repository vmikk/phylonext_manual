---
title: Diversity indices
description: >-
    A list of the most useful diversity metrics supported by Biodiverse, which could be specified in PhyloNext.
---

# Diversity indices

The Biodiverse program is able to estimate more than 350 diversity indices.  
Here you will find a short list of the most common indices and a way to specify them in PhyloNext.  

!!! note "Indices supported by Biodiverse"
    For details see the Biodiverse manual here:  
    [https://github.com/shawnlaffan/biodiverse/wiki/Indices](https://github.com/shawnlaffan/biodiverse/wiki/Indices)  


In Biodiverse, indices are organized into groups by *"subroutines"* (functions) used to generate them. 
The program's output will include single or multiple diversity metrics depending on the subroutine. 
E.g., subroutine `calc_simpson_shannon` will estimate 4 diversity indices (`SHANNON_E`, `SHANNON_H`, `SHANNON_HMAX`, and `SIMPSON_D`), 
while subroutine `calc_phylo_aed_t` will output only a `PHYLO_AED_T` index.  

To specify multiple diversity metrics in PhyloNext, a user must provide a comma-separated list of Biodiverse's subroutines to the `--indices` parameter. 
For example, `--indices "calc_pd,calc_pe"`. 
The output will contain all metrics estimated by these subroutines (`PD`, `PD_P`, `PD_P_per_taxon`, `PD_per_taxon`, `PE_WE`, and `PE_WE_P` in this case).  

| Index name ^1^ | Description                                                                                   | Subroutine ^2^                           |
| -------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------- |
| PD             | Faith's Phylogenetic diversity                                                                | `calc_pd`                                |
| PD_P           | Phylogenetic diversity as a proportion of total tree length                                   | `calc_pd`                                |
| PD_per_taxon   | Phylogenetic diversity per taxon                                                              | `calc_pd`                                |
| PD_P_per_taxon | Phylogenetic diversity per taxon as a proportion of total tree length                         | `calc_pd`                                |
| ENDW_WE        | Weighted endemism                                                                             | `calc_endemism_whole`                    |
| ENDW_CWE       | Corrected weighted endemism                                                                   | `calc_endemism_whole`                    |
| PE_WE          | Phylogenetic weighted endemism                                                                | `calc_pe`                                |
| PE_WE_P        | Phylogenetic weighted endemism as a proportion of the total tree length                       | `calc_pe`                                |
| PE_CWE         | Corrected weighted endemism. This is the phylogenetic analogue of corrected weighted endemism | `calc_phylo_corrected_weighted_endemism` |
| PD_ENDEMISM    | Phylogenetic Diversity Endemism                                                               | `calc_pd_endemism`                       |
| PD_ENDEMISM_P  | Phylogenetic Diversity Endemism, as a proportion of the whole tree                            | `calc_pd_endemism`                       |
| PHYLO_RPD1     | Relative Phylogenetic Diversity (type 1)                                                      | `calc_phylo_rpd1`                        |
| PHYLO_RPD2     | Relative Phylogenetic Diversity (type 2)                                                      | `calc_phylo_rpd2`                        |
| PHYLO_RPE1     | Relative Phylogenetic Endemism (type 1)                                                       | `calc_phylo_rpe1`                        |
| PHYLO_RPE2     | Relative Phylogenetic Endemism (type 2)                                                       | `calc_phylo_rpe2`                        |
| PMPD1_MEAN     | Mean pairwise phylogenetic distance (MPD)                                                     | `calc_phylo_mpd_mntd1`                   |
| PNTD1_MEAN     | Mean nearest taxon distance (MNTD)                                                            | `calc_phylo_mpd_mntd1`                   |
| RICHNESS_ALL   | Species richness                                                                              | `calc_richness`                          |
| SIMPSON_D      | Simpson's diversity index                                                                     | `calc_simpson_shannon`                   |
| SHANNON_H      | Shannon's diversity index                                                                     | `calc_simpson_shannon`                   |
| SHANNON_E      | Shannon's evenness                                                                            | `calc_simpson_shannon`                   |
| SHANNON_HMAX   | Maximum possible value of Shannon's H                                                         | `calc_simpson_shannon`                   |
| ES_x ^*^       | Hurlbert's ES index (a measure of species diversity that accounts for sample size)            | `calc_hurlbert_es`                       |

^1^: 
    To visualize result on a map, provide the index name to the `--leaflet_var` parameter.

^2^: 
    Must be specified with `--indices` parameter in the Phylonext pipeline.

!!! info "* Hurlbert's ES index"
    To activate the corresponding subroutine in Biodiverse and estimate the ES index, 
    add `calc_hurlbert_es` to the `--indices` parameter (e.g., `--indices calc_richness,calc_pd,calc_hurlbert_es`).
    It's important to note that the current implementation of Biodiverse uses a fixed set of ES values: 
    5, 10, 20, 50, 100, 200, 500, 1000, 2000, 5000. 
    The estimation of a specific index will largely depend on data availability from GBIF, 
    so it can be challenging to anticipate whether a particular index can be estimated for a given taxon and area of interest.  
    
    In PhyloNext, we ensure that all available ES results are exported in a tabular format.  
    If the user is looking to visualize this data on a map, they need to specify the index in the form 
    `ES_x`, where `x` corresponds to one of the ES values supported by Biodiverse (e.g., `--leaflet_var RICHNESS_ALL,PD,ES_50,ES_100`).
    
    In its original formulation, the Hurlbert's  ES index requires the number of individuals per species.  
    However, given the nature of GBIF data, we usually only have information on species occurrences.  
    To overcome this limitation, we can replace the number of individuals with the number of records in the database.  
    This substitution would slightly modify the **interpretation** of the index, 
    and the ES index would correspond to **the number of unique species in a random sample of N occurrence records**.  
    This is an approach that has been previously explored by John Waller 
    [(link to blogpost)](https://data-blog.gbif.org/post/exploring-es50-for-gbif/).  
    
    For more information about the ES index, see:  
    Hurlbert SH (1971) The Nonconcept of Species Diversity: A Critique and Alternative Parameters. Ecology, 52: 577-586. 
    [DOI:10.2307/1934145](https://esajournals.onlinelibrary.wiley.com/doi/abs/10.2307/1934145)  


## Effect sizes

Standardized effect sizes (SES; a.k.a. Z-scores) can be used to compare phylogenetic diversity of different communities. 
In general, SES is a measure of the deviation of the observed community structure from that expected under a null model of random species distribution. 
The null model is obtained by estimating the diversity index of interest by simulating multiple random communities (of the same size).
SES can be computed for different diversity metrics. 
The most commonly known metrics are NRI (net relatedness index = SES for MPD) and NTI (nearest taxon index = SES for MNTD).  

By default, PhyloNext estimates SES for most of the supported indices. 
To visualize these metrics, user may add `SES_` prefix to the index name (e.g., `--leaflet_var SES_PD`).

!!! info "Standardized effect sizes"
    For more information about standardized effect sizes, see:  
    Webb CO, Ackerly DD, McPeek MA, Donoghue MJ (2002). Phylogenies and Community Ecology. Annual Review of Ecology and Systematics, 33(1), 475–505. [DOI:10.1146/annurev.ecolsys.33.010802.150448](https://www.annualreviews.org/doi/abs/10.1146/annurev.ecolsys.33.010802.150448)



## Special indices

Several metrics are calculated internally by PhyloNext. 
To show them on a map, user may add them to the `--leaflet_var` parameter.  

| Index name | Description                                                                              |
| ---------- | ---------------------------------------------------------------------------------------- |
| CANAPE     | Categorical Analysis of Neo and Palaeo Endemism                                          |
| Redundancy | Measure of sampling completeness (ratio of species richness and the number of records)   |


!!! info "CANAPE"
    For more information about the CANAPE method, see:  
    Mishler BD, Knerr N, González-Orozco CE, Thornhill AH, Laffan CW, Miller JT (2014) Phylogenetic measures of biodiversity and neo- and paleo-endemism in Australian Acacia. Nat Commun 5, 4473. [DOI:10.1038/ncomms5473](https://www.nature.com/articles/ncomms5473)

!!! info "Redundancy"
    For more information about the redundancy index, see:  
    Mishler BD, Guralnick R, Soltis PS, Smith SA, Soltis DE, Barve N, Allen JM, Laffan SW (2020) Spatial phylogenetics of the North American flora. J. Syst. Evol., 58: 393-405. DOI:10.1111/jse.12590](https://onlinelibrary.wiley.com/doi/full/10.1111/jse.12590)

