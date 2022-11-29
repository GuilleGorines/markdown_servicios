```
mkdir $SERVICE_FOLDER_NAME && cd $_
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW
```

**Fill directory**

```
cd ..
cd ANALYSIS
SNIPPY_TEMPLATE="/data/bi/pipelines/TEMPLATES/SNIPPY_TEMPLATE"
cp $SNIPPY_TEMPLATE/ANALYSIS/lablog .
bash lablog
bash _01*
```

```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME
cd ANALYSIS/*ANALYSIS01_SNIPPY
SNIPPY_TEMPLATE="/data/bi/pipelines/TEMPLATES/SNIPPY_TEMPLATE"
cp $SNIPPY_TEMPLATE/ANALYSIS/ANALYSIS01_SNIPPY/lablog .
bash lablog

cd 01-fastqc
SNIPPY_TEMPLATE="/data/bi/pipelines/TEMPLATES/SNIPPY_TEMPLATE"
cp $SNIPPY_TEMPLATE/ANALYSIS/ANALYSIS01_SNIPPY/01-fastqc/lablog .
bash lablog
module load FastQC
bash _01*

cd ../02-preprocessing
SNIPPY_TEMPLATE="/data/bi/pipelines/TEMPLATES/SNIPPY_TEMPLATE"
cp $SNIPPY_TEMPLATE/ANALYSIS/ANALYSIS01_SNIPPY/02-preprocessing/lablog .
bash lablog
module load Trimmomatic
bash _01*
```

```
bash _02*
```

```
cd ../03-preprocQc
SNIPPY_TEMPLATE="/data/bi/pipelines/TEMPLATES/SNIPPY_TEMPLATE"
cp $SNIPPY_TEMPLATE/ANALYSIS/ANALYSIS01_SNIPPY/03-preprocQC/lablog .
bash lablog
module load FastQC
bash _01*

cd ../04-snippy
SNIPPY_TEMPLATE="/data/bi/pipelines/TEMPLATES/SNIPPY_TEMPLATE"
cp $SNIPPY_TEMPLATE/ANALYSIS/ANALYSIS01_SNIPPY/04-snippy/lablog .
bash lablog

```
