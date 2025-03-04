# <code>exome-seek <b>run</b></code>

## 1. About 
The `exome-seek` executable is composed of several inter-related sub commands. Please see `exome-seek -h` for all available options.

This part of the documentation describes options and concepts for <code>exome-seek <b>run</b></code> sub command in more detail. With minimal configuration, the **`run`** sub command enables you to start running exome-seek pipeline. 

Setting up the exome-seek pipeline is fast and easy! In its most basic form, <code>exome-seek <b>run</b></code> only has *four required inputs*.

## 2. Synopsis
```text
$ ./exome-seek run [--help] \
                   [--mode {local, slurm}] \
                   [--job-name JOB_NAME] \
                   [--callers {mutect2,mutect,strelka, ...}] \
                   [--pairs PAIRS] \
                   [--ffpe] \
                   [--cnv] \
                   [--silent] \
                   [--singularity-cache SINGULARITY_CACHE] \
                   [--sif-cache SIF_CACHE] \
                   [--threads THREADS] \
                   --runmode {init, dryrun, run} \
                   --input INPUT [INPUT ...] \
                   --output OUTPUT \
                   --genome {hg38, ...} \
                   --targets TARGETS 
```

The synopsis for each command shows its parameters and their usage. Optional parameters are shown in square brackets.

A user **must** provide a list of FastQ or BAM files (globbing is supported) to analyze via `--input` argument, an output directory to store results via `--output` argument, an exome targets BED file for the samples' capture kit, and select reference genome for alignment and annotation via the `--genome` argument. 

Use you can always use the `-h` option for information on a specific command. 

### 2.1 Required Arguments

Each of the following arguments are required. Failure to provide a required argument will result in a non-zero exit-code.

  `--input INPUT [INPUT ...]`  
> **Input FastQ or BAM file(s) to process.**  
> *type: file(s)*  
> 
> One or more FastQ files can be provided. The pipeline does NOT support single-end WES data. Please provide either a set of FastQ files or a set of BAM files. The pipeline does NOT support processing a mixture of FastQ files and BAM files. From the command-line, each input file should seperated by a space. Globbing is supported! This makes selecting FastQ files easy. Input FastQ files should be gzipp-ed.
> 
> ***Example:*** `--input .tests/*.R?.fastq.gz`

---  
  `--output OUTPUT`
> **Path to an output directory.**   
> *type: path*
>   
> This location is where the pipeline will create all of its output files, also known as the pipeline's working directory. If the provided output directory does not exist, it will be initialized automatically.
> 
> ***Example:*** `--output /data/$USER/WES_hg38`

