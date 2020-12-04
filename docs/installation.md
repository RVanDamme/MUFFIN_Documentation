## Installation

### base installation
You need to install nextflow Version 20.07+ ( https://www.nextflow.io/ )
```sh
# verify Java version (at least version 8+)
java -version 

# Setup nextflow (it will create a nextflow executable file in the current directory)
curl -s https://get.nextflow.io | bash

# If you want the pipeline installed locally use the following
git clone https://github.com/RVanDamme/MUFFIN.git

# If you want to not install the pipeline use the following when running nextflow

nextflow run  RVanDamme/MUFFIN --parameters.....

```

### For conda usage
If you use conda, you don't need extra installations.
An error might occur with the installation of metawrap, if so please consult [Troubleshooting](#troubleshooting).

### For gcloud usage
If you use the google lifescience ressources you first need to setup a few parameters.

In the nextflow.config you need to change the parameters of gcloud to correspond to your project (line 67 to 78).
```sh
    gcloud {  
        //workDir = "/tmp/nextflow-docker_pipelines-$USER"
        process.executor = 'google-lifesciences'
        process.memory = params.memory
        bucketDir = 'gs://bucket/work-dir' // change this to your bucket where you want the workfile to be stored
        google { project = 'project-name-111111'; zone = 'europe-north1-a' } // insert your project ID as well as the zone(s) you want to use
        // you can also use {region = 'europe-north1'} instead of zone
        google.lifeSciences.copyImage = 'google/cloud-sdk:latest'
        google.lifeSciences.preemptible = true
        google.lifeSciences.bootDiskSize = "10GB"
        google.lifeSciences.debug = true
        //includeConfig 'configs/preemptible.config'
    }
```
You will also have to change the bucket to store the different database in:
    /modules/checkmgetdatabases.nf ; /modules/eggnog_get_databases.nf ; /modules/sourmashgetdatabase.nf

To do so just edit the line using your bucket. keep the structure for more clarity (e.g. keep the "/databases-nextflow/sourmash" part).

Example:
```sh
if (workflow.profile.contains('gcloud')) {publishDir 'gs://gcloud_storage/databases-nextflow/sourmash', mode: 'copy', pattern: "genbank-k31.lca.json.gz" }
#becomes
if (workflow.profile.contains('gcloud')) {publishDir 'gs://MY_STORAGE/databases-nextflow/sourmash', mode: 'copy', pattern: "genbank-k31.lca.json.gz" }
```
If you desire run on gcloud without the preemptible parameter activated just edit the line 74 of nextflow.config to false.




### For containers usage
If you use containers either docker or singularity, you don't need extra installations.

### For usage of software installed locally
You just need to have all the software used in the pipeline (see table above) installed and in your $PATH.

## Test the pipeline
To test the pipeline we have a subset of 5 bins available at https://osf.io/9xmh4/
A detailed explanation of all the parameter is available in [Usage](#usage), the most important for the test is the profile executor and engine.
To run it you just need to add "test" in the -profile parameter e.g.:
```
#test locally with conda, you need to specify cpus and ram available
nextflow run RVanDamme/MUFFIN --output results_dir  --cpus 8 --memory 32g -profile local,conda,test

#test locally with docker, you can change the cpus and ram in configs/containers.config
# this test also run the transcriptomics analysis with --rna
nextflow run RVanDamme/MUFFIN --output results_dir --rna -profile local,docker,test

#test using gcloud with docker, you can change the cpus and ram in configs/containers.config
# this test use flye instead of spades with the --assembler metaflye
nextflow run RVanDamme/MUFFIN --output results_dir --assembler metaflye -profile gcloud,docker,test
```
The subset contains also RNA data to test with transcriptomics analysis you just need to activate it using "--rna"
The results of the different test run are available at https://osf.io/m5czv/
