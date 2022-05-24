---
title:  "Mamba Usage Notebook"
date:   2022-05-18
categories: [Conda]
tags: [Mamba]
render_with_liquid: false
---

Almost all commands in conda could be replaced by mamba, except activation and deactivation still using conda

## create a environment
`mamba create -n nameofmyenv <list of packages>`

## install further packages
`mamba install otherpkgs`

## install packages through channels
`mamba install -c bioconda samtools`

## remove a environment
`mamba remove -n snakemake-tutorial --all`
