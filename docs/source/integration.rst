Integration
================================

Necessary inputs
----------------

The pipeline can be used to integrate simultaneously different types of omics data.

The method we currently provide is Multiple Co-Inertia Analysis (MCIA).

You can either run MCIA with your own data or choose this module as an
additional step in your analyses. Check
`params_mcia.yml <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/params_mcia.yml>`__.

You need to provide the path in (pathmcia variable) in which you store
the different omics data and a samples info file describing the metadata
of each sample.

   The path you provide should consist of sub-directories named after
   the omics type you have. For example, if I have proteins and miRNAs my
   MCIA directory will look like:

.. code:: plaintext

   - mcia
       -genes
         - genes.txt
       - proteins
         - proteins.txt
       - lipids
         - lipids.txt
       

Alternatively, if MCIA is used with pipeline-generated data you
should declare these variables as follows:

.. code:: bash

   params{
     outdir: '/home/results/' ### If outdir =  results then pathmcia should: /complete/path/to/results/mcia/ biotransl_all_path:path/to/results/prepareforbio
     pathmcia  = '/home/results/mcia/'  # should of the string of the format /complete/path/to/results/mcia/ 
     biotrans_all_path  : "/home/results/prepareforbio/"  # should of the string of the format /complete/path/to/results/prepareforbio/ 
     }

Additionally, you need to change parameters in
`params_mcia.yml <../params_mcia.yml>`__ appropriately:

.. code:: bash


   params{
     runmcia = true
     samplesinfomcia = 'path of you samples info file'
     a1lim = limits of your X space, Default :  '0, Inf'
     a2lim = limits of your Y space, Default :  '0, Inf'
   }

..

   Change the location of the files appropriately

Important
~~~~~~~~~

   .. rubric:: Sample names have to be in the first column or in a
      column called sampleID and **need to match** the column names of
      your count matrix.
      :name: sample-names-have-to-be-in-the-first-column-or-in-a-column-called-sampleid-and-need-to-match-the-column-names-of-your-count-matrix.

   .. rubric:: If you have column names other than **condition** you
      need to declare the names in the params_mcia.yml.
      :name: if-you-have-column-names-other-than-condition-you-need-to-declare-the-names-in-the-params_mcia.yml.

   .. rubric:: Sample names have to be identical between different omics types!

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
-------------

MCIA utilizes techniques such as covariance analysis,
optimization, and dimensionality reduction to integrate diverse omics
datasets. It starts by centering and scaling data, then identifies
shared patterns through joint covariance analysis. Through optimization,
it determines coefficients for each feature, quantifying their
contribution to the shared structures. The method constructs latent
variables (or components), representing these shared patterns. If needed,
dimensionality reduction is applied for a more concise interpretation.
This comprehensive approach allows MCIA to effectively
capture commonalities and distinctions in multi-omics data, offering
insights into complex biological relationships.

Additionally, we offer the possibility of functional integration of data
to cover scenarios in which MCIA cannot be applied. Namely, we offer 


LipiDB
~~~~~~~~~~~~~~~~~~~

LipidR will produce differentially expressed features for each category
of lipids. Subsequently, LipiDB, using KREGGREST and a local daabase, will find genes
associated to these differentially expressed lipids, for each category.
Input is the result of lipidR or in other words a txt file that has deregulated lipids along with their logFC and pval (Columns should be in that order).
The results are in as form of a text file and a heatmap.

If the user wants to run LipiDB alone they need to declare it in nextflow.confi:

.. code:: bash

      params{
         lipidb_alone = true
         }

and this is the command to run it alone:

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/annotate_lipids/main.nf -c multiomicsintegrator/nextflow.config -profile docker

multiMiR
~~~~~~~~~

MultiMiR is a database that stores predicted and experimentaly targets of miRNA. 
As input it takes a txt file containing differentially expressed miRNAs, in a single column.
The output consists of two files, one containing only the targets and one storing
the miRNA with their targets. 

If the user wants to run multiMiR alone they need to declare it in nextflow.config:

.. code:: bash

      params{
         multimir_alone = true
         }

and this is the command to run it alone:

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/multimir/main.nf -c multiomicsintegrator/nextflow.config -profile docker


Exploratory analysis
~~~~~~~~~~~~~~~~~~~~
The pipeline produces automatically a heatmap with differentially expressed
features and their presence across available omics layers. As input it takes
differentially expressed features and, optionally, the results from multiMiR and 
LipiDB. 

If the user wants to run multiMiR alone they need to declare it in nextflow.config:

