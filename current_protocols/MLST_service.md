```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW
```
Fill RAW

```
cd ..
MLST_TEMPLATE="/data/bi/pipelines/TEMPLATES/MLST_TEMPLATE"
cp $MLST_TEMPLATE/ANALYSIS/lablog .
bash lablog
bash _01_copy_folder.sh
```

```
cd /data/bi/scratch_tmp/bi/$SERVICE_NAME/ANALYSIS/*ANALYSIS01_MLST*
MLST_TEMPLATE=/data/bi/pipelines/TEMPLATES/MLST_TEMPLATE
cp $MLST_TEMPLATE/ANALYSIS/ANALYSIS01_MLST/lablog .
bash lablog
cd 01-fastQC-pre
conda activate MLST
cp $MLST_TEMPLATE/ANALYSIS/ANALYSIS01_MLST/01-fastQC-pre/lablog .
bash lablog
```

