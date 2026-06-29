---
# yaml-language-server: $schema=schemas\page.schema.json
Object type:
    - Page
Backlinks:
    - Linkage Analysis
Creation date: "2025-08-27T10:23:27Z"
Created by:
    - Mohanad Hussein
Links:
    - anytype-the-everything-app.md
    - files\image.png
id: bafyreigdlw7tty4htamf4uguk6rknfi44hkkm6tobn65v4vb42bb7apjcq
---
# Linkage Analysis CLI workflow   
# GenomeStudio   
- This is part is quite similar to what is done before and documented in [https://object.any.coop/bafyreigbit4d2qpddjawu6wtrhdtxo3eiebf2dvfb5id3zjphdim6rxni4?spaceId=bafyreif3wap3rus7s5f4h556xas46ctj7jfmcrjctiqxbznu7465xcipyq.1na0xeo0zt44o](https://object.any.coop/bafyreigbit4d2qpddjawu6wtrhdtxo3eiebf2dvfb5id3zjphdim6rxni4?spaceId=bafyreif3wap3rus7s5f4h556xas46ctj7jfmcrjctiqxbznu7465xcipyq.1na0xeo0zt44o)    
[anytype — the everything app](https://object.any.coop/bafyreigbit4d2qpddjawu6wtrhdtxo3eiebf2dvfb5id3zjphdim6rxni4?spaceId=bafyreif3wap3rus7s5f4h556xas46ctj7jfmcrjctiqxbznu7465xcipyq.1na0xeo0zt44o)    
   
# BCFTools & PLINK (Preparing for Linkage)   
## If Input is VCF   
### Converting raw SNP data into VCF   
- VCF can be obtained from many input types, the tool to be used is bcftools plugins (installed separately from bcftools, see doc)   
- note that using the raw idat files means that you lose filtering applied by genomestudio. However, the direct ouput from genomestudio seems to have problems when read into bcftools. So keep that in mind.   
   
```
# i prefer gtc-to-vcf, also see idat-to-vcf
bcftools +gtc2vcf \
  --no-version -Oz \
  --bpm $bpm_manifest_file \
  --csv $csv_manifest_file \
  --egt $egt_cluster_file \
  --gtcs $path_to_gtc_folder \
  --fasta-ref $ref \
  --extra $out_prefix.tsv | \
  bcftools sort -Oz -T ./bcftools. | \
  bcftools norm --no-version -o $out_prefix.vcf.gz -Oz -c x -f $ref --write-index

gunzip -c $out_prefix.vcf.gz > $out_prefix.vcf
```
### Pre-editing VCF    
- MEGA2 might have some problems with vcf that has more than one family ID (FID) (i.e all samples must have the same chip number even if they were not sequenced together)   
- This could be manually fixed by opening the unzipped vcf in any text editor and changing sample names manually.   
   
```
nano input.vcf # nano can handle large vcfs
```
### Atomizing VCf   
- By default, SNPs with many alternative alleles are recorded as one entry in VCFs, but MEGA2 doesn't like it and wants a single entry per SNP (so no replicated SNP positions)   
- Atomization (splitting repeated SNP entries) and removing replicates and checking for multiple entries per SNP   
   
```
# Atomizing the vcf file with -m flag (split multi alleles) of bcftools norm
ref="/home/adduser/Linkage_Analysis/GRCh37_reference/human_g1k_v37.fasta"
bcftools norm -m - \
   -d both \
   -o Fam1_variants_from_FinalReport_atomized.vcf.gz \
   -Oz \
   -f $ref \
   Fam1_variants_from_FinalReport.vcf.gz
# this might not be necessary

# Recoding the atomized vcf file to recode GT values, this creates .ped and .map files to be used as inputs for MEGA2
plink --vcf Fam1_variants_from_FinalReport_atomized.vcf.gz --recode --out "/plink_recode/Fam1_variants_from_FinalReport_atomized_recode"

# to check multiple allelic sites in a vcf file
bcftools view -M 2 --types snps your_file.vcf.gz

```
### To change the format of VCF file
   
```
# unless needed, try not to use unzipped vcf, or rezip it after editing, for space efficiency
bcftools view -Oz -o output.vcf.gz input.vcf 
```
### Splitting VCF for samples   
```
# provide a list of sample names to be split
bcftools view -S subfamily_members.txt -Oz -o output.vcf.gz input.vcf.gz
```
## If Input is PLINK   
- PLINK data can take many shapes, that's because PLINK can handle many encoding formats of data in genotype studies (e.g chr, GT, phenotype etc)   
- The goal is to keep the original raw data format and create the input files required for each analysis/program separately   
- Mega2 accepts PLINK format (see exact requirements in menu of Mega2)   
   
# Open-pedigree   
- We have to check if the pedigree is interpretable by softwares (e.g Mega2)   
- Run whatever pedigree software you like, i use open-pedigree, see easy installation and usage in doc   
   
# MEGA2 Linkage Analysis   
> MEGA2: https://sites.pitt.edu/~weeks/docs/mega2_html/mega2.html    

### Before Mega   
- Change the sample-names in vcf and in .fam to have the same/single family (chip barcode) in all samples, MEGA2 gives an error when samples have different families in the first column of .fam (FIID). Unless the analysis spans more than one family.   
   
## Creating Database (from genotyping VCF )   
- Mega2 runs as an interactive tool in the command line, another option is to use a BATCH file for integration into pipelines, see MEGA2 documentation.   
- Mega2 formats the different types of input files and creates its own format to run subsequent analysis   
- The Database is created once then updated and created again using the output of the first create   
   
```
mega2 #opens the following menu, use 0 to skip through menus after choosing desired option

```
![image](files\image.png)    
> First Database creation     

- Run mega2 > Database Mode Menu: select Mega2 database create mode (then 0 to go out of the menu)   
- File input Menu: 1) choose number 12 for vcf/.gz input   
- File input Menu: 5) enter path to vcf file   
- File input Menu: 6) depending on how the sample name column in .fam is, choose 2 or 3   
- File input Menu: 7) enter path to .fam, must be a full pedigree (even samples with no SNPs), complete sex/pheno columns, samples should have same FIID column (usually FIID contains illumina chip barcode)   
- To proceed: press 0 and navigate through the different menus prompted, usually we just skip the default   
- Now a database file (**dbmega2.db**) must have been created in the output directory   
   
