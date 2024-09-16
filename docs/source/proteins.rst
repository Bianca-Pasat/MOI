Proteins
========


Nessecary inputs
----------------

The pipeline has as input the `count
matrix <../assets/counts_proteins.txt>`__ with the abundance of proteins
and a `phenotype file <../assets/samplesheet_proteins.csv>`__ describing
the metadata of each sample. You have to modify one file, specifying
which part of the analysis you want to run and specific parameters
`params_proteins.yml <../params_proteins.yml>`__:

.. code:: bash


   params{
     outdir  '[full path of location you want to output]'
     count_matrix_proteins  '[full path of location your proteins count matrix]'
     input_proteins   '[full path of location your proteins phenotype file]'
   }

   Change the location of the files appropriately

Important
----------

   .. rubric:: Sample names have to be in the first column or in a
      column called sampleID and **need to match** the column names of
      your count matrix.
      :name: sample-names-have-to-be-in-the-first-column-or-in-a-column-called-sampleid-and-need-to-match-the-column-names-of-your-count-matrix.

   .. rubric:: If you have column names other than **condition** and
      **batch** you need to change declare the names in the
      params_proteins.yml. See below
      (preprocess_matrix.,dea,clusterprofiler)
      :name: if-you-have-column-names-other-than-condition-and-batch-you-need-to-change-declare-the-names-in-the-params_proteins.yml.-see-below-preprocess_matrix.deaclusterprofiler

Running the pipeline
--------------------

The general command to run the pipeline is:

.. code:: bash

   nextflow run multiomicsintegrator -params-file multiomicsintegrator/params_proteins.yml -profile docker 

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

Initially, there is an optional module
`preprocess_matrix <../subworkflows/local/preprocess_matrix.nf>`__ that
preprocesses this matrix. Namely, the user can perform
`filtering <../modules/local/mom_filter>`__,
`normalization <../modules/local/mom_norm/>`__ and `batch
effect <../modules/local/mom_filter/>`__ correction, depending on the
state of their data.

   .. rubric:: Input_proteins should have a column named “condition”
      describing the states of the experiment (ctr vs treat) and one
      called “batch” describing batches of the experiment (if there is
      no batch then the replicate column is the batch). If the user
      wants other names they user have to specify in the
      params_proteins.yml the column name of their conditions and that
      column name to be present in the input_proteins.csv file:
      :name: input_proteins-should-have-a-column-named-condition-describing-the-states-of-the-experiment-ctr-vs-treat-and-one-called-batch-describing-batches-of-the-experiment-if-there-is-no-batch-then-the-replicate-column-is-the-batch.-if-the-user-wants-other-names-they-user-have-to-specify-in-the-params_proteins.yml-the-column-name-of-their-conditions-and-that-column-name-to-be-present-in-the-input_proteins.csv-file

.. code:: bash


   params{

       mom_norm_condition_proteins           = "condition"   # must be columns in samples info 
       mom_norm_treatment_proteins           = "condition"   # must be columns in samples info 
       mom_batch_condition_proteins       = "condition"    # which is the condition of interest, must be present in columns of sample info
       mom_batch_batch_proteins           = "batch"  
   }

Once the count matrix is ready, we can move on to differential
expression analysis. We provide three different algorithms for that:

Preprocess
~~~~~~~~~~

After the formation of the count matrix there is an optional module
`preprocess_matrix <../subworkflows/local/preprocess_matrix.nf>`__\ that
preprocesses this matrix. Namely, the user can perform
`filtering <../modules/local/mom_filter>`__,
`normalization <../modules/local/mom_norm/>`__ and `batch
effect <../modules/local/mom_filter/>`__ correction, depending on the
state of their data.

.. code:: bash


   params{
       mom_filt_method_proteins           = "filterByExp"  # filterByExp or choose a cutoff value
       mom_norm_method_proteins           = "quantile"     # calncNorm quantile
       mom_batch_method_proteins          = "com" # com for combat, sva,  comsva for combat & sva, svacom for sva and comba, none
       mom_batch_batch_proteins           = "replicate"  
   }

Now,is time to perform differential expression analysis. We provide
three different algorithms for that, which we describe below.

Note
~~~~

   You need to specify which algorithm you are going to use in
   params_proteins.yml

.. code:: bash

   params{
     alg_proteins     = 'edger' # Default
   }

DEA
~~~

edger `edger <../modules/local/edger>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: bash

   params{
       dgergroupingfactor_proteins        =  "condition" # column name where your treatments are located
       edgerformulamodelmatrix_proteins   =  "~0 + condition" # design matrix, values have to be column names in deseq2 samplesheet_proteins.csv
       edgercontrasts_proteins            = "TNBC-non_TNBC"  # contrasts of interest. Values have to be present in the samplesheet_proteins.csv
   }

DESeq2 `deseq <../modules/local/deseq2>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Important note**
~~~~~~~~~~~~~~~~~~

   For DESeq2 to run you need to have the column of the treatments in
   the samplesheet_proteins.csv has to be named **condition** and the
   batches **batch**

.. code:: bash

   params{
       batchdeseq2_proteins               = false # perform batch effect correction
       deseqFormula_proteins              = "~0 + condition"  # design matrix, values have to be column names in deseq2 samplesheet_proteins.csv
       con1_proteins                     = "mkc"   # control, has to be cell in samplesinfo
       con2_proteins                     = "dmso"  # treatment, has to be cell in samplesinfo
       deseq2single_matrix             = true   # if the input is a single matrix or a directory of files
   }

RankProduct `rankprod <../modules/local/rankprod>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Inputs for to run RankProduct are the same, with a single difference:
The **condition column** has to be named **cl** and the user has to
asign **0 to controls and 1 to treatments**

.. code:: console

   sampleID,cl
   CONTROL_REP1,1
   CONTROL_REP2,1
   TREATMENT_REP1,0

Pathway Enrichment Analysis (PEA)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Last step of the analysis is to perform pathway enrichment analysis with
`clusterprofiler <../modules/local/clusterprofiler>`__ or
`biotranslator <../modules/local/biotranslator>`__

.. code:: bash

   params{
       features                         = null # if you want to perform clusterprofiler as a standalone tool, specify directory of features here
       alg                        = "edger" # algoritmh you used to perform differential expression analysis or mcia
       proteins_genespval                  = 1 # pval cutoff for genes
       mirna_genespval                  = 1 # pval cutoff for miRNA
       proteins_genespval               = 0.5 # pval cutoff for proteins
       lipids_genespval                 = 0.5 # pval cutoff for lipids
   }

BIOTRANSLATOR
^^^^^^^^^^^^^

.. code:: bash

   params{
       
       pea_proteins      = "biotranslator"
       biotrans_pro_organism          = "hsapiens"
       biotrans_pro_keytype          = "gene_symbol"
       biotrans_pro_ontology         = "GO" # MGIMP, Reactome

   }

