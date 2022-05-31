```
mkdir ANALYSIS  DOC  RAW  REFERENCES  RESULTS  TMP
cd RAW
```
Fill RAW

```
cd ..
PIKAVIRUS_TEMPLATE=/data/bi/pipelines/TEMPLATES/PIKAVIRUS_TEMPLATE
cp $PIKAVIRUS_TEMPLATE/DOC/hpc_slurm_pikavirus.config DOC
cd ANALYSIS
cp $PIKAVIRUS_TEMPLATE/ANALYSIS/lablog .
cd *PIKAVIRUS*
cp $PIKAVIRUS_TEMPLATE/ANALYSIS/ANALYSIS01_PIKAVIRUS/lablog .
module load Nextflow
bash _01_nf_pikavirus.sh
```