> Generating Mega2 Format Files from the Database   

- Database Mode Menu: select Mega2 database read mode (then 0 to go out of the menu)   
- Give the name of db file if not already given (then 0 to continue)   
- Analysis Menu: 33) Mega2 Format   
- Locus Reordering Menu: 3) Select Loci on multiple chromosomes   
- Locus Reordering Menu: 2) Select all chromosomes   
- Trait and covariate selection menu: skip it using 0   
- Output option menu for multiple chromosomes: 2) Combine chromosomes into one output file   
- Mega2 file name menu: skip and keep default names using 0   
- This will generate following files (**map, names, frequency, pedin, penetrance**) for all chromosomes combined   
   
> Recreating Database with updated cM values in the MAP file   

- first use the attached script below (Updating cM values in Mega2's Map) to update the "Map.h.a" column in map   
- Run mega2 > Database Mode Menu: select Mega2 database create mode (then 0 to go out of the menu)   
- File input Menu: 1) choose number 1 for MEGA2 format input   
- File input Menu: 2) correct the file suffix to match how the mega2 files were created, we do "all.mega2"   
- File input Menu: 3) enter path to names. file   
- File input Menu: 4) enter path to pedin file   
- File input Menu: 5) enter path to map file (this is cM updated file)   
- File input Menu: 7) enter path to frequency file   
- File input Menu: 8) enter path to penetrance file   
- To proceed: press 0 and navigate through the different menus prompted, usually we just skip the default   
- Now a new database file must have been created in the output directory   
   
> Merlin Files Generation from Database   

