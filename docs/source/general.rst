Core Nextflow arguments
========================

   **NB:** These options are part of Nextflow and use a *single* hyphen
   (pipeline parameters use a double-hyphen).

``-profile``
~~~~~~~~~~~~

Use this parameter to choose a configuration profile. Profiles can give
configuration presets for different compute environments.

Several generic profiles are bundled with the pipeline which instruct
the pipeline to use software packaged using different methods (Docker,
Singularity, Podman, Shifter, Charliecloud, Conda) - see below.

   We highly recommend the use of Docker or Singularity containers for
   full pipeline reproducibility, however, when this is not possible
   Conda is also supported.

The pipeline also dynamically loads configurations from
https://github.com/nf-core/configs when it runs, making multiple config
profiles for various institutional clusters available at run time. For
more information and to see if your system is available in these configs
please see the `nf-core/configs
documentation <https://github.com/nf-core/configs#documentation>`__.

Note that multiple profiles can be loaded, for example:
``-profile test,docker`` - the order of arguments is important! They are
loaded in sequence, so later profiles can overwrite earlier profiles.

If ``-profile`` is not specified, the pipeline will run locally and
expect all software to be installed and available on the ``PATH``. This
is *not* recommended, since it can lead to different results on
different machines dependent on the computer enviroment.

-  ``test``

   -  A profile with a complete configuration for automated testing
   -  Includes links to test data so needs no other parameters

-  ``docker``

   -  A generic configuration profile to be used with
      `Docker <https://docker.com/>`__

-  ``singularity``

   -  A generic configuration profile to be used with
      `Singularity <https://sylabs.io/docs/>`__

-  ``podman``

   -  A generic configuration profile to be used with
      `Podman <https://podman.io/>`__

-  ``shifter``

   -  A generic configuration profile to be used with
      `Shifter <https://nersc.gitlab.io/development/shifter/how-to-use/>`__

-  ``charliecloud``

   -  A generic configuration profile to be used with
      `Charliecloud <https://hpc.github.io/charliecloud/>`__

-  ``conda``

   -  A generic configuration profile to be used with
      `Conda <https://conda.io/docs/>`__. Please only use Conda as a
      last resort i.e. when it’s not possible to run the pipeline with
      Docker, Singularity, Podman, Shifter or Charliecloud.

``-resume``
~~~~~~~~~~~

Specify this when restarting a pipeline. Nextflow will use cached
results from any pipeline steps where the inputs are the same,
continuing from where it got to previously. For input to be considered
the same, not only the names must be identical but the files’ contents
as well. For more info about this parameter, see `this blog
post <https://www.nextflow.io/blog/2019/demystifying-nextflow-resume.html>`__.

You can also supply a run name to resume a specific run:
``-resume [run-name]``. Use the ``nextflow log`` command to show
previous run names.

Custom configuration
--------------------

Resource requests
~~~~~~~~~~~~~~~~~

Whilst the default requirements set within the pipeline will hopefully
work for most people and with most input data, you may find that you
want to customise the compute resources that the pipeline requests. Each
step in the pipeline has a default set of requirements for number of
CPUs, memory and time. For most of the steps in the pipeline, if the job
exits with any of the error codes specified
`here <https://github.com/nf-core/rnaseq/blob/4c27ef5610c87db00c3c5a3eed10b1d161abf575/conf/base.config#L18>`__
it will automatically be resubmitted with higher requests (2 x original,
then 3 x original). If it still fails after the third attempt then the
pipeline execution is stopped.

For example, if the nf-core/rnaseq pipeline is failing after multiple
re-submissions of the ``STAR_ALIGN`` process due to an exit code of
``137`` this would indicate that there is an out of memory issue:

