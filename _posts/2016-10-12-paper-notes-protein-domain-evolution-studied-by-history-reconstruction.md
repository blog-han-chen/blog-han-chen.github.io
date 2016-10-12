---
layout: post
title: "Paper Notes: Protein Domain Evolution Through Studying Rearrangement"
description: ""
category: 
tags: [Bioinformatics, Paper Notes, Domain Architecture]
---
{% include JB/setup %}
{:center: style="text-align: center"}


Evolution at the Subgene Level: Domain Rearrangements in the Drosophila Phylogeny
=======

[http://compbio.mit.edu/publications/56_Wu_MBE_11.pdf](http://compbio.mit.edu/publications/56_Wu_MBE_11.pdf)



#Main Idea
-----
The paper presents a new algorithm of constructing domain level evolution that takes into account domain **rearrangement**, in addition to duplication, loss, horizontal transfer. The algorithm is run and tested on _Drosophila_ genome data. 


#Key Contribution
-----

* A new, intermediate resolution view on evolution through atomic modules on sub-gene level, rather than high resolution base pair level, or low resolution gene level
* An algorithm to identify novel evolutionary modules (e.g. domains, exons, or any _subsequence_ of gene) that are subject to gain, loss, and rearrangement. This is based on sequence similiarity given known closely related species
* A new model for **gene** evolution capturing module operations like module creation, duplication, loss, merge, and split. It traces "gene evolution" rather than "architecture evolution".
* A new maximum parsimony algorithm to construct evolutionary tree by improving on an existing species tree, taking into account evolutionary modules. 


Key Methods Explained
----


#Definitions
----

* a **module** is a subsequence (not a substring) of base pairs, regarded as an atomic unit of evolution. Modules are conceptually distinct from domains, though they sometimes agree. 
* **Module Family** is a set of homologous modules. They descend from a single ancestral module, found in the last common ancestor of all species. 
* **Architecture**: list of modules in a _gene_
* **Gene Architecture Family**: maximal set of genes connected by module homology
* **Gene Architecture Family Evolution**: instead of a tree, represented as a directed acyclic graph (DAG)
* **Architecture Scenarios**: basically an annotated, improved species tree with architecture info. Species tree nodes annotated by multiset of architectures (since one genome has many genes, one gene has many architectures), as well as architecture copy number. Tree edges annotated with evolutionary events (merge, split, gain, loss) . 


#Identifying Modules and Module Families
----

1. pairwise all vs all BLASTp on all given species. Filter out e-value > 10^-5 and identity <60% (TODO, why?) 
2. alignments extended using LALIGN 
3. ADDA algorithm to identify modules (doi:10.1006/jmbi.2001.4776)
4. module family clustering to OrthoMCL (Enright et al. 2002)


#Clustering Architectures
----

1. Take Module Family as vertices, add edges only if two module families are neighbours within at least one gene (Say Module families A, B, found in 1 gene ABC, then A B are connected)
2. since some module families are highly connected (with >6 neighbours), those are removed
3. Take all genes in a connected component as an architecture family

**Reconstructing Architecture Scenarios**
too complicated, did not understand
But basically attempt to reconstrcut tree to best explain module evolution. 

#Results
-----
Not all results presented, the paper is 17 pages. 

* Found some new modules (not previously annotated domains)
* Confirmed chimeric genes (2 or more genes fused in one) found previously in fruit flies.
* Architectures associated with some GO terms are enriched in merge/split
* Propose that merging and splitting of gene could occur by altering start/stop codon, as suggested by tree reconstruction. 
* Enable studying alternative splicing and its relation to module splitting/merging. 

#Questions
-----

1. Clustering architecture families seems strange. It connect any two module family as long as they are found in at least one gene. We would get an over-sized component due to some module families randomly connected to each other. And the resulting set of genes is large. Perhaps the paper has more detail




