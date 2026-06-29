---
# yaml-language-server: $schema=schemas\page.schema.json
Object type:
    - Page
Backlinks:
    - Linkage Analysis
Creation date: "2023-11-20T14:41:17Z"
Created by:
    - Mohanad Hussein
Links:
    - files\technote_array_analysis_workflows.pdf
    - files\whatsapp-image-2023-11-22-at-13-21-34_69545bf9.jpg
    - files\illumina-resources.jpg
id: bafyreigbit4d2qpddjawu6wtrhdtxo3eiebf2dvfb5id3zjphdim6rxni4
---
# GenomeStudio workflow   
Main Tutorial: https://youtu.be/s23379Gya0Y?si=pfwXwmMyHfZvkrWb

Main Analysis Documentation: https://www.illumina.com/Documents/products/technotes/technote\_infinium\_genotyping\_data\_analysis.pdf

https://support.illumina.com/training.html?filters=web%253Aproducts%252Fsnp\_genotyping\_and\_cnv\_analysis%252Finfinium\_dna\_analysis\_beadchips%252Finfinium-qc-array   
   
> Required files   

- **Intensity Folder:** a folder with intensity file (x,y coordinates) in .**idat** format for each sample (green/red), can be found on illuminassoftware LIMS. 
these can also be converted to .**gtc** files using Autoconvert from iScan Control, see:     
[technote_array_analysis_workflows](files\technote_array_analysis_workflows.pdf)    
- **beadpool manifest file:** .**bpm** contains bead ID and sequence, found on product support page. Note: we use *v2 A1* as it is the one based on *GRCh37*   
   
> optional files   

- sample sheet: .**csv **file with sample information e.g: gname, chip, and array location, gender. assay specific template is available under support files in illumina.com   
- **cluster file:** in .**egt **format containing genotype cluster positions, found in product specific page in illumina.com   
   
> output file   

.**bsc **file as the workspace, not that data is stored in the project file and no need to upoload them each time you run the project   
## Project creation   
- modify sample sheet by converting it to .csv according to the format provided, take care of the barcode format.   
   
> I m not sure yet if te project name must be added to the sample sheet as a specific name folder or just a random name.   

- ensure that sample names (i.e idat files names) are the same as in the sample sheet.    
   
## Quality Control   
> The control help identify outliers, their intensities are reported in Control Dashboards. They don't depend on thresholds and are applied to particular steps in the assay, they are used together with genomestudio's ctrls (call rate, p10,LogR dev)   

> Genotyping assay workflow   