.. code:: console

   [62/149eb0] NOTE: Process `NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:STAR_ALIGN (WT_REP1)` terminated with an error exit status (137) -- Execution is retried (1)
   Error executing process > 'NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:STAR_ALIGN (WT_REP1)'

   Caused by:
       Process `NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:STAR_ALIGN (WT_REP1)` terminated with an error exit status (137)

   Command executed:
       STAR \
           --genomeDir star \
           --readFilesIn WT_REP1_trimmed.fq.gz  \
           --runThreadN 2 \
           --outFileNamePrefix WT_REP1. \
           <TRUNCATED>

   Command exit status:
       137

   Command output:
       (empty)

   Command error:
       .command.sh: line 9:  30 Killed    STAR --genomeDir star --readFilesIn WT_REP1_trimmed.fq.gz --runThreadN 2 --outFileNamePrefix WT_REP1. <TRUNCATED>
   Work dir:
       /home/pipelinetest/work/9d/172ca5881234073e8d76f2a19c88fb

   Tip: you can replicate the issue by changing to the process work dir and entering the command `bash .command.run`

For beginners
^^^^^^^^^^^^^

A first step to bypass this error, you could try to increase the amount
of CPUs, memory, and time for the whole pipeline. Therefore, you can try
to increase the resource for the parameters ``--max_cpus``,
``--max_memory``, and ``--max_time``. Based on the error above, you have
to increase the amount of memory. Therefore, you can go to the `parameter
documentation of rnaseq <https://nf-co.re/rnaseq/3.9/parameters>`__ and
scroll down to the ``show hidden parameter`` button to get the default
value for ``--max_memory``. In this case 128GB, you than can try to run
your pipeline again with ``--max_memory 200GB -resume`` to skip all
process, that were already calculated. If you cannot increase the
resource of the complete pipeline, you can try to adapt the resource for
a single process as mentioned below.

Advanced option on process level
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To bypass this error you would need to find exactly which resources are
set by the ``STAR_ALIGN`` process. The quickest way is to search for
``process STAR_ALIGN`` in the `nf-core/rnaseq Github
repo <https://github.com/nf-core/rnaseq/search?q=process+STAR_ALIGN>`__.
We have standardised the structure of Nextflow DSL2 pipelines such that
all module files will be present in the ``modules/`` directory and so,
based on the search results, the file we want is
``modules/nf-core/star/align/main.nf``. If you click on the link to that
file you will notice that there is a ``label`` directive at the top of
the module that is set to
```label process_high`` <https://github.com/nf-core/rnaseq/blob/4c27ef5610c87db00c3c5a3eed10b1d161abf575/modules/nf-core/software/star/align/main.nf#L9>`__.
The `Nextflow
``label`` <https://www.nextflow.io/docs/latest/process.html#label>`__
directive allows us to organise workflow processes in separate groups
that can be referenced in a configuration file to select and configure
subset of processes having similar computing requirements. The default
values for the ``process_high`` label are set in the pipeline’s
```base.config`` <https://github.com/nf-core/rnaseq/blob/4c27ef5610c87db00c3c5a3eed10b1d161abf575/conf/base.config#L33-L37>`__
which in this case is defined as 72GB. Providing you haven’t set any
other standard nf-core parameters to **cap** the `maximum
resources <https://nf-co.re/usage/configuration#max-resources>`__ used
by the pipeline then we can try and bypass the ``STAR_ALIGN`` process
failure by creating a custom config file that sets at least 72GB of
memory, in this case increased to 100GB. The custom config below can
then be provided to the pipeline via the ```-c`` <#-c>`__ parameter as
highlighted in previous sections.

.. code:: nextflow

   process {
       withName: 'NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:STAR_ALIGN' {
           memory = 100.GB
       }
   }

..

   **NB:** We specify the full process name
   i.e. ``NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:STAR_ALIGN`` in the config
   file because this takes priority over the short name (``STAR_ALIGN``)
   and allows existing configuration using the full process name to be
   correctly overridden.

   If you get a warning suggesting that the process selector isn’t
   recognised check that the process name has been specified correctly.

Updating containers (advanced users)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The `Nextflow DSL2 <https://www.nextflow.io/docs/latest/dsl2.html>`__
implementation of this pipeline uses one container per process which
makes it much easier to maintain and update software dependencies. If
for some reason you need to use a different version of a particular tool
with the pipeline then you just need to identify the ``process`` name
and override the Nextflow ``container`` definition for that process
using the ``withName`` declaration. For example, in the
`nf-core/viralrecon <https://nf-co.re/viralrecon>`__ pipeline a tool
called `Pangolin <https://github.com/cov-lineages/pangolin>`__ has been
used during the COVID-19 pandemic to assign lineages to SARS-CoV-2
genome sequenced samples. Given that the lineage assignments change
quite frequently it doesn’t make sense to re-release the
nf-core/viralrecon everytime a new version of Pangolin has been
released. However, you can override the default container used by the
pipeline by creating a custom config file and passing it as a
command-line argument via ``-c custom.config``.

