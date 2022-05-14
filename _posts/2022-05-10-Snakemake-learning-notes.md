---
title:  "Snakemake Tutorial"
date:   2022-05-10
categories: [Pipeline]
tags: [Tutorial, Snakemake]
render_with_liquid: false
---

This post records the learning of Snakemake. Refered to this [tutorial](https://snakemake.readthedocs.io/en/stable/)

The Snakemake workflow management system is a tool to create reproducible and scalable data analyses. Workflows are described via a human readable, Python based language. They can be seamlessly scaled to server, cluster, grid and cloud environments, without the need to modify the workflow definition. Finally, Snakemake workflows can entail a description of required software, which will be automatically deployed to any execution environment.

The first and the most important thing is that you know the whole pipeline to do the thing, the sequences, the tools, the version of tools, the input and output, the parameters, the log files.

Keep in mind that Snakemake helps you build a workflow that make you reproduce your analysis easily, but itself will not analyze anything.

## An example
Config file
```yaml
samples:
    A: data/samples/A.fastq
    B: data/samples/B.fastq

prior_mutation_rate: 0.001
```

Sankefile
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
    benchmark:
        "benchmarks/{sample}.bwa.benchmark.txt"
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
```

## Useful command line
- dry-run and print shell commands: `snakemake -np`

- visualize the DAG of jobs using the Graphviz dot command: `snakemake --dag | dot -Tsvg > dag.svg`

- execute the workflow with 8 cores: `snakemake --cores 8`

- `expand()` produces a list, an example `expand("sorted_reads/{sample}.{replicate}.bam", sample=SAMPLES, replicate=[0, 1])`

- `--forcerun` forcing re-execution of parts of the workflow, `--forceall` forcing re-execution of whole workflow

- If `--cores` command is given without a number, all available cores are used

- Snakemake will load the config file and store its contents into a globally available dictionary named `config`

## Directory structure
Git repository with

	├── .gitignore
	├── README.md
	├── LICENSE.md
	├── config
	│   └── config.yaml
	├── workflow
	│   ├── envs
	│   │   ├── env1.yaml
	│   │   └── env2.yaml
	│   ├── report
	│   │   ├── fig1.rst
	│   │   ├── fig2.rst
	│   │   └── workflow.rst
	│   ├── rules
	│   │   ├── rules1.smk
	│   │   └── rules2.smk
	│   ├── scripts
	│   │   ├── script1.py
	│   │   └── script2.R
	│   ├── notebooks
	│   │   ├── notebook1.py.ipynb
	│   │   └── notebook2.r.ipynb
	│   └── Snakefile
	├── resources
	└── results


## Snakemake property
- Snakemake allows generalizing rules by using named wildcards, you can have multiple wildcards in your file paths, however, to avoid conflicts with other jobs of the same rule, all output files of a rule have to contain exactly the same wildcards.

- Snakemake automatically creates missing directories before jobs are executed

- Snakemake allows to access wildcards in the shell command via the wildcards object that has an attribute with the value for each wildcard

- Snakemake uses the Python format mini language to format shell commands.

- Snakefiles are in principle Python code enhanced by some declarative statements to define workflows

- In the Python script, all properties of the rule like input, output, wildcards, etc. are available as attributes of a global snakemake object

- In R scripts, an S4 object named snakemake analogous to the Python case above is available and allows access to input and output files and other parameters

- Config files can be written in JSON or YAML

- Snakemake workflows are executed in three phases

	- In the initialization phase, the files defining the workflow are parsed and all rules are instantiated.

	- In the DAG phase, the directed acyclic dependency graph of all jobs is built by filling wildcards and matching input files to output files.

	- In the scheduling phase, the DAG of jobs is executed, with jobs started according to the available resources.

- **Input functions** take as single argument a `wildcards` object, that allows to access the wildcards values via attributes (here `wildcards.sample`)

- The flag `--summary` prints a table associating each output file with the rule used to generate it, the creation date and optionally the version of the tool used for creation is provided.

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
`snakemake --cluster qsub --jobs 100`

### Constraining wildcards
the wildcard sample in the output file `"sorted_reads/{sample}.bam"` can be constrained to only allow alphanumeric sample names as `"sorted_reads/{sample,[A-Za-z0-9]+}.bam"`.