Genes
=====


Nessecary inputs
----------------

You will need to create a samplesheet with information about the samples
you would like to analyse before running the pipeline. It has to be a
comma-separated file with 6 columns, and a header row as shown in the
examples below.

.. code:: console

   sample,fastq_1,fastq_2,condition,batch,cl
   CONTROL_REP1,AEG588A1_S1_L002_R1_001.fastq.gz,AEG588A1_S1_L002_R2_001.fastq.gz,control,1,0
   CONTROL_REP1,AEG588A1_S1_L003_R1_001.fastq.gz,AEG588A1_S1_L003_R2_001.fastq.gz,control,2,0
   CONTROL_REP1,AEG588A1_S1_L004_R1_001.fastq.gz,AEG588A1_S1_L004_R2_001.fastq.gz,control,3,0

Important
---------

   .. rubric:: Sample names have to be in the first column or in a
      column called sampleID.
      :name: sample-names-have-to-be-in-the-first-column-or-in-a-column-called-sampleid.

   .. rubric:: If you have column names other than **condition** and
      **batch** you need to declare the names in the params_genes.yml.
      See below (Preprocess,DEA,PAE)
      :name: if-you-have-column-names-other-than-condition-and-batch-you-need-to-declare-the-names-in-the-params_genes.yml.-see-below-preprocessdeapea

.. code:: console

   sampleID,condition
   CONTROL_REP1,ctrl
   CONTROL_REP2,ctrl
   TREATMENT_REP1,treat

+---+-------------------------------------------------------------------+
| C | Description                                                       |
| o |                                                                   |
| l |                                                                   |
| u |                                                                   |
| m |                                                                   |
| n |                                                                   |
+===+===================================================================+
|   | Custom sample name. This entry will be identical for multiple     |
|   | sequencing libraries/runs from the same sample. Spaces in sample  |
| s | names are automatically converted to underscores (``_``).         |
| a |                                                                   |
| m |                                                                   |
| p |                                                                   |
| l |                                                                   |
| e |                                                                   |
|   |                                                                   |
|   |                                                                   |
+---+-------------------------------------------------------------------+
|   | Full path to FastQ file for Illumina short reads 1. File has to   |
|   | be gzipped and have the extension “.fastq.gz” or “.fq.gz”.        |
| f |                                                                   |
| a |                                                                   |
| s |                                                                   |
| t |                                                                   |
| q |                                                                   |
| _ |                                                                   |
| 1 |                                                                   |
|   |                                                                   |
|   |                                                                   |
+---+-------------------------------------------------------------------+
|   | Full path to FastQ file for Illumina short reads 2. File has to   |
|   | be gzipped and have the extension “.fastq.gz” or “.fq.gz”.        |
| f |                                                                   |
| a |                                                                   |
| s |                                                                   |
| t |                                                                   |
| q |                                                                   |
| _ |                                                                   |
| 2 |                                                                   |
|   |                                                                   |
|   |                                                                   |
+---+-------------------------------------------------------------------+
|   | Custom sample name. This entry will be identical for multiple     |
|   | sequencing libraries/runs from the same sample. Spaces in sample  |
| s | names are automatically converted to underscores (``_``).         |
| t |                                                                   |
| r |                                                                   |
| a |                                                                   |
| n |                                                                   |
| d |                                                                   |
| n |                                                                   |
| e |                                                                   |
| s |                                                                   |
| s |                                                                   |
|   |                                                                   |
|   |                                                                   |
+---+-------------------------------------------------------------------+
|   | Metadata describing your test condition (or treatment, or state   |
|   | etc)                                                              |
| c |                                                                   |
| o |                                                                   |
| n |                                                                   |
| d |                                                                   |
| i |                                                                   |
| t |                                                                   |
| i |                                                                   |
| o |                                                                   |
| n |                                                                   |
|   |                                                                   |
|   |                                                                   |
+---+-------------------------------------------------------------------+
|   | Describes unwanted source of variation (e.g. technical            |
|   | replicates, different platfroms, different batches etc.).         |
| b |                                                                   |
| a |                                                                   |
| t |                                                                   |
| c |                                                                   |
| h |                                                                   |
|   |                                                                   |
|   |                                                                   |
+---+-------------------------------------------------------------------+