- Run mega2 > Database Mode Menu: select Mega2 database read mode (then 0 to go out of the menu)   
- Database input menu: 2) by default the dbmega2.db will be selected, only change if name is different   
- Analysis Menu: 27) Merlin Format   
- Locus Reordering Menu: 3) Select Loci on multiple chromosomes   
- Locus Reordering Menu: 2) Select all chromosomes   
- Trait and covariate selection menu: skip it using 0   
- Merlin run options menu: 2) Create **merlin\_model** from affection trait parameters   
- skip the remaining with 0 and e   
- now merlin files are generated in the same directory   
   
## Creating Database (from genotyping PLINK format)   
- There are some technical differences in using MEGA2 that apply when using input from plink   
- Instead of creating the dbmega2.db with vcf we using plink format as input: binary (preferred) or text/ped format.   
- As a part of a typical plink workflow, we have to ensure that naming of samples is compatible with mega2. Mainly: single family code for all members (unless doing mutli fam linkage) and parents naming etc.   
- Another important point is that plink 1.9 defaults map values to cM but mega2's default is morgans, so we have to explicitly tell mega2 to use cM by using the `--cM` when loading input.   
-     
   
# Merlin LOD score Analysis   
> Official tutorial: https://csg.sph.umich.edu/abecasis/Merlin/index.html    

[https://www.staff.ncl.ac.uk/heather.cordell/msc2008merlin.html](https://www.staff.ncl.ac.uk/heather.cordell/msc2008merlin.html)   
Merlin Source: [https://csg.sph.umich.edu/abecasis/Merlin/tour/linkage.html](https://csg.sph.umich.edu/abecasis/Merlin/tour/linkage.html)    
- We then run merlin with the outputs from Mega2 in addition to a model file (in case of Parametric Linkage)   
   
model file content: Affection, Disease Allele Frequency, Penetrances (Homozygote Ref, Heterozygote, Homozygote Alternate), Model Name   
  for AR: **default 0.01 0.00001,0.00001,0.99 AR**   
  for AD: **default 0.0001 0.0001,1.0,1.0 AD**   
```
# this will run merlin for one chromosome only, you can automate it with the helper script below
merlin -d "$data_file" -p "$ped_file" -m "$map_file" --model "$model_file" --prefix "$chr" --pdf --markerNames --grid 1 --tabulate --megabytes 4000

```
# Helper Codes/Commands   
### Updating cM values in Mega2's Map    
```
awk '
BEGIN {OFS="\t"} 
NR > 1 {
  $3 = $4 / 1000000; 
  print;
}
NR == 1 {
  print;
}
' map.all.mega2 > map_cM.all.mega2

```
### Automating Merlin LOD score calculation for all Chromosomes   
```
model_file="/home/adduser/Linkage_Analysis/mega2_analysis/mega2/merlin_model_AR"
chr_number=(01 02 03 04 05 06 07 08 09 10 11 12 13 14 15 16 17 18 19 20 21 22 X Y MT)

for chr in "${chr_number[@]}"; do
    map_file="/path/to/merlin_map.$chr"
    data_file="/path/to/merlin_data.$chr"
    ped_file="/path/to/merlin_ped.$chr"
    output_prefix="merlin_chr${chr}"

    echo "Running Merlin for chromosome $chr"
    merlin -d "$data_file" -p "$ped_file" -m "$map_file" --model "$model_file" --prefix "$output_prefix" --pdf --markerNames --grid 0.001 --tabulate --megabytes 4000
done
```
# Fine Mapping   
- When finding high LOD hits in some regions, it is a common practice to rerun linkage with the markers of those regions only instead of the entire genome.    
- This allows to obtain higher LOD scores as regions are small   
- when doing fine mapping, spacing between markers is removed/ignored as the search region already gets smaller   
   
### Haplotype Construction   
> drawing halpotypes: https://mtekman.github.io/haploforge/    

- Tools like merlin are used to reconstruct the haplotype   
- Then from the output we can draw the haplotype of samples   
- If we have haplotypes already (e.g for a smaller region scanned in excel) we can construct the haplotype right away in excel or so   
-    
