# How to perform the Assembly Service
Welcome to this (as) brief (as possible) tutorial on how to perform a Assembly Service as a member of the ISCIII's Bioinformatics Unit!

First of all, take the service and place it `in progress` in iSkyLIMS so the solicitant is notified that the service has started. This is a common step for all services provided by BU-ISCIII.

          **INSERT SUPER COOL SCREENSHOT YET TO BE PREPPED**

Now, **login in the HPC with your credentials**, and go to the appropriate service directory, according to the nature of the service (the center and the area):
```
cd /data/bi/services_and_colaborations/SERVICE_CENTER/AREA
```
Create the directory with the Folder Name you are given in the service information, and enter it. This will be the workdir for the service.
```
mkdir $SERVICE_FOLDER_NAME && cd $_
```

Create the directory structure
```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
```

Move to the RAW directory
```
cd RAW
```
Inside the Service Info Display page, check the name of the sequencing runs that are involved.

Create a symbolic link to the reads that will be used. 
```
ln -s /srv/fastq_repo/$RUNS .
```
Optionally, check how many reads there are, for future references and error tracking. Take into account that the samples might be either *paired-end* or *single-end*.
```
ls | wc -l
```

Now, move back

```
cd ..
```
Record the assembly template path, we will copy all the lablogs from there (of course, this will only work inside the BU-ISCIII file system).

```
ASSEMBLY_TEMPLATE=/data/bi/pipelines/TEMPLATES/ASSEMBLY_TEMPLATE
```
Copy necessary configuration files into the DOC directory

```
cp $ASSEMBLY_TEMPLATE/DOC/hpc_slurm_assembly.config DOC
```

Now go to the analysis directory

```
cd ANALYSIS
```

Enter the ANALYSIS directory inside the service, and copy the corresponding lablog from the template. 

```
cp $ASSEMBLY_TEMPLATE/ANALYSIS/lablog .
```

