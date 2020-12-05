## Usage

### Automated usage 
To avoid writing all the parameter in the CLI you can use the additional "-params-file" and provide a .yml file that contains all the parameters available for MUFFIN and described below.
You can find the MUFFIN_params.yml file in the base of MUFFIN directory.

Exemple:
MUFFIN_params.yml
```
assembler   : "metaspades"
ouptut      : "path/to/resultdir"
illumina    : "fastq_ill/"
ont         : "fastq_ont/"
cpus        : 16
memory      : "64g"
modular     : "full"
```

MUFFIN comand:
```
path/to/nextflow run $MUFFIN_pipeline -params-file MUFFIN_params.yml -profile local,conda,test
```
$MUFFIN_pipeline is either "path/to/MUFFIN/main.nf" or "RVanDamme/MUFFIN"

### Basic usage

```
path/to/nextflow run $MUFFIN_pipeline --output results_dir --assembler $assembler --illumina fastq_ill/ --ont fastq_ont/ --cpus 16 --memory 64g --modular full -profile $profile_executor,$profile_engine
```
 $MUFFIN_pipeline is either "path/to/MUFFIN/main.nf" or "RVanDamme/MUFFIN"

 $assembler is either:
  - "metaspades" for hybrid assembly
  - "metaflye" for long-read assembly with short-reads polishing

 $profile_executor can be:
  - "local" to run on your computer
  - "gcloud" to run on google life science cloud computing (you need to setup your project in nextflow.config)
  - "slurm" to run on HPC using slurm (e.g. UPPMAX)

 $profile_engine can be:
  - "local_engine" to execute the software installed locally
  - "conda" to execute using conda installation
  - "docker" and "singularity" to execute using the docker container

You can add "-resume" at the end of the command to restart it while keeping the process that succeeded
This is often used in case or error in the pipeline or if you modify slightly the command and want to avoid running everything again.
One exemple is to run the pipeline without RNA and rerun it adding RNA data, in this specific case the second time you add:
```
--rna path/to/rna -resume
```
Only the transcript processes and final parsing will be run

### Advanced usage

You can use RNA data to have transcriptomics analysis to do so add "--rna path/to/fastq_rna/"

You can run only partially the pipeline to do so change --modular to the right parameter:
 - "full" run the 3 steps of MUFFIN (assemble, classify, annotate)
 - "assemble" run the assembly and binning
 - "classify" run the classification of the bins (require a different input)
 - "annotate" run the annotation step of the bins (require a different input) and RNA if provided
 - "assemb-class" run assemble and classify step
 - "assemb-annot" run assemble and classify step
 - "class-annot" run classify and annotate step (require a different input)

To run classify and annotate independently from the assemble you need to provide a CSV file of the bins
The structure of the file should correspond to:
```
Samplename,path/to/bin1.fa
Samplename,path/to/bin2.fa
...
Samplename,path/to/binX.fa
Samplename,path/to/binY.fa
```
If you run "classify" with or without "annotation" use "--bin_classify"
If you run "annotate" without "classify" use "--bin_annotate"



## Options

### --cpus
* number of thread available
* default 2

### --mem
* number of memory available
* default 16g

### --assembler
* which method to use
* can be Hybrid with metaspades or long read + polishing with metaflye
* required

### --illumina
* location of the dir containing the forward and reverse illumina reads in fasta or fastq 
* required

### --nanopore
* location of the dir containing the nanopore reads in fasta or fastq
* required

### --output
* output directory
* required

### -profile
* engine and executor
* required
* engine: local_engine, conda, docker, singularity
* executor: local, gcloud, slurm


## Complete help and options
```
    *********hybrid assembly and differential binning workflow for metagenomics, transcriptomics and pathway analysis*********

    MUFFIN is still under development please wait until the first non edge version realease before using it.
    Please cite us using https://www.biorxiv.org/content/10.1101/2020.02.08.939843v1

    Mafin is composed of 3 part the assembly of potential metagenome assembled genomes (MAGs); the classification of the MAGs; and the annotation of the MAGs.

        Usage example:
    nextflow run RVanDamme/MUFFIN --output result --ont nanopore/ --illumina illumina/ --assembler metaspades --rna rna/ -profile local,docker
    or 
    nextflow run RVanDamme/MUFFIN --output result --ont nanopore/ --illumina illumina/ --assembler metaflye -profile local,docker

        Input:
    --ont                       path to the directory containing the nanopore read file (fastq) (default: $params.ont)
    --illumina                  path to the directory containing the illumina read file (fastq) (default: $params.illumina)
    --rna                       path to the directory containing the RNA-seq read file (fastq) (default: none)
    --bin_classify              path to the directory containing the bins files to classify (default: none)
    --bin_annotate              path to the directory containing the bins files to annotate (default: none)
    --assembler                 the assembler to use in the assembly step (default: $params.assembler)

        Optional input:
    --check_db                  path to the checkm database
    --check_tar_db              path to the checkm database tar compressed
    --sourmash_db               path to the LCA database for sourmash (default: GTDB LCA formated)
    --eggnog_db                 path to the eggNOG database

        Output:
    --output                    path to the output directory (default: $params.output)

        Outputed files:
        You can see the output structure at https://osf.io/a6hru/
    QC                          The reads file after qc
    Assembly                    The assembly contigs file 
    Bins                        The bins produced by CONCOCT, MetaBAT2, MaxBin2 and MetaWRAP (the refining of bins)
    Mapped bin reads            The fastq files containing the reads mapped to each metawrap bin
    Unmapped bin reads          The fastq files containing the unmmaped reads of illumina and nanopore
    Reassembly                  The reassembly files of the bins (.fa and .gfa)
    Checkm                      Various file outputed by CheckM (summary, taxonomy, plots and output dir)
    Sourmash                    The classification done by sourmash
    Classify summary            The summary of the classification and quality control of the bins (csv file)
    RNA output                  The de novo assembled transcript and the quantification by Salmon
    Annotation                  The annotations files from eggNOG (tsv format)
    Parsed output               HTML files that summarize the annotations and show graphically the pathways


    

        Basic Parameter:
    --cpus                      max cores for local use [default: $params.cpus]
    --memory                    80% of available RAM in GB for --metamaps [default: $params.memory]


        Workflow Options:
    --skip_ill_qc               skip quality control of illumina files
    --skip_ont_qc               skip quality control of nanopore file
    --short_qc                  minimum size of the reads to be kept (default: $params.short_qc )
    --filtlong                  use filtlong to improve the quality furthermore (default: false)
    --model                     the model medaka will use (default: $params.model)
    --polish_iteration          number of iteration of pilon in the polish step (default: $params.polish_iteration)
    --extra_ill                 a list of additional ill sample file (with full path with a * instead of _R1,2.fastq) to use for the binning in Metabat2 and concoct
    --extra_ont                 a list of additional ont sample file (with full path) to use for the binning in Metabat2 and concoct
    --skip_metabat2             skip the binning using metabat2 (advanced)
    --skip_maxbin2              skip the binning using maxbin2 (advanced)
    --skip_concoct              skip the binning using concoct (advanced)

        Nextflow options:
    -profile                    change the profile of nextflow both the engine and executor more details on github README
    -resume                     resume the workflow where it stopped
    -with-report rep.html       cpu / ram usage (may cause errors)
    -with-dag chart.html        generates a flowchart for the process tree
    -with-timeline time.html    timeline (may cause errors)
```