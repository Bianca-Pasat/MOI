Welcome to MultiOmicsIntegrator documentation!
=============================================

**MOI** is a nextflow pipeline that aims to cover extensive and diverse omics analyses. `MOI <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses>`_
offers end-to-end analysis of RNAseq, taking into account different RNA molecules, like isoforms of genes and miRNAs. 
In addition to traditional RNAseq pipelines, MOI has tools for functional annotation and secondary structure prediction of RNA transcripts. 
Moreover, it can cover proteomics and metabolomics analyses. For the latter, MOI has additional tools for accessing differences in 
specific biochemical attributes. All available omics-specific workflows can run autonomously, however, if the user wishes they can integrate
their data either with data driven or biology driven approaches. 
Finally, differentiating from existing pipelines we offer biotranslator as our pathway enrichment algorithm as it can better mitigate the 
high noise inhereit with biological data as well as omnipathr for advance downstream analyses.




.. note::

   This project is additional documentation for MOI: MultiOmicsIntegrator: an integrated solution for omics analyses.


General inputs and outputs
--------------------------

The MOI pipeline is organized into individual modules, each responsible
for a specific step in the analysis workflow. The modular design
facilitates code flexibility in incorporating new analyses techniques or
custom implementations, as well as easy maintenance and scalability.

MOI’s behavior is regulated through the params.yml files, each named to
align with the specific analysis segment they govern. In those files the
user is tasked with specifying input and output parameters and with the
optional fine-tuning intricacies such as algorithm selection and
algorithmic configurations.

The pipeline’s inputs are streamlined to one csv file. This file
accommodates either a solitary column of SRA codes or a directory
pointing to the location of fastq files, along with any other metadata
pertaining to their samples. If the analysis commences with count (abundance)
matrices the user can specify the directory of the this matrix along
with a phenotype file.

MOI produces extensive outputs, including informative plots and
intermediate results in the form of text and RData objects for each
module, accommodating users who seek further utilization or detailed
inspection of results. Outputs are organized hierarchically based on the
user’s parameterization; for example, the pathway enrichment analysis of
genes will be located under the directory
“/user_defined_output_directory/genes/biotranslator/”.

Most important tools
--------------------

+----------------+------------------------------------+----------------+
| Omics          | Functionality                      | Tools          |
+================+====================================+================+
| Genes, miRNA,  | SRA download                       | SRA toolkit    |
| isoforms       |                                    |                |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Quality control                    | FastQC,        |
| isoforms       |                                    | trimgalore     |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Align and Assembly                 | Salmon,        |
| isoforms       |                                    | samtools,      |
|                |                                    | STAR, Hisat2,  |
|                |                                    | StringTie2     |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Data preprocessing                 | R packages:    |
| isoforms,      |                                    | edger, limma,  |
| proteins,      |                                    | sva, ggplot2,  |
| lipids         |                                    | ComplexHeatmap |
+----------------+------------------------------------+----------------+
| Proteins,      | Specific for proteins and lipids   | R packages:    |
| lipids         |                                    | p              |
|                |                                    | reprocesscore, |
|                |                                    | mstus          |
|                |                                    | normalization  |
+----------------+------------------------------------+----------------+
| Lipids         | Specific for lipids                | R packags:     |
|                |                                    | lipidr         |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Differential expression analyss    | R packages:    |
| isoforms,      |                                    | DESeq2, edger, |
| proteins,      |                                    | RankProd,      |
| lipids         |                                    | ggplot2        |
|                |                                    | ComplexHeatmap |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Correlation analysis               | R package      |
| isoforms,      |                                    | stats          |
| proteins,      |                                    |                |
| lipids         |                                    |                |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Pathway enrichment analysis        | Cl             |
| isoforms,      |                                    | usterprofiler, |
| proteins,      |                                    | Biotranslator  |
| lipids         |                                    |                |
+----------------+------------------------------------+----------------+
| Lipids         | Specific for lipids pathway        | Custom tool:   |
|                | enrichment analysis                | Lipidb         |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | RIDDER (module to identify IRE1    | gRIDD,         |
| isoforms,      | substrates)                        | RNAeval, fimo  |
| proteins       |                                    |                |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Functional annotation              | CPAT, signalP, |
| isoforms       |                                    | pfam           |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Secondary structure prediction     | RNAfold,       |
| isoforms,      |                                    | RNAeval        |
| proteins       |                                    |                |
+----------------+------------------------------------+----------------+
| Genes, miRNA,  | Find motif                         | fimo           |
| isoforms,      |                                    |                |
| proteins       |                                    |                |
+----------------+------------------------------------+----------------+
| Isoforms       | Genome wide isoform analysis       | Isoform        |
|                |                                    | SwitchAnalyzer |
+----------------+------------------------------------+----------------+


Contents
--------

.. toctree::


   installation
   genes
   isoforms
   miRNA
   proteins
   lipids
   integration
   example_outputs
   general
   ----------

