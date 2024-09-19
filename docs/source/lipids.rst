Lipids
======


Nessecary inputs
----------------

The pipeline has as input the `count matrix <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/assets/lipids.txt>`__
with the abundance of lipids and a `phenotype
file <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/assets/samplesheet_lipids.csv>`__ describing the metadata of each
sample.

.. code:: bash


   params{
     count_matrix_lipids = ' path where count matrix is located'
     input_lipids = 'path where your phenotype file is located'
   }

Important
---------

   .. rubric:: Sample names have to be in the first column or in a
      column called sampleID and **need to match** the column names of
      your count matrix.
      :name: sample-names-have-to-be-in-the-first-column-or-in-a-column-called-sampleid-and-need-to-match-the-column-names-of-your-count-matrix.

   .. rubric:: If you have column names other than **condition** and
      **batch** you need to change declare the names in the
      params_lipids.yml.
      :name: if-you-have-column-names-other-than-condition-and-batch-you-need-to-change-declare-the-names-in-the-params_lipids.yml.

Running the pipeline
--------------------

In order to run the lipids part of the pipeline you have to modify one
file, specifying which part of the analysis you want to run and
respective parameters `params_lipids.yml <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/params_lipids.yml>`__:

.. code:: bash

   params{
     outdir  '[full path of location you want to output]'
     count_matrix_lipids  '[full path of location your lipids count matrix]'
     input_lipids   '[full path of location your lipids phenotype file]'
   }

The general command to run the pipeline is:

.. code:: bash

   nextflow run multiomicsintegrator -params-file multiomicsintegrator/params_lipids.yaml -profile docker 

   Change the location of the files appropriately

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
==============

All in one analysis with LipidR
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We provide the possibility to perform the proprocessing steps, as well
as the the differential expression analysis using the R Bioconductor
package `lipidr <../modules/local/lipidr>`__ . Lipidr provides
additional exploratory plots regarding the different classes of lipids
as well as if there is any enrichment of these classes between
conditions. Moreover, it provides with information abou the saturation
level of the carbon chains of the different classes of lipids between
conditions.

   .. rubric:: You need to have either the first or one column named
      **sampleID** and the column that stores the different settings of
      your experiment has be named **condition** in your
      samplesheet_lipids.csv file.
      :name: you-need-to-have-either-the-first-or-one-column-named-sampleid-and-the-column-that-stores-the-different-settings-of-your-experiment-has-be-named-condition-in-your-samplesheet_lipids.csv-file.

LipiDB
^^^^^^

LipidR will produce differentially expressed features for each category
of lipids. Subsequently, LipiDB, using KREGGREST will find genes
associated to these differentially expressed lipids, for each category.
Input is the result of lipidR or in other words a txt file that has eregulated lipids along with their logFC and pval. The results are in as form of a text file and a heatmap.

PEA
^^^^

Last step of the analysis is to perform pathway enrichment analysis with
metabAnalystR or with biotranslator

BIOTRANSLATOR
~~~~~~~~~~~~~

.. code:: bash

   params{
       
       pea_proteins      = "biotranslator"
       biotrans_pro_organism          = "hsapiens"
       biotrans_pro_keytype          = "gene_symbol"
       biotrans_pro_ontology         = "GO" // MGIMP, Reactome

   }

