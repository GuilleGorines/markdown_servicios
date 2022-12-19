```
mkdir $SERVICE_FOLDER_NAME && cd $_
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
cd RAW
```
fill raw directory (use fastq-dump --gzip --split-files from the sra toolkit if from SRA, it can be found in the sratools env inside of the HPC)


```
cd ..
ASSEMBLY_TEMPLATE="/data/bi/pipelines/TEMPLATES/ASSEMBLY_TEMPLATE"
cp $ASSEMBLY_TEMPLATE/DOC/hpc_slurm_assembly.config DOC
cd ANALYSIS
PLASMIDID_TEMPLATE="/data/bi/pipelines/TEMPLATES/PLASMIDID_TEMPLATE"
cp $PLASMIDID_TEMPLATE/ANALYSIS/lablog .
bash lablog
ls
ls *ANALYSIS01_ASSEMBLY
bash _01_copy_folder.sh
```
```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME
cd ANALYSIS/*ANALYSIS01*
PLASMIDID_TEMPLATE="/data/bi/pipelines/TEMPLATES/PLASMIDID_TEMPLATE"
cp $PLASMIDID_TEMPLATE/ANALYSIS/ANALYSIS01_ASSEMBLY/lablog .
```
```
bash lablog [ + / - ]
module load Nextflow/21.10.6
module load singularity
bash _01_nf_assembly.sh
```
```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME/ANALYSIS/*PLASMIDID
PLASMIDID_TEMPLATE="/data/bi/pipelines/TEMPLATES/PLASMIDID_TEMPLATE"
cp $PLASMIDID_TEMPLATE/ANALYSIS/ANALYSIS02_PLASMIDID/* .
bash lablog
module load singularity
bash _01_plasmidID.sh
```
```
bash _02_summary_table.sh
```
```
cd ..
bash _02_copy_back.sh
```
