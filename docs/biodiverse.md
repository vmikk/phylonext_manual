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
    [https://github.com/shawnlaffan/biodiverse/wiki/Indices](https://github.com/shawnlaffan/biodiverse/wiki/Indices).  


In Biodiverse, indices are organized into groups by *"subroutines"* (functions) used to generate them. 
The program's output will include single or multiple diversity metrics depending on the subroutine. 
E.g., subroutine `calc_simpson_shannon` will estimate 4 diversity indices (`SHANNON_E`, `SHANNON_H`, `SHANNON_HMAX`, and `SIMPSON_D`), 
while subroutine `calc_phylo_aed_t` will output only a `PHYLO_AED_T` index.  

To specify multiple diversity metrics in PhyloNext, a user must provide a comma-separated list of Biodiverse's subroutines to the `--indices` parameter. 
For example, `--indices "calc_pd,calc_pe"`. 
The output will contain all metrics estimated by these subroutines (`PD`, `PD_P`, `PD_P_per_taxon`, `PD_per_taxon`, `PE_WE`, and `PE_WE_P` in this case).  