Start from SRA
--------------

Alternatively, instead of providing with paths of fastq files you
can provide a column with SRA **run** identifiers you wish to download
from NCBI in the first column named sample and the second column has to
be named sampleID and store the SRA **codes**:

.. code:: console

   sample,sampleID,condition,batch,cl
   SRR2015757,SRX1022901,control,1,1
   SRR2015760,SRX1022904,control,2,1
   SRR2015761,SRX1022905,control,3,1

An `example samplesheet <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/assets/samplesheet_genes.csv>`__ has been
provided with the pipeline.

Refference files
----------------

The user can choose to run the pipeline using fasta and gtf files
supplied by aws:

.. code:: bash

   params{
     igenomes_base              : 's3://ngi-igenomes/igenomes'
     igenomes_ignore            : false 
   }

Alternatively, they can provide the location of local refference files:

.. code:: bash

   params{
     fasta_genes                   : '/home/bianca/gencode2/GRCh38.primary_assembly.genome.fa.gz'
     transcript_fasta_genes         : '/home/bianca/gencode2/gencode.v44.transcripts.fa.gz'
     gtf_genes                       : '/home/bianca/gencode2/gencode.v44.annotation.gtf.gz'

Running the pipeline
--------------------

In order to run the genes part of the pipeline you have to modify one
file, specifying which part of the analysis you want to run and specific
parameters `params_genes.yml <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/params_genes.yml>`__:

.. code:: bash

   params{
   genome = 'GRCh38'  # Reference genome identifier from AWS, check /conf/igenomes.config
   outdir  = 'full path of location you want to output'
   salmonDiGenes = '/path/to/outdir/salmon/genes'
   input_genes = 'full path of samplesheet with SRA code or location of fastq files'
   }

In case you started from SRA codes you also need to declare it in the
params_genes.yml

.. code:: console

   params{
     sra_genes = true
   }

The general command to run the pipeline is:

.. code:: bash

   nextflow run multiomicsintegrator -params-file multiomicsintegrator/params_genes.yml -profile docker 

This will launch the pipeline with the ``docker`` configuration profile.
See below for more information about profiles.

Note that the pipeline will create the following files in your working
directory:

.. code:: bash

   work                'Directory containing the nextflow working files'
   <OUTDIR>            ' Location of where you want your results (defined by outdir)' 
   .nextflow_log       # Log file from Nextflow
   # Other nextflow hidden files, eg. history of pipeline runs and old logs.

Functionality
~~~~~~~~~~~~~

The pipeline initially downloads SRA codes and converts the runs into
fastq files. Alternativey you can provide local fastq files. It then
performs quality control with FASTQC and
then automatically detects and removes adapters with
Trimgalore.

Each of the above steps can be skipped, for example if you don’t want to
perform quality control, you can specify in the params_genes.yml file:

.. code:: bash

   params{
     skip_qc_genes= true
   }

It then employs salmon in order to
obtain quantification files that are outputed in
::

/OUTDIR/salmon_genes/sampleID/quant.sf 

directory.

If you have performed the alignment step outside you can organise your
data in the aforementioned way and specify the directory that holds the
quant.sf files in the params_genes.yml:

.. code:: bash

   params{
     salmonDirGenes = '/path/to/directory_that_holds_quantification_files'
   }

Note: All files need to be in the format:
-----------------------------------------

.. code:: plaintext

   salmonDirGenes:
   - sampleID/  
     - quant.sf

The sampleID is the same of the sampleID of the phenotype file (or the
sample names of the samplesheet.csv)

If you want to skip the alignment step you need to specify the location
of the count matrix and the respective phenotype (samplesheet_genes.csv)
you have, in the params_genes.yml file:

.. code:: bash

   params{
     count_matrix_genes = 'path where count matrix is located'
     input_genes = 'path where your phenotype file is located'
   }

Preprocess
----------

After the formation of the count matrix there is an optional module 
preprocess_matrix that performs preprocessing steps on the count matrix. 
Namely, the user can perform filtering, normalization and batch effect correction, 
depending on the state of their data.

   .. rubric:: Input_genes should have a column named condition
      describing the states of the experiment (ctr vs treat) and one
      called “batch” describing batches of the experiment (if there is
      no batch then the replicate column is the batch). If the user
      wants other names they have to specify in the
      params_genes.yml the column name of their conditions and that
      column name to be present in the samplesheet_genes.csv file:
      :name: input_genes-should-have-a-column-named-condition-describing-the-states-of-the-experiment-ctr-vs-treat-and-one-called-batch-describing-batches-of-the-experiment-if-there-is-no-batch-then-the-replicate-column-is-the-batch.-if-the-user-wants-other-names-they-have-to-specify-in-the-params_genes.yml-the-column-name-of-their-conditions-and-that-column-name-to-be-present-in-the-samplesheet_genes.csv-file

.. code:: bash


   params{
       mom_filt_method_genes           = "filterByExp"  # filterByExp or choose a cutoff value
       mom_norm_method_genes           = "quantile"     # calcNorm quantile
       mom_norm_condition_genes           = "condition"   # must be column in samples info 
       mom_norm_treatment_genes           = "condition"   # must be column in samples info 
       mom_batch_method_genes          = "com" # com for combat, sva,  comsva for combat & sva, svacom for sva and comba, none
       mom_batch_condition_genes       = "condition"    # which is the condition of interest, must be present in columns of sample info
       mom_batch_batch_genes           = "batch"  
   }

DEA
---

Next step is differential expression analysis. We provide
three different algorithms for that, which we describe below.

Note
~~~~

   You need to specify which algorithm you are going to use in
   params_genes.yml

.. code:: bash

   params{
     alg_genes     = 'edger' # Default
   }

edger
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: bash

   params{
       dgergroupingfactor_genes        =  "condition" # column name where your treatments are located
       edgerformulamodelmatrix_genes   =  "~0 + condition" # design matrix, values have to be column names in samplesheet_genes.csv
       edgercontrasts_genes            = "TNBC-non_TNBC"  # contrasts of interest. Values have to be present in the samplesheet_genes.csv
   }

DESeq2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Important note**
~~~~~~~~~~~~~~~~~~

   For DESeq2 to run, the column of the treatments in
   the samplesheet_genes.csv has to be named **condition** and the
   batches **batch**

.. code:: bash

   params{
       batchdeseq2_genes               = false # perform batch effect correction
       deseqFormula_genes              = "~0 + condition"  # design matrix, values have to be column names in samlesInfo_genes.txt
       con1_genes                     = "mkc"   # control, has to be cell in samplesinfo
       con2_genes                     = "dmso"  # treatment, has to be cell in samplesinfo
       deseq2single_matrix             = true   # if the input is a single matrix or a directory of files
   }

RankProduct rankprod
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inputs for to run RankProduct are the same, with a single difference:
The **condition column** has to be named **cl** and the user has to
asign **0 to controls and 1 to treatments**

.. code:: console

   sampleID,cl
   CONTROL_REP1,1
   CONTROL_REP2,1
   TREATMENT_REP1,0

or if you started from SRA codes

.. code:: console

   sampleID,cl
   SRX1022911,0
   SRX1022917,0
   SRX1022920,1

PEA
---

Last step of the analysis is to perform pathway enrichment analysis with
clusterprofiler or biotranslator.


.. code:: bash

   params{
       features                         = null # if you want to perform clusterprofiler as a standalone tool, specify directory of features here
       alg                        = "edger" # algoritmh you used to perform differential expression analysis or mcia
       genes_genespval                  = 1 # pval cutoff for genes
       mirna_genespval                  = 1 # pval cutoff for miRNA
       proteins_genespval               = 0.5 # pval cutoff for proteins
       lipids_genespval                 = 0.5 # pval cutoff for lipids
   }

bash

::

   params{
       // BIOTRANSLATOR
       pea_genes      = "biotranslator"
       biotrans_genes_organism          = "hsapiens"
       biotrans_genes_keytype          = "gene_symbol"
       biotrans_genes_ontology         = "GO" // MGIMP, Reactome
   }


