```
mkdir $SERVICE_FOLDER_NAME && cd $_
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW  
```
Fill the RAW directory with the corresponding samples

```
cd ..
EXOME_TEMPLATE=/data/bi/pipelines/TEMPLATES/EXOME_TEMPLATE
cp $EXOME_TEMPLATE/DOC/hpc_slurm_sarek.config DOC
```
Now, get the **appropriate bed file** from the template:

| Kit | Commmand |
| ------------- | ------------- |
| NEXTERA FLEX for enrichment by Illumina  | `cp $EXOME_TEMPLATE/REFERENCES/Nextera_flex_for_enrichment–Illumina/* REFERENCES`  |
| XGEN exome research panel  | `cp $EXOME_TEMPLATE/REFERENCES/xgen-exome-research-panel/* REFERENCES`  |
| KAPA exome research panel | `cp $EXOME_TEMPLATE/REFERENCES/KAPA_HyperExome_capture/* REFERENCES`|

```
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

```
cd 02-postprocessing
EXOME_TEMPLATE=/data/bi/pipelines/TEMPLATES/EXOME_TEMPLATE
cp $EXOME_TEMPLATE/ANALYSIS/ANALYSIS01_EXOME/02-postprocessing/lablog .
bash lablog
```

```
cd 03-annotation
EXOME_TEMPLATE=/data/bi/pipelines/TEMPLATES/EXOME_TEMPLATE
cp $EXOME_TEMPLATE/ANALYSIS/ANALYSIS01_EXOME/03-annotation/lablog .
bash lablog
```
