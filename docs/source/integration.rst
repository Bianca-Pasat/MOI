Integration
================================

Nessecary inputs
----------------

The pipeline gives the possibility to integrate simultaneously different
types of omics data.

The method we currently provide is MCIA (Multiple Co-Inertia Analysis).

You can either run MCIA with your own data or choose this module as an
additional step on your analyses. Check
`params_mcia.yml <../params_mcia.yml>`__.

You need to provide the path in (pathmcia variable) in which you store
the different omics data and a samples info file describing the metadata
of each sample.

   The path you provide should consist of sub-directories named after
   the omics type you have. For example if I have proteins and mirnas my
   mcia directory will look like:

.. code:: plaintext

   - mcia
       -genes
         - genes.txt
       - proteins
         - proteins.txt
       - lipids
         - lipids.txt
       

Alternatively, if you mcia is used as with pipeline-generated data you
should declare these variables as follows:

.. code:: bash

   params{
     outdir: '/home/bianca/testresultsintegration' ### If outdir =  results then pathmcia should: /complete/path/to/results/mcia/ biotransl_all_path:path/to/results/prepareforbio
     pathmcia  = '/$outdir/mcia/'  # should of the string of the format /complete/path/to/results/mcia/ 
     biotrans_all_path  : "/home/bianca/testresultsintegration/prepareforbio/"  # should of the string of the format /complete/path/to/results/prepareforbio/ 
     }

Additionally, you need to change parameters in
`params_mcia.yml <../params_mcia.yml>`__ appropriately:

.. code:: bash


   params{
     runmcia = true
     outdir =  '[full path of location you want to output]'
     pathmcia = 'directory that store the different omics data'
     samplesinfomcia = 'path of you samples info file'
     a1lim = limits of your X space, Default :  '0, Inf'
     a2lim = limits of your Y space, Default :  '0, Inf'
   }

..

   Change the location of the files appropriately

Important
---------

   .. rubric:: Sample names have to be in the first column or in a
      column called sampleID and **need to match** the column names of
      your count matrix.
      :name: sample-names-have-to-be-in-the-first-column-or-in-a-column-called-sampleid-and-need-to-match-the-column-names-of-your-count-matrix.

   .. rubric:: If you have column names other than **condition** you
      need to change declare the names in the params_mcia.yml.
      :name: if-you-have-column-names-other-than-condition-you-need-to-change-declare-the-names-in-the-params_mcia.yml.

Running the pipeline
--------------------

The general command to run the pipeline is:

.. code:: bash

   nextflow run multiomicsintegrator -params-file multiomicsintegrator/params_mcia.yml -profile docker 

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

MCIA utilizes mathematical techniques such as covariance analysis,
optimization, and dimensionality reduction to integrate diverse omics
datasets. It starts by centering and scaling data, then identifies
shared patterns through joint covariance analysis. Through optimization,
it determines coefficients for each feature, quantifying their
contribution to shared structures. The method constructs latent
variables or components, representing these shared patterns. If needed,
dimensionality reduction is applied for a more concise interpretation.
This comprehensive mathematical approach allows MCIA to effectively
capture commonalities and distinctions in multi-omics data, offering
insights into complex biological relationships.

Additionally, we offer the possibility of funtional integration of data
to cover scenarios in which MCIA cannot be applied. Namely, we offer 


LipiDB
~~~~~~~~~~~~~~~~~~~

LipidR will produce differentially expressed features for each category
of lipids. Subsequently, LipiDB, using KREGGREST will find genes
associated to these differentially expressed lipids, for each category.
Input is the result of lipidR or in other words a txt file that has eregulated lipids along with their logFC and pval. The results are in as form of a text file and a heatmap.

multiMiR
~~~~~~~~~

MultiMiR is a database that stores predicted and experimentaly targets of miRNA. 
As input it takes a txt file containing differentially expressed miRNAs.
The output consists of two files, one containing only the targets and one storing
the miRNA with their targets. 


Exploratory analysis
~~~~~~~~~~~~~~~~~~~~
The pipeline produces automatically a heatmap with differentially expressed
features and their presence accross available omics layers. As input it takes
differentially expressed features and optionally the results from multiMiR and 
LipiDB. 

Correlation analysis
~~~~~~~~~~~~~~~~~~~~~

to estimate correlation between differentially expressed features. 
We suggest to use the count matrices of the differentially expressed features.

.. code:: bash

   params{
       correlation_alone          = false
       cor_m1                     = "${projectDir}/results//mirna/rankprod/mirna_defeatures_expression.txt"
       cor_m2                     = "${projectDir}/results/genes/rankprod/genes_defeatures_expression.txt"
       cor_method                 = "pearson" // method of correlation. available: pearson, spearman
       cor_corc                   = 0.8 // cutoff of correlation
       cor_pvalc                  = 0.1 // pval cutoff of correlation
       
   }

Additionally, we offer a
comparative_analysis tool,which estimates the semantic distance 
(e.g. the similarity of their pathways) of two features signatures. 
Input is a txt file, with each column storing one distinct feature signature. 
Available parameters are:

.. code:: bash

   params{
       comparative_alone = false
       biocomp_input             = "${projectDir}/assets/ensembl_9.txt" // Input
       biocomp_organism          = "hsapiens"   // Organism
       biocomp_keytype          = "gene_symbol" // Type of keys. Available gene_symbol, ensembl, ncbi
       biocomp_ontology         = "GO" // Ontologies MGIMP, Reactome
   }

If the user wishes to run correlation or comparative_analysis as
standalone modules they need to modify the nextflow.config file and run
the command:

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/correlation/main.nf -c multiomicsintegrator/nextflow.config -profile docker

or

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/comparative_analysis/main.nf -c multiomicsintegrator/nextflow.config -profile docker