.. code:: bash

      params{
         preparedf_alone = true
         preparedf_alone_genes = '[Logical, do you have genes?]'
         preparedf_alone_mirna = '[Logical, do you have miRNA?]'
         preparedf_alone_proteins = '[Logical, do you have proteins?]'
         preparedf_alone_lipids = '[Logical, do you have lipids?]'
         preparedf_alone_isoforms = '[Logical, do you have isoforms?]'
         preparedf_alone_integrated = '[Logical, have you applied mcia?]'
         preparedf_alone_integratedafterlipids = '[Logical, have you applied mcia and lipidomic analysis?]'
         preparedf_alone_path   = '[Directory of the inputs]'  
         preparedf_alone_alg_genes = '[Algorithm used for genes]'  
         preparedf_alone_alg_mirna = '[Algorithm used for miRNA]'  
         preparedf_alone_alg_proteins = '[Algorithm used for proteins]'  
         preparedf_alone_pval  = '[pvalue cut off]'  
         }

Extremely important:
The files should follow the same naming system as the output of MOI, for example for genes : genes_defeatures.txt!!!! 

and this is the command to run it alone:

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/prepare_for_bio_alone/main.nf -c multiomicsintegrator/nextflow.config -profile docker

Correlation analysis
~~~~~~~~~~~~~~~~~~~~~

To estimate correlation between differentially expressed features we suggest to use the count matrices of the differentially expressed features.

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
**comparative_analysis** tool, which estimates the semantic distance 
(e.g., the similarity of their pathways) of two features signatures. 
Input is a txt file, with each column storing one distinct feature signature. 
Available parameters are:

.. code:: bash

   params{
       comparative_alone = [logcal, if you want to run it as a standalone module, default : false]
       biocomp_input             = ['Input']
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



OmnipathR
~~~~~~~~~~~~
OmnipathR is a knowledge database that stores multiple levels of biological information. In MOI OmnipathR can run as part of the pipeline or as a standalone tool. As part of the pipeline it takes the hub genes and forms a network of protein protein interactions. Moreover, it can annotate the hub features based on the role of the feature (e.g., ligand, transcription factor etc.) in the signaling pathway they reside in. By leveraging this information it can then reconstruct the pathways that exist in the network, an aspect crucial in signaling specific contexts. 

Detailed information on how to run the tool is listed below: 

.. code:: bash

    params {
        omnipath_biotrans = '[directory containing the outputs of biotranslator, relative to outdir]' 
        omnipath_choose = '[choose_omics, choose_role]'
        omnipath_choose_type = '[logical, specify if additional annotation is desired]'
        omnipath_additional_info_bool = '[Logical, whether you want additional annotation]'
        omnipath_additional_info_val = '[Must be present in get_omnipath_resources(), e.g., "SignaLink pathway"]'
        omnipath_additional_info_attribute = '[Must be in get_omnipath_resources(omnipath_annot), e.g., "TGF" (omnipath_annot is declared above)]'
    }




If the user want to run the tool as a standalone module for a single omics they need one extra argument:

.. code:: bash

   params{
     omnipath_alone = '[logical, T]'
   }



The command to run the tool as a standalone module is

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/omnipath/main.nf -c multiomicsintegrator/nextflow.config -profile docker


Moreover, if the user has multiple omics and wants to integrate them after the step of differential expression rather than after pathway enrichment analysis they need to supply an additional file with columns Gene (gene symbol) and Category (omics type). 
This file is automatically produced by MOI and is called genes_across_omics.txt


.. code:: bash

   params{
     omnipath_biotrans = '[ directory that has the outputs of biotranslator, should be relative to outdir]' 
     omnipath_integrated_gao = '[ path of file genes_across_omics ]' 
     omnipath_choose   = '[choose_omics, choose_role]'
     omnipath_choose_type = '[logical, do you want additional annotation]'
     omnipath_additional_info_bool = '[Logical, whether you want additional annotation]'
     omnipath_additional_info_val = '[Must be present in get_omnipath_resources(), for example "SignaLink pathway"]'
     omnipath_additional_info_attribute = '[Must be a get_omnipath_resources(omnipath_annot), for example "TGF" (omnipath_annot is declared above)]'
   }
    

If the user wants to run the tool as a standalone module for a single omics they need one extra argument:

.. code:: bash

   params{
     omnipath_integrated_alone = '[logical, T]'
   }



The command to run it as a standalone module is:

.. code:: bash

   nextflow run multiomicsintegrator/modules/local/omnipath_integrated/main.nf -c multiomicsintegrator/nextflow.config -profile docker


Additional omics types
~~~~~~~~~~~~~~~~~~~~~~~~~

MOI can be extended to other omics types as well. If supplied with abundance matrices (for example, glycomics) MOI can integrate them with MCIA, after performing basic filtering and normalization steps. 
If translated into the gene level, MOI can integrate them with the exploratory analysis tool, multiMiR, lipidDB as explained above. In addition, if translated to the gene level additional omics types can be integrated with high-level approaches like biotranslator, comparative analysis tool or OmnipathR. 
The user will treat these data as if they were gene data.  
