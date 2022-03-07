# harvester
Combine candidate gene evidence from multiple sources

Reference genomes

```
tree /rsstu/users/r/rrellan/sara/
# ref
# ├── B73_RefGen_v2.fa
# ├── B73_RefGen_v2.fa.fai
# ├── Zm-B73-REFERENCE-GRAMENE-4.0.fa
# ├── Zm-B73-REFERENCE-GRAMENE-4.0.fa.fai
# ├── Zm-B73-REFERENCE-NAM-5.0.fa
# └── Zm-B73-REFERENCE-NAM-5.0.fa.fai
```

Data to liftover into v5:

```
tree /rsstu/users/r/rrellan/sara/ZeaGEA/results/

# results
# ├── glm_20181019
# │   ├── GLM_Q28_site
# │   └── GLM_site                     # GWAS pvalues AGPv2
# └── pcadapt
#     ├── AGPv2_to_B73_RefGen_v4.chain
#     ├── B73_RefGen_v2_Chr.bed
#     ├── pcadapt_corrected.Rimage     # PCAdapt pvalues AGPv2
#     └── PCAdapt_v4.RDS               # PCAdapt pvalues AGPv4
```