1. Check the default version used by the pipeline in the module file for
   `Pangolin <https://github.com/nf-core/viralrecon/blob/a85d5969f9025409e3618d6c280ef15ce417df65/modules/nf-core/software/pangolin/main.nf#L14-L19>`__

2. Find the latest version of the Biocontainer available on
   `Quay.io <https://quay.io/repository/biocontainers/pangolin?tag=latest&tab=tags>`__

3. Create the custom config accordingly:

   -  For Docker:

      .. code:: nextflow

         process {
             withName: PANGOLIN {
                 container = 'quay.io/biocontainers/pangolin:3.0.5--pyhdfd78af_0'
             }
         }

   -  For Singularity:

      .. code:: nextflow

         process {
             withName: PANGOLIN {
                 container = 'https://depot.galaxyproject.org/singularity/pangolin:3.0.5--pyhdfd78af_0'
             }
         }

   -  For Conda:

      .. code:: nextflow

         process {
             withName: PANGOLIN {
                 conda = 'bioconda::pangolin=3.0.5'
             }
         }

..

   **NB:** If you wish to periodically update individual tool-specific
   results (e.g., Pangolin) generated by the pipeline then you must
   ensure to keep the ``work/`` directory otherwise the ``-resume``
   ability of the pipeline will be compromised and it will restart from
   scratch.

nf-core/configs
~~~~~~~~~~~~~~~

In most cases, you will only need to create a custom config as a one-off
but if you and others within your organisation are likely to be running
nf-core pipelines regularly and need to use the same settings regularly
it may be a good idea to request that your custom config file is
uploaded to the ``nf-core/configs`` git repository. Before you do this
please can you test that the config file works with your pipeline of
choice using the ``-c`` parameter. You can then create a pull request to
the ``nf-core/configs`` repository with the addition of your config
file, associated documentation file (see examples in
```nf-core/configs/docs`` <https://github.com/nf-core/configs/tree/master/docs>`__),
and amending
```nfcore_custom.config`` <https://github.com/nf-core/configs/blob/master/nfcore_custom.config>`__
to include your custom profile.

See the main `Nextflow
documentation <https://www.nextflow.io/docs/latest/config.html>`__ for
more information about creating your own configuration files.

If you have any questions or issues please send us a message on
`Slack <https://nf-co.re/join/slack>`__ on the ```#configs``
channel <https://nfcore.slack.com/channels/configs>`__.

Azure Resource Requests
-----------------------

To be used with the ``azurebatch`` profile by specifying the
``-profile azurebatch``. We recommend providing a compute
``params.vm_type`` of ``Standard_D16_v3`` VMs by default but these
options can be changed if required.

Note that the choice of VM size depends on your quota and the overall
workload during the analysis. For a thorough list, please refer the
`Azure Sizes for virtual machines in
Azure <https://docs.microsoft.com/en-us/azure/virtual-machines/sizes>`__.

Running in the background
-------------------------

Nextflow handles job submissions and supervises the running jobs. The
Nextflow process must run until the pipeline is finished.

The Nextflow ``-bg`` flag launches Nextflow in the background, detached
from your terminal so that the workflow does not stop if you log out of
your session. The logs are saved to a file.

Alternatively, you can use ``screen`` / ``tmux`` or similar tool to
create a detached session which you can log back into at a later time.
Some HPC setups also allow you to run nextflow within a cluster job
submitted your job scheduler (from where it submits more jobs).

Nextflow memory requirements
----------------------------

In some cases, the Nextflow Java virtual machines can start to request a
large amount of memory. We recommend adding the following line to your
environment to limit this (typically in ``~/.bashrc`` or
``~./bash_profile``):

.. code:: bash

   NXF_OPTS='-Xms1g -Xmx4g'
