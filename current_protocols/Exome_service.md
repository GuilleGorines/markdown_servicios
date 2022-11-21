```
mkdir $SERVICE_FOLDER_NAME && cd $_
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW  
```
Fill the RAW directory with the appropriate samples

```
cd ..
EXOME_TEMPLATE=/data/bi/pipelines/TEMPLATES/EXOME_TEMPLATE
cp $EXOME_TEMPLATE/DOC/hpc_slurm_sarek.config DOC
cd ANALYSIS
EXOME_TEMPLATE=/data/bi/pipelines/TEMPLATES/EXOME_TEMPLATE
cp $EXOME_TEMPLATE/ANALYSIS/lablog .
bash lablog
bash _01*
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME/ANALYSIS/*ANALYSIS01_EXOME
```


```
EXOME_TEMPLATE=/data/bi/pipelines/TEMPLATES/EXOME_TEMPLATE
cp $EXOME_TEMPLATE/ANALYSIS/ANALYSIS01_EXOME/lablog .
bash lablog
module load Nextflow/21.10.6
module load singularity
bash _01*
```
