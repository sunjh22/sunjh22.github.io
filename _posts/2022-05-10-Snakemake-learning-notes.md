---
title:  "Snakemake Tutorial"
date:   2022-05-10
categories: [Pipeline]
tags: [Tutorial, Snakemake]
render_with_liquid: false
---

This post records the learning of Snakemake. Refered to this [tutorial](https://snakemake.readthedocs.io/en/stable/)

The first and the most important thing is that you know how to do the thing, sequences, tools and their version, input and output, parameters and log files.

Keep in mind that Snakemake could help you build a workflow that make the analysis reproducible, but itself does not analyze anything.

## Install

Recommand install through conda or mamba
    
    mamba install -c bioconda snakemake

## Example files from Snakemake tutorial

### Config file

store global variables in dictionary, can be wrote in JSON or YAML

```yaml
samples:
    A: data/samples/A.fastq
    B: data/samples/B.fastq

prior_mutation_rate: 0.001
```

### Snakefile

Main part of snakemake workflow, containing all rules need to be excuted, use `include` to include other Snakemake files

```python
configfile: "config.yaml"

# localrules all require all intended output, it will not be submitted to cluster
localrules: all
rule all:
    input:
        "plots/quals.svg"

# input function, enable us to process input files, like add if to filter some conditions
def get_bwa_map_input_fastqs(wildcards):
    return config["samples"][wildcards.sample]

# generally, for one rule, input, output, params, threads are necessary; log and benchmark
# could be used to record the running information; conda could indicate specific environment;
# four ways to run: shell, script, wrapper and directly write Python code.
# {sample} here is a wildcard, snakemake will automatically determine the content of wildcard
# based on the whole workflow. In rule all, wildcard can not be used, it needs explicit target.
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

# temp() tell snakemake to delete the intermediate files after no process is dependent on them
# protected() could protect the important files in case they are deleted or covered accidently.
# {sample} and {wildcards.sample} are equivalent here.
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

# expand() is a very useful function, it returns a list
# multiext is variant of expand(), it is useful in reference index
# usage: multiext("refs/genome", ".fa", ".fa.fai", ".bwt")
# use a () to put all commands in shell to one, then log can log everything happened.
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


# wrapper is pre-compiled tool distributed in snakemake-wrapper. The pattern of whole rule
# should be identical to what has been defined in wrapper, or it will throw errors. When
# running, snakemake will automatically download a conda env to run the tool. You can sepcify
# the path for conda env in command line `snakemake --conda-env /a/path/to/`
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

### Environment file

Environment file for specific tool to run: `envs/samtools.yaml`, used under `conda` parameter.

```yaml
channels:
  - bioconda
  - conda-forge
dependencies:
  - samtools =1.9
```

## Snakemake execution

- `snakemake -np`, dry run and print shell commands, if `--cores` command is given without a number, all available cores are used

- `snakemake targetfiles --cores 8`, run for single specific targets, snakemake will automatically find dependency

- `snakemake somerules --core 8`, run specific rules, the output of the rule will be the target

- `--forceall`, force to re-run specific rules or targets

- `snakemake --use-conda --cores 1`, will automatically create required environments and activate them before a job is executed

- `snakemake --dag | dot -Tpdf > dag.pdf` or `snakemake --dag | dot -Tsvg > dag.svg`, build a graph showing the workflow logic

- `snakemake --cluster qsub --jobs 10`, run snakemake in clusters

- `snakemake --use-conda --cores 4 --conda-frontend mamba --conda-prefix ~/data/biosoft/grenepipe/conda-envs --directory example/`, a complicated but useful snakemake run

- Snakemake automatically creates missing directories before jobs are executed

## Additional features

1. use `include` to include other Snakefile into current one, this feature enable us to separate a large workflow into small pieces
2. under `script` parameter, in Python script we can access Snakemake properties by like `snakemake.input[0]`, or we can do that in R script by `snakemake@input[[1]]`

## One good snakemake example - GrenePipe

    git clone https://github.com/moiexpositoalonsolab/grenepipe.git
    conda install mamba -n base -c conda-forge
    mamba env create -f envs/grenepipe.yaml
    conda activate grenepipe
    cd grenepipe
    ./example/prepare.sh
    snakemake --use-conda --cores 4 --conda-frontend mamba --conda-prefix ~/data/biosoft/grenepipe/conda-envs --directory example/ 2>2.log