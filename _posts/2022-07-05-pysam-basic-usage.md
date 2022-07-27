---
title:  "pysam basic usage"
date:   2022-07-05
render_with_liquid: false
---

[Original source](https://pysam.readthedocs.io/en/latest/usage.html)

Several useful usages

```python
import pysam

# open a BAM file
samfile = pysam.AlignmentFile('file.bam', 'rb')

# fetch reads mapped to a region
for x in samfile.fetch('chr1', 10, 20):
	print(str(x))

# pileup - single basecount in the region
for x in samfile.pileup('chr1', 10, 20):
	print(str(x))

# use samtools commands with python
pysam.sort('-o', 'output.bam', 'in.bam')
# which is same as `samtools sort -o output.bam in.bam`

# work with tabix file, like bgzip compressed and tabix indexed gtf file
tabixFile = pysam.TabixFile('in.gtf.gz')
for gtf in tabixFile.fetch(reference='1', start=100000, end=110000, parser=pysam.asGTF()):
    print(gtf.contig, gtf.start, gtf.end, gtf.gene_id)

# work with VCF/BCF
from pysam import VariantFile
bcfFile = VariantFile('in.bcf')
for x in bcfFile.fetch('chr1', 1000, 2000):
	print(x.contig, x.pos, x.ref)
```