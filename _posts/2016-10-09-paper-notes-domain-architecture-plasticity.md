---
layout: post
title: "Paper Notes: Domain Architecture Plasticity"
description: ""
category: 
tags: [Bioinformatics, Paper Notes, Domain Architecture ]
---
{% include JB/setup %}
{:center: style="text-align: center"}

Function-selective domain architecture plasticity potentials in eukaryotic genome evolution
==========

[http://www.sciencedirect.com/science/article/pii/S0300908415001376](http://www.sciencedirect.com/science/article/pii/S0300908415001376)

<br>

#**Main Idea**: 
Introduces the concept of **plasticity**: the number of distinct domain architectures that a given domain superfamily is found in a genome. It roughly measures how plastic a superfamily is to combine with other domains. Studies plasticity of domains grouped by a Gene Ontology term throughout Eukaryotic evolution. 

<br>

#**Main Questions Attempted to Answer**:

1. Are domains with relevant functions (e.g. in the same pathway) more plastic than a random set of domains?
2. How does plasticity change throughout Eukaryotic evolution? Does it change differently in different species or in different functions?


<br>

#**Key Concepts**:

* **Domain Architecture Plasticity**: the number of distinct domain architectures that a given domain superfamily is found within a genome. E.g. if superfamily X contains domains A, B, and ACD, ABE, BEG are found in a genome, then X has 3 distinct domain architectures
* **Plasticity Potential** (PP) for an individual domain: the number of distinct domain architectures that a given _domain_ is found within a genome. E.g. domain A is found in ACD, ABE, then its PP is 2
* **Plasticity Potential w.r.t. Gene Ontology**: the plasticity of all domain superfamilies associated with a particular GO term found in one genome
* **PP Score** for a _set_ of domains (e.g. all domain superfamilies associated with signal transduction): a number that indicates the average plasticity potential for the given domain set, relative to other domains found in a genome

<br>

#**Key Methodology**:

* PP Score calculation for given **GO term** and **genome of interest**
    1. find all domains associated with a GO term, call it **domain_set**
    2. for each domain in the domain_set, find how many different architectures it is involved in within that genome, calculate the median, **PP_raw**. Note that median is used instead of mean to remove effect of extreme values
    3. perform bootstraping in steps 4 - 7
    4. find all domains with *any* GO annotation in a genome, randomly sample **n** domains where n = size(domain_set), calculate **PP_raw_sampled** for each of sampled domain_set, do this **B** times to obtain B PP_raw_sample scores
    5. calcualte mean and standard deviation of B number of PP_raw_sampled's 
    6. calculate Z score of PP_raw using the mean and sd from random sampling (note that normal distribution is assumed), this is called **PP score**
    7. a **p-value** is also calculated by (number of times PP_raw >= PP_raw_sampled) / B, if PP_raw > 0. Otherwise, (number of times PP_raw <= PP_raw_sampled) / B. Note that p-value does not assume normal distribution. See bootstrapping P-value for more detail. 
* calculate PP Score and p-value for a matrix of GO terms and Eukaryotic genomes
* p-values are normalized using **multiple testing correction**



<br>

#**Results**

- Most of domain set associated with a GO term display positive PP score. Meaning that most of them have high plasticity.
- Details on plasticity evolution within different species, or functions can be found in paper's result section. Not repeated here. 


<br>

#**Questions**
1. What is implied by the concept of plasticity? Is it the recombination potential of a given domain, or a domain superfamily?

Consider the following domain architecture: 

~~~
A B C 
A B D
A B E
...
A B Z
~~~

A B will both get a high plasticity score because they are both present in many different architectures, but A or B alone are not very plastic, they require each other to function properly. Further, it might be that domains like C D E can recombine easily (e.g. localization domain, kinase domain), but A B get high plasticity score as well. 

We might want to simply interpret plasticity as domain architecture diversity, but the exact implication of diversity can be complicated. 

2. What percentage of domains are not annotated with GO term? Suppose a fair portion of domains are not annotated, then they cannot contribute to the bootstrapped mean and standard deviation. 

3. High plasticity of a given domain group can either imply: 1. there is evolutionary pressure for it to gain diversity, to accomodate different needs 2. there is no evolutionary pressure at all, it gains different domain architecture freely, relative to other more conservative domain groups. Again, the implication of plasticity/diversity is unclear. 



