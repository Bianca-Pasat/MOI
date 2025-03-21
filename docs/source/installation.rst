Installation
==========================



**multiOmicsIntegrator** is a pipeline for analysis of multi-Omics data.

The pipeline is built using [Nextflow](https://www.nextflow.io) version 23.04.2.5870 (IMPORTANT), a workflow tool to run tasks across multiple compute infrastructures in a very portable manner. It uses Docker/Singularity containers making installation straightforward and results highly reproducible. The [Nextflow DSL2](https://www.nextflow.io/docs/latest/dsl2.html) implementation of this pipeline uses one container per process which makes it much easier to maintain and update software dependencies. Where possible, these processes have been submitted to and installed from [nf-core/modules](https://github.com/nf-core/modules) in order to make them available to all nf-core pipelines, and to everyone within the Nextflow community!


Pipeline summary
-------------------


   - RNAseq analysis at the level of 
      - mRNAs 
      - miRNAs
      - isoforms 
   - Functional annotation of transcripts
   - Metabolomics analysis
   - Proteomics analysis
   - Integration of multi omics data
   ----------

The pipeline can be found at:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses

Supplementary materials for this pipeline can be found at this zenodo repository:
^^^^^^^^^^^^^^^^^^^^^^^

https://zenodo.org/records/10813721



Quick Start
-----------

1. Install [`Nextflow`](https://www.nextflow.io/docs/latest/getstarted.html#installation) (`>=22.10.1`)

2. Install [`Docker`](https://docs.docker.com/engine/installation/).

3. Download the pipeline and rename it:

 .. code:: bash

   git clone https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses.git
   mv MOI--An-integrated-solution-for-omics-analyses multiomicsintegrator 
 

4. Modify in the params_mcia.yml file the following parameters regarding the location you want your outputs

- outdir: yourDir
- pathmcia: /path/to/yourDir/**mcia**
- biotrans_all_path :  /path/to/yourDir/**prepareforbio**

   Paths of pathmcia and biotrans_all_path should be complete and follow this format: 

  .. code:: bash

      $outdir/mcia
      $outdir/prepareforbio
 

   See format in *params_mcia.yml* and change accordingly.

In addition check/modify resources (in params_mcia.yml) according to your system:
^^^^^^^^^^^^^^^^^^^^^^^

   - max_memory : '8.GB'
   - max_cpus   : 7


5. Run the pipeline by providing the full path to params-file argument 

 .. code:: bash

   NXF_VER=23.04.2 nextflow run multiomicsintegrator -params-file /full/path/to/params_mcia.yml -profile docker 
 

   Note that some form of configuration will be needed so that Nextflow knows how to fetch the required software. This is usually done in the form of a config profile (`YOURPROFILE` in the example command above). You can chain multiple config profiles in a comma-separated string.



6. Start running your own analysis!

   The above example refers to a simplified version of an integrated analysis. Depending on which part of the pipeline you want to run and your starting point (raw or matrices) modify the respective parameter file:

   - params_isoforms.yml
   - params_genes.yml
   - params_mirna.yml
   - params_proteins.yml
   - params_lipids.yml
   - params_mcia
   - params_ridderalone


Common issues:
-----------------
   - If an error regarding biomaRt appears:
     
  .. code:: bash

     Error in h(simpleError(msg, call)) : 
     error in evaluating the argument 'conn' in selecting a method for function 'dbDisconnect': object 'info' not found
     Calls: useEnsembl ... .sql_disconnect -> dbDisconnect -> .handleSimpleError -> h
     Execution halted
  
   or 
   
 
     Ensembl site unresponsive, trying useast mirror
  Ensembl site unresponsive, trying asia mirror
  Error in .chooseEnsemblMirror(mirror = mirror, http_config = http_config) : 
    Unable to query any Ensembl site
  Calls: useEnsembl -> .chooseEnsemblMirror
  Execution halted
 

just run the pipeline again with -resume :


 .. code:: bash

   nextflow run multiomicsintegrator -params-file /full/path/to/params_mcia.yml -profile docker -resume
 
   - If the error persists try delete container of bianca7/mompreprocess (or all containers if possible) and run again 
   - Comparative analysis, isoform analysis and mcia need substantial resources (at least 7 cpus).
   - Check resources and your directories!



Documentation
-------------

The ASAGlab/moi pipeline comes with documentation about the pipeline under docs in various usage.md files as well as example yml files which the user can modify as guidance into custom modifications directly. Example outputs are also included under the docs folder in this repository `ASAG/MOI <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses>`_.
