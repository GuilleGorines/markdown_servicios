## How to perform the Exome Service

First step, as in every service in the BU-ISCIII, consists in setting the pending service to `in progress` state.

Now, go to the service directory, according to the nature of the service (to date, all exome services are placed in `IIER/genetica_humana`)
```
cd /processing_Data/bioinformatic/services_and_colaborations/SERVICE_CENTER/AREA
```
Create the directory with the Folder Name provided in the service information, and enter it. The service will be performed here.

```
mkdir $SERVICE_FOLDER_NAME && cd $_
```
Create the directory structure.
```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP 
```
Move to the RAW directory.
```
cd RAW
```
Inside the Service Info Display Page, check the name of the sequencing run(s) involved, and create a symbolic link to the reads that will be used. The expected quantity of reads depends on how many individuals are involved in this service, bear that in mind before proceeding any further. 
```
ln -s /srv/fastq_repo/$RUNS .
```
Exome services also need the enrichment BED file. This file, roughly, contains the list of the zones that have been enriched (corresponding to the exome of the analyzed organism). Depending on the tools used to enrich the exome, this file will be different. 

However, for samples enriched with the same kit, this file will be the same. These files can be found in `/processing_Data/bioinformatics/references/exome_beds/`

```
cp /processing_Data/bioinformatics/references/exome_beds/$USED_KIT/*.{interval,bed,txt} .
```

If a novel kit was used in this analysis, please check the "making new beds for new kits" section!

Once the BEDs and the samples are placed, it is time to move to the ANALYSIS directory.

```
cd ../ANALYSIS
```
We will find the lablogs inside a previous exome analysis.

```
cp $PREVIOUS_EXOME_JOB/ANALYSIS/lablog .
```
<span style="color:red;"> Check the lablog </span> of course, and execute it. 
```
bash lablog
```

This will create the following structure:
* 00-reads: where the reads will be symbolic-linked
* $(date '+%Y%m%d')_ANALYSIS01_EXOME:  where the analysis will be performed
* samples_id.txt: a file containing the name and path of the samples in RAW

Enther the analysis repository, and copy the lablog from a previous service:
```
cd *_ANALYSIS01_EXOME
cp $PREVIOUS_EXOME_JOB/ANALYSIS/*_ANALYSIS01_EXOME/lablog .
```
Check the lablog, and execute it

```
bash lablog
```
The following elements will be created:
* 00-reads
* _00_sarek.sh: this script will run nf-core SAREK, a nextflow program, in the HPC
* 02-postprocessing:
* 03-annotation:
* 99-stats:

sarek is the first step in the analysis. To run it properly, nextflow needs to be on its version 20.07.1. Luckily, there is a conda environment installed for it!

```
conda activate nextflowv20.07.1
```

Once the environment is activated, sarek can be called. The -bg (background) option is on, so the terminal can still be used while nextflow is running. 

```
nohup bash _00_sarek.sh &> $(date '+%Y%m%d')_sarek01.log &
```

This process will probably take a while. When it is finished, enter the 02-postprocessing directory
```
cd 02-postprocessing
```

Copy the lablog of this directory from a previous exome job

```
cp $PREVIOUS_EXOME_JOB/ANALYSIS/*_ANALYSIS01_EXOME/02-postprocessing\* .
```

Check and execute the lablog

```
bash lablog
```

This will create a lot of files:
* _01_select_snps.sh: first script, uses SELECTSNPS in the result of sarek to extract the snps.
* _02_select_indels.sh: second script to use, uses SELECTINDELS in the result of sarek to extract the indels.
* _03_snps_filtration.sh: filtrates the snps. USING THIS SCRIPT ALONE WONT WORK.
* _03_run_snps_filtration.sh: sends *_03_snps_filtration.sh* to the HPC (if the previous is sent directly, it may cause problems)
* _04_indels_filtration.sh: sends *_04_run_indels_filtration.sh* to the HPC (right like in the _03_snps_filtration.sh case)
* _04_run_indels_filtration.sh: filtrates the indels. USING THIS SCRIPT ALONE WONT WORK.
* _05_merge_vcfs.sh: uses MERGEVCF to merge the indel and the snps vcfs created by the previous scripts.
* _06_gzip.sh: GZIPs the previous vcfs.

To run these, a new conda environment is needed.

```
conda activate nf-core-sarek-2.6-fix-gatk
```

This scripts will be used sequentially.

```
bash _01_select_snps.sh
bash _02_select_indels.sh
```

Once the former scripts have finished, execute the next ones.
```
bash _03_run_snps_filtration.sh
bash _04_run_indels_filtration.sh
```

When those are finished, run the last two, the latter after the former is finished.
```
bash _05_merge_vcfs.sh
```
```
bash _06_gzip.sh
```

When those are finished, the directory will be filled with logs from the HPC, so its a good moment to hide those.
```
mkdir logs
mv FILINDEL* logs
mv FILSNP* logs
mv GZIP* logs
mv MERGE* logs
mv SELECTINDELS* logs
mv SELECTSNPS* logs
```

Once they have all finished correctly and everything is shiny clean, its time to go to the part 03 of the analysis.

```
cd ../03-annotation
```

As usual, we copy-check-execute the lablog from a previous service:

```
cp $PREVIOUS_EXOME_JOB/ANALYSIS/*_ANALYSIS01_EXOME/03-annotation/lablog .
```
```
bash lablog
```

This lablog will create more elements:
* exomiser
* vep
* _01_vcfmod.sh: modifes the vcf file with awk so VEP (Variant Effect Predictor) can work with it
* _02_variant_table.sh: creates a table containing the variants using bcftools and sed
* _03_vep_annotation.sh: uses VEP to annotate the effect of the variants in the vcf file. USING THIS SCRIPT ALONE WONT WORK.
* _03_run_vep_annotation.sh: sends the previous script to the HPC.
* _04_mod_vcf_vep.sh: uses grep and sed to modify the header in the vcf generated by VEP (so as they can be parsed in further steps). 
* _05_merge_dbnsfp_R.sh: copies and uses the R script "merge_dbNFSP.R" on the header generated by the previous script. 
* _06_merge_vcf_R.sh: copies and uses the R script "merge_parse.R" on the variants table and the header (?????)
* _07_conseq_filtering.sh: filters the table containing all annotated variants to show only those which are high or moderate.
* _08_exomiser_exome.sh: copies exomizer configuration file, moves to the exomizer directory, executes it on the HPC, and returns.

Exomiser requires a yaml as a configuration 

genePanelFilter: {geneSymbols: ['gene1','gene2','gene3']}


```
bash _01_vcfmod.sh
```
```
conda activate nf-core-sarek-2.6-fix-gatk
```
```
bash _02_variant_table.sh
```
```
conda activate ensembl-vep
```
```
bash _03_run_vep_annotation.sh
```

```

```



```
mkdir logs 
mv EXOMISER* logs
mv VEP* logs
```


```
cd ../99-stats
```

```
cp $PREVIOUS_EXOME_JOB/ANALYSIS/*_ANALYSIS01_EXOME/99-stats/lablog .
```

```
bash lablog
```

*_01_picardHsMetrics.sh: uses Picard to extract the metrics in the sarek results.
*_02_hsMetrics_all.sh: uses awk to create a new table with the needed metrics in the previous script results.


# Preparing the delivery
To prepare the delivery, we go to the root of the workdir.

```
cd ../../..
```

Change the `RAW` and the `TMP` directories, adding "_NC" to their names. 

```
mv RAW RAW_NC
mv TMP TMP_NC
```

