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

However, for samples enriched with the same kit, this file will be the same. Therefore, it is needed to find a previous service that used the very same kit, and make a symbolic link to its RAW.

```
ln -s /$PREVIOUS_EXOME_JOB/RAW/*.{interval,bed,txt} .
```

If a novel kit was used in this analysis, please check the "making new beds for new kits" section!

Once the BEDs and the samples are placed, it is time to move to the ANALYSIS directory.

```
cd ../ANALYSIS
```