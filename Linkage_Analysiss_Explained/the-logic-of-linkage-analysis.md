---
# yaml-language-server: $schema=schemas\page.schema.json
Object type:
    - Page
Backlinks:
    - Linkage Analysis
Creation date: "2025-09-02T21:29:24Z"
Created by:
    - Mohanad Hussein
id: bafyreicvz32ts43hlzwii7ey5zvbeziwj5eqnngkkbtkhwkbp5xfiyvbiu
---
# The Logic of Linkage analysis   
> https://jamanetwork.com/journals/jamaneurology/fullarticle/775035    

Very informative: [https://practical-haemostasis.com/Genetics/linkage\_analysis1.htm](https://practical-haemostasis.com/Genetics/linkage_analysis1.html)l    

$$
https://www.youtube.com/watch?v=5GY6ZIyJ0ds

$$
# Linkage Logic   
### Linkage Disequilibrium   
- Markers or alleles are said to be in LD when they are in proximity that is less likely to be broken by recombination. Also defined as appearing together in a population more than expected by random chance   
- This proximity is what we benefit from in finding loci (genes) that are close to a marker in our SNP map/chip   
   
### Paremeters   
- Since the amount of markers is big and the search area is even bigger, we don't consider only single markers but rather block of markers to compare among sample with a given inheritance pattern   
- Two important parameters are spacing and markers per set   
- **markers per set** is the amount of markers we include as a single haplotype, which we try to trace its passage from parents to children, we usually choose 30 markers   
- **spacing **is the distance in cM between each two markers in the set. Although not precise, it is assumed that 1 cM is equal to 1 Mb. We use spacing of 0.01 cM (i.e 1Mb/100=10kb)   
- This means: between every two markers in a set of 30 markers there is a distance of 0.01 cM (i.e 10kb). Given a set of 30 spaced with 10kb bases, the entire region (i.e haplotype) becomes 10kb\*30 = 300kb   
   
### Analysis   
- As far as I checked each tool uses different math method.   
- But the main idea is that we look for how these marker sets flow from parents to children, the more the flow matches the disease pattern and designated phenotypes, the higher the LOD score   
   
# LOD Score   
> Calculator: https://faculty.ucr.edu/~mmaduro/LODcalc.htm    

logic: [https://www.mun.ca/biology/scarr/LOD\_analysis.html](https://www.mun.ca/biology/scarr/LOD_analysis.html)    
lecture: [https://www.uvm.edu/~dstratto/bcor101/0920.pdf](https://www.uvm.edu/~dstratto/bcor101/0920.pdf)    
[https://www.wikihow.com/Calculate-LOD-Score](https://www.wikihow.com/Calculate-LOD-Score)    
- When saying that two markers are 10cM away from each other, it means that the probability of occurrence of recombination event between these two markers is 10%.     
- this means that the non-recombined haplotype probability is 90% and the recombinant haplotype probability is 10%   
   
Recombinant fraction (θ) = 0.1   
  - This is the case when there're two markers each with only two possible alleles and they are 10cM away from each other, and we are calculating for one generation (each generation is considered an independent event)   
  - this also means that prob of non-recombined haplotype = 1-θ   
- LOD is the logarithm of the ratio of two probabilities   
   
By definition, LOD is logarithm of the ratio of two probabilities:   
  **LOD= P(Pedigree data\| θ<0.5) / P(pedigree data \| θ=0.5)**   
  - **Numerator **is the probability of observing the data (data=all possible inheritance patterns and markers summed together) given range of possible theta values (0>θ>0.5)   
  - **Denominator **is the same probability, but with the null hypothesis (no linkage at all) represented as theta 0.5 implied of independent assortment law   
  - Now, tools (e.g merlin) can utilize "data" in different ways, like using a grid of 1cM with central imaginary marker where all informative markers in range are used to calculate "data"   
  - HMM are used to calculate "Data", merlin calculates probability of a position's hidden state (parental origin) given an observed state (genotyped sample)   
# Pedigree   
> https://pmc.ncbi.nlm.nih.gov/articles/PMC1275650/    

- **phasing:** If a pedigree has full genotypes available, it is called as phased, otherwise not.   
- **Informative Meiosis: **A meiosis event (birth) in pedigree where you can define what alleles each person have received   
   
Bits of a Pedigree: 2n-f   
  n = nonfounders, samples with parents in pedigree   
  f = founders, samples with no parents in the pedigree   
   
