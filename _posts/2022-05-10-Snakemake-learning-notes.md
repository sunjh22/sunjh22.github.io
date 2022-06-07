---
title:  "Snakemake Tutorial"
date:   2022-05-10
categories: [Pipeline]
tags: [Tutorial, Snakemake]
render_with_liquid: false
---

This post records the learning of Snakemake. Refered to this [tutorial](https://snakemake.readthedocs.io/en/stable/)

The Snakemake workflow management system is a tool to create reproducible and scalable data analyses. Workflows are described via a human readable, Python based language. They can be seamlessly scaled to server, cluster, grid and cloud environments, without the need to modify the workflow definition. Finally, Snakemake workflows can entail a description of required software, which will be automatically deployed to any execution environment.

The first and the most important thing is that you know how to do the thing, sequences, tools and their version, input and output, parameters and log files.

Keep in mind that Snakemake could help you build a workflow that make the analysis reproducible, but itself does not analyze anything.

## Install

Recommand install through conda or mamba
    
    mamba install -c bioconda snakemake

## Example from Snakemake tutorial

Config file - store global variables in dictionary, can be wrote in JSON or YAML
```yaml
samples:
    A: data/samples/A.fastq
    B: data/samples/B.fastq

prior_mutation_rate: 0.001
```

Snakefile
```python
configfile: "config.yaml"


rule all:
    input:
        "plots/quals.svg"


def get_bwa_map_input_fastqs(wildcards):
    return config["samples"][wildcards.sample]


rule bwa_map:
    input:
        "data/genome.fa",
        get_bwa_map_input_fastqs
    output:
        temp("mapped_reads/{sample}.bam")
    params:
        rg=r"@RG\tID:{sample}\tSM:{sample}"
    log:
        "logs/bwa_mem/{sample}.log"
    threads: 8
    shell:
        "(bwa mem -R '{params.rg}' -t {threads} {input} | "
        "samtools view -Sb - > {output}) 2> {log}"


rule samtools_sort:
    input:
        "mapped_reads/{sample}.bam"
    output:
        protected("sorted_reads/{sample}.bam")
    shell:
        "samtools sort -T sorted_reads/{wildcards.sample} "
        "-O bam {input} > {output}"


rule samtools_index:
    input:
        "sorted_reads/{sample}.bam"
    output:
        "sorted_reads/{sample}.bam.bai"
    shell:
        "samtools index {input}"


rule bcftools_call:
    input:
        fa="data/genome.fa",
        bam=expand("sorted_reads/{sample}.bam", sample=config["samples"]),
        bai=expand("sorted_reads/{sample}.bam.bai", sample=config["samples"])
    output:
        "calls/all.vcf"
    params:
        rate=config["prior_mutation_rate"]
    log:
        "logs/bcftools_call/all.log"
    shell:
        "(bcftools mpileup -f {input.fa} {input.bam} | "
        "bcftools call -mv -P {params.rate} - > {output}) 2> {log}"


rule plot_quals:
    input:
        "calls/all.vcf"
    output:
        "plots/quals.svg"
    script:
        "scripts/plot-quals.py"


# wrapper version bwa_mem
rule bwa_mem:
  input:
      ref="data/genome.fa",
      sample=lambda wildcards: config["samples"][wildcards.sample]
  output:
      temp("mapped_reads/{sample}.bam")
  log:
      "logs/bwa_mem/{sample}.log"
  params:
      "-R '@RG\tID:{sample}\tSM:{sample}'"
  threads: 8
  wrapper:
      "0.15.3/bio/bwa/mem"
```

`envs/samtools.yaml`
```yaml
channels:
  - bioconda
  - conda-forge
dependencies:
  - samtools =1.9
```

## Rules in snakemake
. `rule`

. `wildcards` - 用来获取通配符匹配到的部分

. `input`

. `output`

. `shell`

. `threads`

. `message`

. `script` - you can use `snakemake.input[0]` to access the properties of the rule in Python, and `snakemake@input[[1]]` or `snakemake@input[["myfile"]]` in R

. `log`

. `params`

. `run` - write python code in this block

. `temp` - assign temperary files, will be removed after running

. `protected`

. `benchmark` - with the benchmark directive, Snakemake can be instructed to measure the wall clock time of a job

. `include` - include another Snakefile into the current one

. `conda` - specify Conda environments per rule, required tools and their version can be specified in a file `envs/samtools.yaml` as shown in above

. `wrappers` - A wrapper is a short script that wraps (typically) a command line application and makes it directly addressable from within Snakemake

. `expand` - produces a list, an example `expand("sorted_reads/{sample}.{replicate}.bam", sample=SAMPLES, replicate=[0, 1])`

. `glob_wildcards` - can extract all matched contents to a list

. Input function - determine the input of a rule through a function

. Rule Dependencies - output of former rules can be the input of this rule

## Execution

. `snakemake -np`, dry run and print shell commands, if `--cores` command is given without a number, all available cores are used

. `snakemake targetfiles --cores 8`, run for single specific targets, snakemake will automatically find dependency

. `snakemake somerules --core 8`, run specific rules, the output of the rule will be the target

. `--forceall`, force to re-run specific rules or targets

. `snakemake --use-conda --cores 1`, will automatically create required environments and activate them before a job is executed

. `snakemake --dag | dot -Tpdf > dag.pdf`, build a graph showing the logic of snakemake run

. `snakemake --dag | dot -Tsvg > dag.svg`

- Snakemake allows generalizing rules by using named wildcards, you can have multiple wildcards in your file paths, however, to avoid conflicts with other jobs of the same rule, all output files of a rule have to contain exactly the same wildcards.

- Snakemake automatically creates missing directories before jobs are executed

- Snakemake allows to access wildcards in the shell command via the wildcards object that has an attribute with the value for each wildcard

- In the Python script, all properties of the rule like input, output, wildcards, etc. are available as attributes of a global snakemake object

- Snakemake workflows are executed in three phases

	- In the initialization phase, the files defining the workflow are parsed and all rules are instantiated.

	- In the DAG phase, the directed acyclic dependency graph of all jobs is built by filling wildcards and matching input files to output files.

	- In the scheduling phase, the DAG of jobs is executed, with jobs started according to the available resources.

## Additional features

### Benchmarking

With the `benchmark` directive, Snakemake can be instructed to measure the wall clock time of a job.

### Modularization

Snakemake provides the `include` directive to include another Snakefile into the current one

### Automatic deployment of software dependencies

Use `conda` directive, execute snakemake with `snakemake --use-conda --cores 1`

### Tool wrappers

A wrapper is a short script that wraps (typically) a command line application and makes it directly addressable from within Snakemake. [Snakemake wrapper repository](https://snakemake-wrappers.readthedocs.io/)

### Cluster execution

`snakemake --cluster qsub --jobs 10`

### Constraining wildcards

the wildcard sample in the output file `"sorted_reads/{sample}.bam"` can be constrained to only allow alphanumeric sample names as `"sorted_reads/{sample,[A-Za-z0-9]+}.bam"`.