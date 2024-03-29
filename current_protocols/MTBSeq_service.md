

## In short
Here, we gather all the above steps without an explanation, so the service can be launched in a blast. However, take into account that you should always check the lablogs.

```
mkdir $SERVICE_FOLDER_NAME && cd $_
```
```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW
```

*fill raw directory*

```
cd ..
ASSEMBLY_TEMPLATE="/data/bi/pipelines/TEMPLATES/ASSEMBLY_TEMPLATE"
cp $ASSEMBLY_TEMPLATE/DOC/hpc_slurm_assembly.config DOC
cd ANALYSIS
MTBSEQ_TEMPLATE="/data/bi/pipelines/TEMPLATES/MTBSEQ_TEMPLATE"
cp $MTBSEQ_TEMPLATE/ANALYSIS/lablog .
bash lablog
ls
ls *ANALYSIS02_MTBSEQ
bash _01_copy_folder.sh
```
```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME
```
```
cd ANALYSIS/*ANALYSIS01*
MTBSEQ_TEMPLATE="/data/bi/pipelines/TEMPLATES/MTBSEQ_TEMPLATE"
cp $MTBSEQ_TEMPLATE/ANALYSIS/ANALYSIS01_ASSEMBLY/lablog .
bash lablog
module load Nextflow/21.10.6
module load singularity
bash _01_nf_assembly.sh
```

Wait a while for the assembly to end

```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME/ANALYSIS/*MTBSEQ
MTBSEQ_TEMPLATE="/data/bi/pipelines/TEMPLATES/MTBSEQ_TEMPLATE"
cp $MTBSEQ_TEMPLATE/ANALYSIS/ANALYSIS02_MTBSEQ/lablog .
bash lablog
conda activate mtbseq
bash _00_prepareRaw.sh
bash _01_preparesamples.sh
conda activate mtbseq
bash _02_mtbseq.sh
```
Wait another while for the MTBSeq to end
```
bash _03_gather_results
```
**IF THE SNP MATRIX IS REQUIRED:**

```
mkdir samples_all
cd samples_all
MTBSEQ_TEMPLATE="/data/bi/pipelines/TEMPLATES/MTBSEQ_TEMPLATE"
cp $MTBSEQ_TEMPLATE/ANALYSIS/ANALYSIS02_MTBSEQ/samples_all/lablog .
conda activate mtbseq
bash lablog
# NOTE: STILL TESTING WHAT MODULES TO UNLOAD FOR JAVA TO BE SET IN THE 1.8 VERSION NEEDED TO WORK
bash _01*
```
Last script shows error for some reason?
```
bash _02*
```

```
bash _03*
```


```
cd ../..
bash _02_copy_back.sh
```
