```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW
```
Fill RAW

```
cd ../REFERENCES
```
Fill REFERENCES

```
cd ../ANALYSIS
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
bash _01_fastqc_pre_trimming.sh
cd ../02-fastp-trim
cp $MLST_TEMPLATE/ANALYSIS/ANALYSIS01_MLST/02-fastp-trim/lablog .
bash lablog
bash _01_fastp_trimming.sh
```
Wait for the fastp process to end

```
MLST_TEMPLATE=/data/bi/pipelines/TEMPLATES/MLST_TEMPLATE
cd ../03-fastQC-post
cp $MLST_TEMPLATE/ANALYSIS/ANALYSIS01_MLST/03-fastQC-post/lablog .
bash lablog
bash _01_fastqc_post_trimming.sh
cd ../04-mlst
conda activate SRST2
cp $MLST_TEMPLATE/ANALYSIS/ANALYSIS01_MLST/04-mlst/lablog .
bash lablog
bash _01_srst2_mlst.sh
bash _02_srst2_summary.sh
```
