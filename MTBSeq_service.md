

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
MTBSEQ_TEMPLATE="/data/bi/pipelines/TEMPLATES/MTBSEQ_TEMPLATE"
cd ..
cp $MTBSEQ_TEMPLATE/DOC/hpc_slurm_assembly.config DOC
cd ANALYSIS
cp $TEMPLATE/ANALYSIS/lablog .
bash lablog
ls
ls *ANALYSIS01_MTBSEQ
bash _01_copy_folder.sh
```
```
MTBSEQ_TEMPLATE="/data/bi/pipelines/TEMPLATES/MTBSEQ_TEMPLATE"
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME
cd ANALYSIS/*ANALYSIS01*
cp $MTBSEQ_TEMPLATE/ANALYSIS/ANALYSIS01_MTBSEQ/lablog .
bash lablog
```

```
module load Nextflow singularity
bash _01_nf_assembly.sh
```

Wait a while for the assembly to end

```
cd 05-MTBSeq
cp $MTBSEQ_TEMPLATE/ANALYSIS/ANALYSIS01_MTBSEQ/05-MTBSeq/lablog .
bash lablog
bash _00_prepareRaw.sh
bash _01_preparesamples.sh
conda activate mtbseq
bash _02_mtbseq.sh
```
```
bash _03_gather_results
```

