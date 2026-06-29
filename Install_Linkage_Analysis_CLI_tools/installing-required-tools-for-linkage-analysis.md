---
# yaml-language-server: $schema=schemas\page.schema.json
Object type:
    - Page
Backlinks:
    - Linkage Analysis
Creation date: "2025-09-04T13:17:10Z"
Created by:
    - Mohanad Hussein
id: bafyreiang6iy5pwvb7ozpizyuq4wixm5ylaiibvlnjwfpupvqivqaqf3o4
---
# Installing Required tools for Linkage Analysis in CLI   
# BCFTools    
- can be installed from conda directly, together with its plugins that are needed for sample prep before linkage   
   
```
# activate env or any other custom env
# note that using mamba instead of conda is more efficient to install/manage
conda activate bioinformatics
# check if idat2gtc exists with plugins download
conda install bioconda::bcftools
conda install bioconda::bcftools-gtc2vcf-plugin 
conda install bioconda::bcftools-liftover-plugin

# this direct download doesnt work with conda, check if idat2gtc exists
wget -P plugins http://raw.githubusercontent.com/freeseek/gtc2vcf/master/{idat2gtc.c,gtc2vcf.{c,h},affy2vcf.c,BAFregress.c}

```
# MEGA2   
> Detailed installation doc: https://sites.pitt.edu/~weeks/docs/mega2_html/mega2_documentation.html#toc-Subsection-8.15    

Full Doc: [https://sites.pitt.edu/~weeks/docs/mega2\_html/mega2.html](https://sites.pitt.edu/~weeks/docs/mega2_html/mega2.html)    
### Code Summaru   
```
git clone https://bitbucket.org/dweeks/mega2.git
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essentials
sudo apt-get install g++
sudo apt-get install csh
SAVE=True ./install.sh mega2compile


```
### Explained Steps   
- The Ubuntu that was installed is probably a bit old, that can be fixed by typing:   
   
```
sudo apt-get update
sudo apt-get upgrade

```
- The Ubuntu that was installed is a minimal set of package that you are expected to expand to your needs. In particular, you will find that make and g++ are missing. So type:   
   
```
sudo apt-get install build-essentials
sudo apt-get install g++

```
- and for completeness let’s get two more packages (or provide existing python exe)   
   
```
sudo apt-get install python
sudo apt-get install csh

```
- Now we are ready to clone a copy of Mega2; in the /home/ directory, type:   
   
```
cd
git clone https://bitbucket.org/dweeks/mega2

```
- The compilation is easy; type:   
   
```
cd mega2
bash
SAVE=True ./install.sh mega2compile

```
- (The installation step will fail because you are do not have write permissions for the install directory; So type   
   
```
sudo ./install.sh all

```
- to complete the installation. When it asks where to install, answer “/usr/bin”.   
   
Now you can cd to the directory with your data and type:   
```
mega2

```
- and answer the prompts to build a database. Note: if you data are on the Windows File System at  on drive x:,   
- you would refer to it via /mnt/x/ in Linux programs (like mega2).   
   
# Open-pedigree   
> https://github.com/phenotips/open-pedigree?tab=readme-ov-file    

deprecated open-source phenotips from the distributer: [https://github.com/phenotips/phenotips](https://github.com/phenotips/phenotips)    
Alper's tool: [https://hereat-lab.com/peddraw](https://hereat-lab.com/peddraw)    
- An important step in Linkage is to check the pedigree format given to programs like MEGA2   
- This is done by reading the pedigree file into a pedigree reader and visualizing it   
- Some options include HaploPainter, Tool written by Alper but the easiest to me is open-pedigree   
- open-pedigree is open-source built on top of phenotips after it went closed source   
- To use it you need JavaScript and others that pop up during install.   
   
# Merlin   
- Merlin Exists in the Bioconda Channel and can be downloaded into a conda environment   
- This is the best option as it saves the hassle     
   
```
# activate env or any other custom env (see merlin requirments if needed)
conda activate bioinformatics 
conda install bioconda::merlin

```
# PLINK   
- Also easily installable with conda   
   
```
conda activate bioinformatics 
conda install bioconda::plink

```
   
