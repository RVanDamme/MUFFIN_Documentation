## Manual Configuration

1. [Current parameter to change](#current-parameter-to-change)
2. [Edit existing profiles](#edit-existing-profiles) :
    - [The cpu/ram usage in cloud and local profiles](#The-cpu/ram-usage-in-cloud-and-local-profiles)
    - [The gcloud configuration](#The-gcloud-configuration)
3. [Create new profile](#Create-new-profile) :


## Current parameter to change
Currently we have the nextflow.config file that contains every parameter and configuration currently available.
If you want to edit here is a small guide to the different option to change with the line number in parenthesis:
* Change default cpus and ram edit those lines (7-8)
```
    cpus = "2"
    memory = '16g'
```

* Change default input (13-17)
If you don't want a default for a parameter change to "false" use simple quote to put a path. E.g.:
```
    illumina = './illumina'
    bin_classify = false
```

* Change default Databases same method as the input (20-22 & 26)
```
    sourmash_db = false
    sourmash_db = 'Path/to/db'
```

* Change default options (29-46)
```
    assembler = 'metaspades'
    modular = 'full'
```

* Change default output (49)
```
    output = './results'
```

## Edit existing profiles
You can edit and configure existing profiles to work for you.
The different parameter you can edit are:

### The cpu/ram usage in cloud and local profiles
To do so just open the cloud.config or local.config in the configs directory and change the value.
```
    withLabel: bwa { cpus = params.cpus ; memory = params.memory } # this calls the parameter defined in nextflow.config
    withLabel: bwa { cpus = 8 ; memory = '30g'}  # this is a user defined value
```

### The gcloud configuration
Please read the nextflow documentation before changing those parameter.
Documentation [HERE](https://www.nextflow.io/docs/latest/google.html)
We will list here the different parameter to change and their use.
```
    bucketDir = 'gs://bucket/work-dir' #the bucket directory where to store the temporary files of the run and the output files if desired
    google { project = 'project-name-111111'; zone = 'europe-north1-a' } # The name of your google project and the zone to execute the run (you can change the zone parameter for region if desired)
    google.lifeSciences.copyImage = 'google/cloud-sdk:latest' #if you want to change the original image started on google (we recommend the latest sdk)
    google.lifeSciences.preemptible = true # if you want to run on the preemptible version of google (cheaper but risk to kill the run)
    google.lifeSciences.bootDiskSize = "20GB" # the size of the boot disk, we recommend 20GB minimum
```



## Create new profile
If you want to create new profile (more advanced use of the nextflow configuration).
We recommend the creation of a specific configuration file in the configs directory and you call it in the nextflow.config file.
Please read the documentation provided by [nextflow](https://www.nextflow.io/docs/latest/config.html) about the configuration of a pipeline before attempting any modification.

If you implement a new profile, you will also need to edit the main.nf file and add
below line 113 if it's a engine or line 121 if it's an executer the following:

```
    workflow.profile.contains('new_profile_name') ||
```
This is to integrate the profile in the error handling

To implement an executor profile we highly recommend to read the documentation provided by [nextflow](https://www.nextflow.io/docs/latest/executor.html) profile.
Once you now the different parameter required for your executer, you can add a profile after the line 54 using the following format.

```
    profilename {
        process.executor = 'Executor_name'    
        Additionnal_parameter1 = variable1
        Additionnal_parameter2 = variable2
        ......
        includeConfig 'configs/name_specific_config.config'
    }
```

In the variable possible for the executor profile you have the variable specific to a cloud service (Amazon, gcloud)
We recommend you to read the documentation of nextflow before starting to integrate one of those cloud platform.

To create an engine profile it is based on the same structure.
You need to create a profile name (below line 87), and add the different configuration parameter.

```
    profilename {
        process.executor = 'Executor_name'    
        Required_parameter1 = variable1
        Required_parameter2 = variable2
        ......
        includeConfig 'configs/name_specific_config2.config'
    }
```

The last configuration is the additional files you call in the profiles.
Those files are there either to pinpoint the tools and their version(container.config or conda.config) or the individual cpu/ram usage (local.config or cloud.config).
The documentation of nextflow provide configuration exemple for conda, docker, singularity, shifter,...

To create those file we recommend the copy of an existing configuration file, to have the structure and all the label (software) called by MUFFIN.
You need then just to edit the values for cpu/ram or the container link (the nomenclature sometimes change between different container manager please read the nextflow documentation).

In the mean time you can easily edit the available configuration file by following this explanation.