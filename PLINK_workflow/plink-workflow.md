---
# yaml-language-server: $schema=schemas\page.schema.json
Object type:
    - Page
Backlinks:
    - Linkage Analysis
Creation date: "2025-10-24T08:07:53Z"
Created by:
    - Mohanad Hussein
Links:
    - files\plink_input_files_comparison.jpg
id: bafyreig4t6grqmje4wbrnpdakpbsif54ufmjlx67dyddowper7bbfcs4ma
---
# PLINK workflow   
> Documented here: https://www.cog-genomics.org/plink/1.9/formats#fam    

 Explained here: [https://www.youtube.com/watch?v=ZRyfpe1zqVg](https://www.youtube.com/watch?v=ZRyfpe1zqVg)    
tutorial: [https://cloufield.github.io/GWASTutorial/04\_Data\_QC/#download-genotype-data](https://cloufield.github.io/GWASTutorial/04_Data_QC/#download-genotype-data)    
   
# Input Files   
- There are two interchangeable types of input for PLINK, they both include same information: binary vs classic   
![plink_input_files_comparison](files\plink_input_files_comparison.jpg)    
   
### Binary PLINK input   
- **.fam**: includes sample names, paternal/maternal relations, sex and phenotype   
- **.bim**: includes variant information (chr, ID in database, cM, coordinate, allele1 and allele2)   
- **.bed**: genotype data stored in binary format   
   
###   Classic/Text PLINK input   
- **.ped**: a combination between .fam file followed by two columns for each SNP   
- **.map**: are similar to the bim file, they contain information of the markers in the array (chr, ID in database, cM, coordinate)   
   
###   Transposed PLINK input   
- **.tped**: transposed ped slightly different   
- **.tfam:** transposed fam   
   
# Workflows   
## Processing Input Files   
- It is advised to work with the binary PLINK fileset instead of classic   
- note that to read binaries to plink use (`--bfile`) and for classic use (`--file`)   
   
```
# creating/converting binary (fam,bim,bed) PLINK files from classic files (map,ped)
plink --file prefix_name --make-bed --out project_prefix_binary

```
- then we can generate stats about the input set, to check samples/SNPs   
   
```
plink --file prefix_name --missing --out prefix_name
```
- then we can check sample genders and X-heterozygosity   
   
```
plink --file prefix_name --check-sex --out prefix_name

```
- we can also subset samples using a sample list   
   
```
# the opposite (i.e remove/exclude) is also valid, replace makebed with recode to generate ped fileset and not binary
plink --bfile prefix_name --keep samples_list.txt --make-bed --out prefix_name

```
- depending on its version, plink changes chromosome names into numerical codes (i.e 1-26), to ensure keeping 1-22.X,Y,MT we use the MT flag:   
   
```
# find 0 chr snps first
cut -f1,2 file.map/.bim | awk '$1 == 0 {print $2}' > chr0_snps.txt
# remove 0 chromosome variants and set chr naming in output
plink --file prefix_name --exclude chr0_snps.txt --remove samples_fam2.txt --recode --allow-extra-chr --output-chr MT --out prefix_name

```
- to check number of variants in a map-like file for each chromosome   
   
```
# ideally run in scipt, but for convinience run in CLI and add semicolon before cut command
for i in {0..22} X Y XY MT; do 
	echo -n "chr $i = "
	cut -f1 file.map | grep "^$i" | wc -l; 
done
# OR CLI
for i in {0..22} X Y XY MT; do echo -n "chr $i = ";cut -f1 file.map | grep "^$i" |wc -l; done

```
- A general command to run   
   
```
plink --file prefix_name --update-parents parents.txt --pheno pheno.txt --recode --allow-extra-chr --output-chr MT --out prefix_name

```
## QCs of Input Genotyping Data (Inputs)   
- Still looking for a good reference   
   
   