Check the lablog just in case (do always check the lablog to avoid disasters), then execute it.
```
cat lablog
bash lablog
```
This first lablog will create the following elements:
* **00-reads**: directory where the reads will be stored, with a simpler naming. 
* **$(date '+%Y%m%d’)_ANALYSIS01_ASSEMBLY**: directory where the whole analysis will be performed.
* **samples_id.txt**: file containing the name of the samples in RAW (this is, without the extension and the `R1` and `R2` tags).
* **_01_copy_folder.sh**: this script copies the whole directory structure to the part of the HPC where the analysis will be performed.
* **_02_copy_bach.sh**: this script does the exact opposite of `_01_copy_folder.sh`: once finished, it will copy the whole analysis directory back to its storage place.

Move all the analysis directory to the working place with:

```
bash _01_copy_folder.sh
```

The next step is going to that directory. In this case, it will be:
```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME
```

Go to the analysis directory and copy the next lablog

```
cd ANALYSIS/*_ANALYSIS01_ASSEMBLY
cp $ASSEMBLY_TEMPLATE/ANALYSIS/ANALYSIS01_ASSEMBLY/lablog .
```
Check and execute the lablog. This second lablog is a little trickier, as it requires a parameter, which is the gram of the bacteria you are working with. Therefore, you need to run `bash lablog +` for gram positive bacteria, and `bash lablog -` for gram negative bacteria. Here, we provide you with a small list of the most studied bacteria in the ISCIII, and its gram tincture results. Do not worry anyways, this script won't work unless the gram value is given.

**Gram positive bacteria**: _Listeria monocytogenes_, _Mycobacterium tuberculosis_

**Gram negative bacteria**: _Neisseria gonorrhoeae_, _Escherichia coli_, _Shigella sonei_, _Salmonella enterica_

```
cat lablog
bash lablog [+|-]
```
The lablog will create several elements: 
* **_01_nf_assembly.sh**: script to perform the assembly pipeline.
* **samplesheet.csv**: the file gathering all samples used as input for the assembly pipeline
* **assembly.sbatch**: the file with all the instructions of the pipeline for the HPC.
* **samples_id.txt**: symbolic link to the file with the list of all names of involved samples (without the extension and the `R1` and `R2` tags).
* **00-reads**: symbolic link to the directory with all the files with a simplified name.

Now, the only step left is preparing the HPC modules for this pipeline, and running said pipeline

```
module load Nextflow singularity
bash _01_nf_assembly.sh
```

Now, just check on the progress from time to time (this can be done with `squeue`, or with `tail -f` on the log file that will appear in that very directory). 

## Preparing the delivery

Before delivering the service, it is necessary to **remove not-so-useful and redundant data** (such as the trimmed reads). Go to the root directory of the service.
```
cd ../../..
```

Change the name of the `RAW` and `TMP` directories, adding a `_NC` (no copy) to their names
```
mv RAW RAW_NC
mv TMP TMP_NC
```

The work directory generated by this pipeline takes a lot of space, and its not necessary, so deleting it will safe us that precious disk capacity.
```
rm -rf ANALYSIS/*ASSEMBLY/work
```

With all of this, the service is ready to be delivered. However, the result revision is still pending.

## Revising the results

Once all processes have ended (remember to always check this with `qstat`), its time to go over the results.

First of all, check the **quality of the reads**. This can be checked in the `99-stats` directory. The MultiQC report can be found there. If you wanted to check the FastQC or FastP report, those can be found in `01-preprocessing`

Secondly, check the **quality of the assembly**. A Quast report can be found in `03-assembly`.

Last, but not least, check the ``kmerfinder.csv`` file in `99-stats` to see if there are hints of **contamination**.

Make sure to take note of any anomalies. Sometimes something might be off, or might not make sense. Try to find a reason for this (wrong parameters in a process, not-so-good quality of the reads). Maybe, repeating the process with some changes is necessary, take note of that as well.


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
(use ```fastq-dump --split-files``` from the sra toolkit if from SRA)

```
cd ..
ASSEMBLY_TEMPLATE="/data/bi/pipelines/TEMPLATES/ASSEMBLY_TEMPLATE"
cp $ASSEMBLY_TEMPLATE/DOC/hpc_slurm_assembly.config DOC
cd ANALYSIS
ASSEMBLY_TEMPLATE="/data/bi/pipelines/TEMPLATES/ASSEMBLY_TEMPLATE"
cp $ASSEMBLY_TEMPLATE/ANALYSIS/lablog .
bash lablog
ls
ls *ANALYSIS01_ASSEMBLY
bash _01_copy_folder.sh
```
```
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME
cd ANALYSIS/*ANALYSIS01*
ASSEMBLY_TEMPLATE="/data/bi/pipelines/TEMPLATES/ASSEMBLY_TEMPLATE"
cp $ASSEMBLY_TEMPLATE/ANALYSIS/ANALYSIS01_ASSEMBLY/lablog .
```
```
bash lablog [ + / - ]
```
```
module load Nextflow singularity
bash _01_nf_assembly.sh
```
```
cd ..
bash _02_copy_back.sh
```



## Troubleshooting

Sometimes, incidences ocurr. Here, we register all troubleshooting to try and help solve it fast and easy.

**Wrong reference** (21-9-2021): sometimes, investigators provide their samples stating that they belong to a certain organism. The kmerfinder result might prove this right, or not. If the results agree with the investigator, the analysis can continue. Hoever, if it does not, please note it down before proceeding.   
	
	
**User namespace** (22-9-2021): 
Found in: kmerfinder process

Description: kmerfinder process stopped early, and logs showed the following message:

`ERROR : Failed to create user namespace: user namespace not supported by your system`

Solution: Restarting the ssh connection worked

**NEWUSER namespace runtime** (15-2-2022)
Found in: kmerfinder process

Description: kmerfinder process stopped early, logs showed the following message:

`Failed invoking the NEWUSER namespace runtime: Invalid argument`
`ABORT  : Retval = 255`

