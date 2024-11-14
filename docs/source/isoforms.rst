Isoforms
========


Nessecary inputs
----------------

You will need to create a samplesheet with information about the samples
you would like to analyse before running the pipeline. It has to be a
comma-separated file with 5 columns, and a header row as shown in the
examples below.

.. code:: console

   sample,fastq_1,fastq_2,condition,batch
   CONTROL_REP1,AEG588A1_S1_L002_R1_001.fastq.gz,AEG588A1_S1_L002_R2_001.fastq.gz,control,1,0
   CONTROL_REP1,AEG588A1_S1_L003_R1_001.fastq.gz,AEG588A1_S1_L003_R2_001.fastq.gz,control,2,0
   CONTROL_REP1,AEG588A1_S1_L004_R1_001.fastq.gz,AEG588A1_S1_L004_R2_001.fastq.gz,control,3,0

Important
---------

   .. rubric:: Sample names have to be in the first column or in a
      column called sampleID.
      :name: sample-names-have-to-be-in-the-first-column-or-in-a-column-called-sampleid.

   .. rubric:: If you have column names other than **condition** and
      **batch** you need to declare the names in the
      params_isoforms.yml. See below (Preprocess,DEA,PEA)
      :name: if-you-have-column-names-other-than-condition-and-batch-you-need-to-declare-the-names-in-the-params_isoforms.yml.-see-below-preprocessdeapea

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
   SRR2015757,SRX10229011,1
   SRR2015760,SRX10229042,1
   SRR2015761,SRX10229053,1

An `example samplesheet <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/assets/samplesheet_isoforms.csv>`__ has been
provided with the pipeline.

Refference files
----------------

The user has to provide the location of local refference files:

.. code:: bash

   params{
     fasta_isoforms                   : '/home/bianca/gencode2/GRCh38.primary_assembly.genome.fa.gz'
     transcript_fasta_isoforms         : '/home/bianca/gencode2/gencode.v44.transcripts.fa.gz'
     gtf_isoforms                       : '/home/bianca/gencode2/gencode.v44.annotation.gtf.gz'

Running the pipeline
--------------------

In order to run the isoform part of the pipeline you have to modify one
file, specifying which part of the analysis you want to run and
parameters associated with
it: `params_isoforms.yml <https://github.com/ASAGlab/MOI--An-integrated-solution-for-omics-analyses/blob/main/params_isoforms.yml>`__

.. code:: bash

   params{
   outdir   '[full path of location you want to output]'
   salmonDirIso '[full path of directory where outdir is/isoforms/salmon_isoforms/]'
   input_isoforms '[full path of samplesheet with SRA code or location of fastq files]'

   }

In addition you have to provide suitable reference fasta files regarding
genome, transcripts and a gtf file regarding the genomic coordinates of
the organism in study.

.. code:: bash

   params{
   fasta_isoforms   :  '[full path of location of fasta of the genome]'
   transcript_fasta_isoforms:  '[full path of location of fasta of the transcripts ]'
   gtf_isoforms :  '[full path of location of the gtf of the organism]'
   }

The general command to run the pipeline is:

.. code:: bash

   nextflow run nf-core/mom -c parmas_file nf-core/mom/params_isoforms.yml -profile docker 

This will launch the pipeline with the ``docker`` configuration profile.
See below for more information about profiles.

Note that the pipeline will create the following files in your working
directory:

.. code:: bash

   work                # Directory containing the nextflow working files
   <OUTDIR>            # Finished results in specified location (defined with --outdir)
   .nextflow_log       # Log file from Nextflow
   # Other nextflow hidden files, eg. history of pipeline runs and old logs.

The pipeline initially downloads SRA codes and converts the runs into
fastq files. Alternativey you can provide local fastq files. It then
performs quality control with [FASTQC] and then automatically detects
and removes adapterS with [Trimgalore].

Each of the above steps can be skipped, for example if you don’t want to
perform quality control, you can specify in the isoforms.config file:

.. code:: bash

   params{
     skip_qc_isoforms= true
   }

It then employs salmon in order to obtain quantification files with
format 
::

OUTDIR/salmon_isoforms/sampleID/quant.sf.

If you want to skip the alignement step you need to specify the location
of those files in the respective field in the params_isoforms.yml file:

.. code:: bash

   params{
     salmonDirIso # path where your outputs from aligningg are located
   }

Note: All files need to be in the format:
-----------------------------------------

.. code:: plaintext

   salmonDirIso:
   - sampleID/  
     - quant.sf

After that isoformSwitchAnalyzer is used, which takes these
quantification files and performs differential expression analysis on both
the level of isoforms and genes. IsoformSwitchAnalyzer requires a
samplesheet_isoforms.csv (phenotype file) with necessary columns
sampleID and condition. The design matrix is of the form :

.. code:: console

   ~0 + condition

Then diferentially expressed features are collected and their sequences
are annotated regarding their coding potential [CPAT], their homology
with protein domains [Pfam] and the existense of any signaling sequence
[signalP]. This is performed with subworkflow functional_annotation.nf

Next step of the analysis is to asses functional implications of the
differentially isoform/exon usage on the expression of the different
genes and isoforms. We provide many insightful plots for this reason
under the direcorty OUTDIR/isovisual. Moreover, we additionally provide
one output specifically focused on lncRNAs and a correlation matrix
between differentially expressed lncRNAs and genes. Lastly, we provide
the R object if the user wishes to inspect the results more thouroughly.