1. Sample quantification, whole genome amplification   
2. fragmentation to small usable parts   
3. Resuspension in hybridizing buffer then hybridizing overnight   
4. wash and labeling probe by(1 nt extension and DNA removal)   
5. Staining with DNP (A/T) or biotin (G/C), (DNP=red channel, biotin=grn channel?   
- Sample dependent ctrl: those bind to the sample DNA, evaluate performance across all samples, are affected by any fail in the steps. Only for human,    
- sample independent ctrl: those hybridise to artificial DNA different from the sample to evaluate hybridizing quality and staining, are not informative about sample quality.   
![WhatsApp Image 2023-11-22 at 13.21.34_69545bf9](files\whatsapp-image-2023-11-22-at-13-21-34_69545bf9.jpg)    
   
   
## GenomeStudio Controls   
- Dashboard controls: y axis is intensity, x asix is the sample.    
- All the controls shown in figure are represented as graphs in the dashboard control. It must be checked even if call rates look normal. Each graph has a key that shows what to expect in the graph.   
- staining controls have unstable instensity levels across controls, because genomstudio autoscales to be able to see all samples in a given control   
- sample-dependent are for human DNA only   
   
> control details   

- **staining**: sample independent, oligos labeled with biotin or DNP bind directly to beads. These oligos go through same sample staining to test efficiency. 
expected: high red in red channel and high green in green channel. 
**note**: Never conclude anything from sample intensity levels, but rather ranges. go to call rates and sort then in ascending order then compare the order, you see that the it is random    
- **Extension**: sample independent, the probe makes a hairping on its self to mimic the free end for extension. 
expected: high A/T in red and high G/C in green channel. All values should be similar with in a sample.   
- **Target removal: **is sample independent negative ctrl, tests if sample was removed succesfully from probe after extension
expected: no signal, i.e only high in red channel, low/background in green channel, compare them to see the difference.   
- **Hybridization**: sample independent, 3 different conc of oligos test for hybridization efficiency using synthetic targets. Depend on stringency, extension and staining. Read only in green channels of each sample.
expected: three points for each sample with the order black>blue>green   
- **restoration**: since DNA can be used from paraffin embedded sections, it must be restored. This ctrl looks to only if restoration was applied or not. 
outcome: background in samples not restored, high signal in green channel for sample restored.   
- **stringency**: sample dependent, two probes bind sample (one to a conserved region and one is a mismatch for that region). Depend on sample quality, staining, extension. Only read in red channel
expected:strong +ve for perfect match (PM) datapoints, very low background for mismatch (MM) points.    
- **Non-specific binding: **sample dependent negative ctrl complementary ro bacterial DNA, marks contamination
expected: background in both red and green channels   
- **non-polymorphic: **the most important sample-dependent, 4 different probes on bead, they query bases in non-polymorphic regions in genome, depend on hybridization, staining, extension, sample quality. Shows overall assay quality.
expected: +vefor A/T in red, -ve for G/C in green channel   
   
> Evaluating controls   

- sort samples by call rate, if such samples have any other issues   
- sort by beadchip barcode and chip section, is a particular chip affected? is there a pattern in issues with samples, maybe related to chip position? It is very important to know what kind of chip you are working with, because if the call sample quality is normal then problem might be in chip/   
- sort bu plate/sample well, are wells in specific rows, columns, wells affected?   
- highlight problematic controls in the control chart   
![Illumina resources](files\illumina-resources.jpg)    
   
> GenomeStudio Metrics   

> Sample Metrics   

- **call rate**: % of loci that a genotype was called for that sample, i.e % of all SNPs & Indels successfully genotyped for a given sample (#calledmarkers÷#allmarkers)   
- **LogR deviation:** Deviation in the nomal intensity value, used to indicate contamination   
   
> SNP Metrics   

**Call frequency:** % of samples that a genotype was called for the SNP   
**GenTrain score: **how well did GenomeStudio identify groups of alleles in training dataset.   
- some others are avialable in datatable for determining cutoff and threshold   
   
## Theory of Genotype calling & Filtering samples   
- Illumina array scanner detects red & green intensities for each SNP locus   
- GenomeStudio converts channel intensity signals into genotypes   
- signals from red and green channels correspond to Alleles A & B in a sample fo a given SNP.    
- Theta (θ) is the angle on which the data point falls in a graph that separates genoypes from each other (AA=0, AB=0.5/45°, BB=1/90°)   
- For easier view, software converts normalized intensities from AB graph from cartesian coordinates (X=B,Y=A)to polar coordinates (θ=angle from X, R= A+B signals). This is to make visualisation easier.    
- GenTrain: is a clustering algo that determines location & shape of each genotype in cluster.   
- GenCall: calling algo that determines what each datapoint belongs to, measures how will a sample fits into a given cluster,    
- Evaluating samples: based on call rates, samples below threshold (normally 99%) can be exucluded from sample statistics and final report.   
   
## Stats Caclulation   
- After loading **.idat** files, if SNPs are not clustered (i.e if a **.egt **file was not provided on start), cluster SNPs manually from **Analysis Tab**T   
- Then calculate **Call rate **by choosing the column in the sample table and run the **calculator **icon   
- after that calculate stat for all samples from the **Analysis Tab**   
- Now samples are ready to be filtered   
   
## Filtering SNPs   
- The software runs GenTrain algo to determine cluster and assigns a score to each SNP.   
- **GenTrain score**: for a SNP. Is sample-specific, is determined by the position and shape of the cluster, broad or noncentralized clusters have lower score. Ideal score is 1 and have 3 clusters at 0,0.5 & 1.    
- **GenCall score:** SNP and sample specific, darker regions show threshold for calling genotypes (defaul is <0.15), centralized samples have higher score. 0-1 denoted as *score under each sample*   
- Guidelines for filtering exist but I don't really filter much   
   
   
## Filtering Samples (subsamples)   
- in case the samples of interest where genotyped in same chip with other samples   
- this is usually doable in GenomeStudio, but often projects in the software become heavy in processing   
- The easiest and fastest solution is often to filter the results with a downstream tool like PLINK   
