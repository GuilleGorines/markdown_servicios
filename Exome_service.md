## How to perform the Exome Service

First step, as in every service in the BU-ISCIII, consists in setting the pending service to `in progress` state.

Now, go to the service directory, according to the nature of the service (to date, all exome services are placed in `IIER/genetica_humana`)
```
cd /processing_Data/bioinformatic/services_and_colaborations/SERVICE_CENTER/AREA
```
Create the directory with the Folder Name provided in the service information, and enter it. The service will be performed here.

```
mkdir $SERVICE_FOLDER_NAME
cd $SERVICE_FOLDER_NAME
```
Create the directory structure
```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP 
```
Move to the RAW directory