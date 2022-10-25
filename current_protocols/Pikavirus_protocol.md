# How to perform the Pikavirus Service
Welcome to this (as) brief (as possible) guide on how to run PikaVirus as a member of the Bioinformatics Unit in the Carlos III Health Institute in Madrid. 

First of all, as in every usual BU-ISCIII service to date, take the service and set it to `in progress` in iSkyLIMS, so the solicitant is notified that their service has been started.

Now, **login in the HPC with your credentials**, and go to the appropriate service directory, depending on the nature of the service (this is, the service and the area).

```
cd /data/bi/service_and_colaborations/SERVICE_CENTER/AREA
```

Create the directory with the Folder Name you have been given in the service information in iSkyLIMS, and enter it. This directory will be the starting point for the service.

```
mkdir $SERVICE_FOLDER_NAME && cd $_
```

Once inside (check with `pwd` if necessary), create the usual BU-ISCIII directory structure.

```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP
```

Move to the RAW directory, and fill it with the involved samples. These can be found in the service info.

```
cd RAW
ln -s /srv/fastq_repo/PROJECT/SAMPLES* .
```

Dont forget to check whether all the needed reads are present in the directory before you continue (for instance, use `ls | wc -l`)

Now, go back to the root directory. As PikaVirus is a NextFlow pipeline, we will use a *config* file for it. This can be found inside of the PikaVirus template directory we have created (which, to this day, is placed in `/data/bi/pipelines/TEMPLATES/PIKAVIRUS_TEMPLATE`). This time, we will copy it inside of the `DOC` directory. 

```
cd ..
PIKAVIRUS_TEMPLATE=/data/bi/pipelines/TEMPLATES/PIKAVIRUS_TEMPLATE
cp $PIKAVIRUS_TEMPLATE/DOC/hpc_slurm_pikavirus.config DOC
```

This file contains the params for a standard PikaVirus service in BU-ISCIII. There's usually no need to change it, but of course this might be required depending on the service specifications.

Once the config file is ready and vibing in the `DOC` directory, let's move on to the ANALYSIS part. First step, of course, is to go to the `ANALYSIS` directory.

```
cd ANALYSIS
```

Once here, we will copy the lablog (this is, code-making code that allows an easier approach to the analysis) from the PikaVirus template metioned previously, and execute it.

```
PIKAVIRUS_TEMPLATE=/data/bi/pipelines/TEMPLATES/PIKAVIRUS_TEMPLATE
cp $PIKAVIRUS_TEMPLATE/ANALYSIS/lablog .
bash lablog
```

Executing this first lablog will create the following elements inside of the directory:

* **samples_id.txt**: a file containing the name of each sample in the RAW directory. 
* **00-reads**: a directory containing symbolic links to the sample files in RAW, renamed for an easier access.
* **${DATE}_ANALYSIS01_PIKAVIRUS**: a directory where the real analysis will be held. 
* **_01_copy_folder.sh**: a bash script that will take all the content in the analysis file and move it to the `scratch_tmp` directory, where the whole analysis can be executed.
* **_02_copy_bach.sh**: a bash script that will take all the resulting files after pikavirus is run back to this location once the analysis in `scratch_tmp` has been finished. 

**NOTE**: sometimes, the naming on your samples might be different from usual. In such cases, it will be necessary to modify the lablog so the `samples_id.txt` file can be correctly created.

Next step is taking this whole directory to `scratch_tmp`. This is achieved by executing the `_01_copy_folder.sh` script. After this, we have to go to the corresponding directory in `scratch_tmp`. The name of this directory will remain the same it was in this directory (the one provided by iSkyLIMS at the beginning). 

```
bash _01_copy_folder.sh
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME/ANALYSIS
```

Once here, go to the **${DATE}_ANALYSIS01_PIKAVIRUS** directory generated in the prior lablog.

```
cd *PIKAVIRUS*
```

Now its time to copy the second and last lablog for this service. Again, we will take it from the corresponding folder in the same template as before, and execute it.

