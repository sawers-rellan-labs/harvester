# `harvester`
Combine candidate gene evidence from multiple sources

## Reference genomes

```{sh}
# in csh
tree /rsstu/users/r/rrellan/sara/ref

# /rsstu/users/r/rrellan/sara/ref
# ├── B73_RefGen_v2.fa
# ├── B73_RefGen_v2.fa.fai
# ├── Zm-B73-REFERENCE-GRAMENE-4.0.fa
# ├── Zm-B73-REFERENCE-GRAMENE-4.0.fa.fai
# ├── Zm-B73-REFERENCE-NAM-5.0.fa
# └── Zm-B73-REFERENCE-NAM-5.0.fa.fai
```

## Data to liftover into v5

Romero Navarro 2017 unfiltered data comes from [SEEDs dataset at CYMMIT](https://data.cimmyt.org/dataset.xhtml?persistentId=hdl:11529/10035)

```{sh}
# Genome version (AGPv2) inferred from the following line in
# All_Zea_GBS_Build_July_2012-Final-release_notes.pdf
bowtie2 -M 4 -p 15 \
        --very-sensitive-local \ 
        -x ../zeareference/bt2/ZmB73_RefGen_v2.fa \
        -U ./AllZeaMasterTags_c10_20120607.fq \
        -S AllZeaMasterTags_c10_20120613.sam
```

Filtering SNPs (MAF >0.01)

```{sh}
#!/usr/bin/env bash

TASSEL5=/Applications/TASSEL\ 5/run_pipeline.pl

for n in $(seq 1 10);
	do $TASSEL5  -h ../dataverse_files/AllZeaGBSv2.7_SEED_Beagle4_chr${n}.hmp.txt \
		-includeTaxaInFile taxalist.txt \
		-FilterSiteBuilderPlugin -siteMinAlleleFreq 0.01 -endPlugin \
		-export chr${n}_ ;
   done
```
 
This filtered data set was copied to the HPC:

```{sh}
tree /rsstu/users/r/rrellan/sara/SorghumGEA/data/RomeroNavarro2017/filtered
# /rsstu/users/r/rrellan/sara/SorghumGEA/data/RomeroNavarro2017/filtered
# ├── chr10_1.hmp.txt
# ├── chr1_1.hmp.txt
# ├── chr2_1.hmp.txt
# ├── chr3_1.hmp.txt
# ├── chr4_1.hmp.txt
# ├── chr5_1.hmp.txt
# ├── chr6_1.hmp.txt
# ├── chr7_1.hmp.txt
# ├── chr8_1.hmp.txt
# └── chr9_1.hmp.txt
```
As there is no v2 to v5 chainfile, that liftover must be done in two steps:

  1. v2 -> v4 
  2. v4 -> v5

```{sh}
# in csh
tree /rsstu/users/r/rrellan/sara/ZeaGEA/results

# /rsstu/users/r/rrellan/sara/ZeaGEA/results
# ├── glm_20181019
# │   ├── GLM_Q28_site                 # GWAS pvalues AGPv2 corrected with K=28 subpopulations in ADMIXTURE
# │   └── GLM_site                     # GWAS pvalues AGPv2 no correction for kinship or population structure
# └── pcadapt
#     ├── AGPv2_to_B73_RefGen_v4.chain
#     ├── B73_RefGen_v2_Chr.bed
#     ├── pcadapt_corrected.Rimage     # PCAdapt pvalues AGPv2
#     └── PCAdapt_v4.RDS               # PCAdapt pvalues AGPv4
```

`B73_RefGen_v2_Chr.bed` BED file is built like:

```{sh}
# in csh
# activate conda environment for samtools
conda activate /usr/local/usrapps/maize/sorghum/conda/envs/snakemake-tutorial

set ref=/rsstu/users/r/rrellan/sara/ref
cd $ref 

wget https://ftp.maizegdb.org/MaizeGDB/FTP/B73_RefGen_v2/B73_RefGen_v2.fa.gz 
gunzip B73_RefGen_v2.fa.gz
samtools faidx B73_RefGen_v2.fa

set pcadapt=/rsstu/users/r/rrellan/sara/ZeaGEA/results/pcadapt

cd $pcadapt

head -n 10 $ref/B73_RefGen_v2.fa.fai | awk 'BEGIN {FS="\t"}; {gsub("Chr", "", $1); gsub(":.*", "", $1); print $1 FS "0" FS $2 FS "ws"}' | sort -k1,1n > B73_RefGen_v2_Chr.bed

```

Chain files [(see specs)](https://genome.ucsc.edu/goldenPath/help/chain.html)  downloaded from [the MaizeGDB ftp site](http://ftp.gramene.org/CURRENT_RELEASE/assembly_chain/zea_mays/) won't be read by R unless spaces are replaced by tabs:

```{sh}
# in csh
wget "http://ftp.gramene.org/CURRENT_RELEASE/assembly_chain/zea_mays/AGPv3_to_B73_RefGen_v4.chain.gz"
gunzip AGPv3_to_B73_RefGen_v4.chain.gz
perl -i -pe ' if ( $_ !~ /chain/) {s/ +/\t/g}' AGPv3_to_B73_RefGen_v4.chain
```

## Other possible data for combining:

  - PBE scores (these are not p-values)
  
  - Differential expression in +P -P experiments