---  
  `--runmode  {init,dryrun,run}`  `
> **Execution Process.**  
> *type: string*
>   
> User should initialize the pipeline folder by first running `--runmode init`  
> User should then perform a dry-run to list all steps the pipeline will take`--runmode dryrun`  
> User should then perform the full run `--runmode run`  
> 
> ***Example:*** `--runmode init` *THEN* `--runmode dryrun` *THEN* `--runmode run`

---  
  `--genome {hg38, custom.json}`
> **Reference genome.**   
> *type: string/file*
>   
> This option defines the reference genome for your set of samples. On Biowulf, exome-seek does comes bundled with pre built reference files for human samples; however, it is worth noting that the pipeline does accept a pre-built resource bundle pulled with the cache sub command (coming soon). Currently, the pipeline only supports the human reference hg38; however, support for mouse reference mm10 will be added soon.
>
> ***Pre built Option***  
> Here is a list of available pre built genomes on Biowulf: hg38.
>
> ***Custom Option***   
> For users running the pipeline outside of Biowulf, a pre-built resource bundle can be pulled with the cache sub command (coming soon). Please supply the custom reference JSON file that was generated by the cache sub command.
>   
> ***Example:*** `--genome hg38` *OR* `--genome /data/${USER}/hg38/hg38.json`  

---  
  `--targets TARGETS`
> **Exome targets BED file.**   
> *type: file*
>   
> This file can be obtained from the manufacturer of the target capture kit that was used.
> 
> ***Example:*** `--targets /data/$USER/Agilent_SSv7_allExons_hg38.bed`

### 2.2 Options

Each of the following arguments are optional and do not need to be provided. 

  `-h, --help`            
> **Display Help.**  
> *type: boolean flag*
> 
> Shows command's synopsis, help message, and an example command
> 
> ***Example:*** `--help`

---  
  `--silent`            
> **Silence standard output.**  
> *type: boolean flag*
> 
> Reduces the amount of information directed to standard output when submitting master job to the job scheduler. Only the job id of the master job is returned.
>
> ***Example:*** `--silent`

---  
  `--mode {local,slurm}`  
> **Execution Method.**  
> *type: string*  
> *default: slurm*
> 
> Execution Method. Defines the mode or method of execution. Vaild mode options include: local or slurm. 
> 
> ***local***  
> Local executions will run serially on compute instance. This is useful for testing, debugging, or when a users does not have access to a high performance computing environment. If this option is not provided, it will default to a local execution mode. 
> 
> ***slurm***    
> The slurm execution method will submit jobs to a cluster using a singularity backend. It is recommended running exome-seek in this mode as execution will be significantly faster in a distributed environment.
> 
> ***Example:*** `--mode slurm`

---  
  `--job-name JOB_NAME`  
> **Set the name of the pipeline's master job.**  
> *type: string*
> *default: pl:exome-seek*
> 
> When submitting the pipeline to a job scheduler, like SLURM, this option always you to set the name of the pipeline's master job. By default, the name of the pipeline's master job is set to "pl:exome-seek".
> 
> ***Example:*** `--job-name wes_id-42`

---  
  `--callers CALLERS [CALLERS ...]`  
> **Variant Callers.**  
> *type: string(s)*
> *default: mutect2, mutect, strelka, vardict, varscan*
> 
> List of variant callers to detect mutations. Please select from one or more of the following options: [mutect2, mutect, strelka, vardict, varscan]. Defaults to using all variant callers.
> 
> ***Example:*** `--callers mutect2 strelka varscan`

---  
  `--pairs PAIRS`  
> **Tumor normal pairs file.**  
> *type: file*
> 
> This tab delimited file contains two columns with the names of tumor and normal pairs, one per line. The header of the file needs to be `Tumor` for the tumor column and `Normal` for the normal column. The base name of each sample should be listed in the pairs file. The base name of a given sample can be determined by removing the following extension from the sample's R1 FastQ file: `.R1.fastq.gz`.   
>  **Contents of example pairs file:**
> ```
> Normal	Tumor
> Sample4_CRL1622_S31	Sample10_ARK1_S37
> Sample4_CRL1622_S31	Sample11_ACI_158_S38
> ``` 
> ***Example:*** `--pairs /data/$USER/pairs.tsv`

---  
  `--ffpe`  
> **Apply FFPE correction.**  
> *type: boolean flag*
> 
> Runs an additional steps to correct strand orientation bias in Formalin-Fixed Paraffin-Embedded (FFPE) samples. Do NOT use this option with non-FFPE samples.
> 
> ***Example:*** `--ffpe`

---  
  `--cnv`  
> **Call copy number variations (CNVs).**  
> *type: boolean flag*
> 
> CNVs will only be called from tumor-normal pairs. If this option is provided without providing a --pairs file, CNVs will NOT be called.
> 
> ***Example:*** `--cnv`

---  
  `--singularity-cache SINGULARITY_CACHE`  
> **Overrides the $SINGULARITY_CACHEDIR environment variable.**  
> *type: path*  
> *default: `--output OUTPUT/.singularity`*
>
> Singularity will cache image layers pulled from remote registries. This ultimately speeds up the process of pull an image from DockerHub if an image layer already exists in the singularity cache directory. By default, the cache is set to the value provided to the `--output` argument. Please note that this cache cannot be shared across users. Singularity strictly enforces you own the cache directory and will return a non-zero exit code if you do not own the cache directory! See the `--sif-cache` option to create a shareable resource. 
> 
> ***Example:*** `--singularity-cache /data/$USER/.singularity`

---  
  `--sif-cache SIF_CACHE`
> **Path where a local cache of SIFs are stored.**  
> *type: path*  
>
> Uses a local cache of SIFs on the filesystem. This SIF cache can be shared across users if permissions are set correctly. If a SIF does not exist in the SIF cache, the image will be pulled from Dockerhub and a warning message will be displayed. The `exome-seek cache` subcommand can be used to create a local SIF cache. Please see `exome-seek cache` for more information. This command is extremely useful for avoiding DockerHub pull rate limits. It also remove any potential errors that could occur due to network issues or DockerHub being temporarily unavailable. We recommend running exome-seek with this option when ever possible.
> 
> ***Example:*** `--singularity-cache /data/$USER/SIFs`

---  
  `--threads THREADS`   
> **Max number of threads for each process.**  
> *type: int*  
> *default: 2*
> 
> Max number of threads for each process. This option is more applicable when running the pipeline with `--mode local`.  It is recommended setting this vaule to the maximum number of CPUs available on the host machine.
> 
> ***Example:*** `--threads 12`

## 3. Example
```bash 
# Step 1.) Grab an interactive node
# Do not run on head node!
sinteractive --mem=8g --cpus-per-task=4
module purge
module load singularity snakemake/6.8.2

# Step 2A.) Initialize the all resources to the output folder 
./exome-seek run --input .tests/*.R?.fastq.gz \
                 --output /data/$USER/WES_hg38 \
                 --genome hg38 \
                 --targets Agilent_SSv7_allExons_hg38.bed \
                 --mode slurm \
                 --runmode init

# Step 2B.) Dry-run the pipeline
./exome-seek run --input .tests/*.R?.fastq.gz \
                 --output /data/$USER/WES_hg38 \
                 --genome hg38 \
                 --targets Agilent_SSv7_allExons_hg38.bed \
                 --mode slurm \
                 --runmode dryrun

# Step 2C.) Run the GATK4 WES pipeline
# The slurm mode will submit jobs to the cluster.
# It is recommended running exome-seek in this mode.
./exome-seek run --input .tests/*.R?.fastq.gz \
                 --output /data/$USER/WES_hg38 \
                 --genome hg38 \
                 --targets Agilent_SSv7_allExons_hg38.bed \
                 --mode slurm \
                 --runmode run

```