```
PIKAVIRUS_TEMPLATE=/data/bi/pipelines/TEMPLATES/PIKAVIRUS_TEMPLATE
cp $PIKAVIRUS_TEMPLATE/ANALYSIS/ANALYSIS01_PIKAVIRUS/lablog .
bash lablog
```

This will create the following elements in the directory:

* **00-reads**: a symbolic link to the `00-reads` directory created by the previous lablog.
* **samples_id.txt**: a symbolic link to the `samples_id.txt` file created by the previous lablog.
* **samplesheet.csv**: a csv file containing the name of each sample, and the location of its associated files in 00-reads. This will be the input for PikaVirus.
* **pikavirus.sbatch**: the sbatch file for the HPC to run PikaVirus. If you check it, you'll find that it accesses the config file we copied to the DOC directory, and it sets some other parameters. This is because most of the parameters in the config file will always remain the same, however the ones in this file can be changed according to circunstances more easily, so it comes handy to have them here.
* **_01_nf_pikavirus.sh**: this file is just the script to execute the sbatch file in the HPC.

The last step is to activate the corresponding modules (in this case, only the Nextflow module) in the HPC, and run the `_01_nf_pikavirus.sh`.

```
module load Nextflow/21.10.6
bash _01_nf_pikavirus.sh
```

Check how the process is going with `squeue`. When it has finished, please check the log file generated by the pipeline to check that everything has been executed successfully. Then, you can check the PikaVirus results. Lastly, it is time to take this out of `scratch_tmp` by using the `_02_copy_back.sh` script generated before.

```
cd ..
bash _02_copy_back.sh
```

## Preparing the delivery

Before the delivery, it is necessary to **remove unuseful or redundant data**. Go to the root directory of the service, and change the name of the `RAW` and `TMP` directories, adding a `_NC` (no copy) to their names
```
mv RAW RAW_NC
mv TMP TMP_NC
```
The `work` directory generated by this pipeline takes a lot of space, and its not necessary, so deleting it will safe us that precious disk capacity.
```
rm -rf ANALYSIS/*PIKAVIRUS/work
```

With this, the Pikavirus service is ready to be delivered.

# In short

Here are the exact commands that need to be executed for the pikavirus service to be executed, just without all that text. It is recommended for this commands to run smoothly to have the `SERVICE_FOLDER_NAME` bash variable established to the correct directory.

```
mkdir $SERVICE_FOLDER_NAME && cd $_
mkdir ANALYSIS  DOC  RAW  REFERENCES  RESULTS  TMP
cd RAW
```
Fill the RAW directory (use ```fastq-dump --split-files``` from the sra toolkit if the samples come from SRA)

```
cd ..
PIKAVIRUS_TEMPLATE=/data/bi/pipelines/TEMPLATES/PIKAVIRUS_TEMPLATE
cp $PIKAVIRUS_TEMPLATE/DOC/hpc_slurm_pikavirus.config DOC
cd ANALYSIS
cp $PIKAVIRUS_TEMPLATE/ANALYSIS/lablog .
bash lablog
bash _01_copy_folder.sh
cd /data/bi/scratch_tmp/bi/$SERVICE_FOLDER_NAME/ANALYSIS/*PIKAVIRUS*
cp $PIKAVIRUS_TEMPLATE/ANALYSIS/ANALYSIS01_PIKAVIRUS/lablog .
bash lablog
module load Nextflow
bash _01_nf_pikavirus.sh
```

# Troubleshooting
Sometimes, errors happen. This section of the guide gathers all errors that we have had with PikaVirus, the date they happened, and the solution we gave to them. Hopefully, they will be a good reference!

- **2022-10-17**: *variable X not defined*. For some reason, and with no changes to the code whatsoever (and a correct functioning until then), NextFlow started reporting this error. This happened in version 22.04.0. Solution was going back to version 21.10.6, available both in module call, and in the "nextflow" conda environment. Further analysis is required.

