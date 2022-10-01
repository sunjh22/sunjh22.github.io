---
title:  "Notes for Bioinfomatics Data Skills"
date:   2022-08-10
tags: [LearningNotes]
render_with_liquid: false
---

## 1. How to learn bioinformatics

1. Assert about assumptions: Python's `assert()` and R's `stopifnot()`
2. Develop frequently used scripts into tools

## 2. Setting up and managing a bioinformatics project

1. Create a well-organized directory structure
2. Document your project in detail: command lines, data source, tool version

## 3. Remedial unix shell
1. Use `tail -f` to monitor redirected standard error
2. Use `tee` to store standard output stream to an intermediate file
3. Use `fg %<job id>` to return program to foreground
4. Use `bg %<job id>` to put a suspended job into background and run
5. Use operator `&&` to chain two commands in which the later one is dependent on the first one
6. Command substitution, use `mkdir reuslts-$(date +%F)` to create a directory with current date as name

## 4. Working with remote machines

## 5. Git

## 6. Bioinformatics data
1. Use powerful `wget --accept --recursive --no-parent --limit-rate -O`
2. Use `curl -O` to download redirected content
3. Use `rsync -avz -e ssh source destination` to transfer entire directory to remote machine, archive, compressed
4. Use `diff -u file1 file2` to find differnece between two small files
5. Use `gunzip -c file.txt.gz > file.txt` to keep original compressed file
6. Use `zgrep, zcat, zless`
7. The most important step in documenting your work is that you’re consistent and make it a habit.

## 7. Unix data tools
1. `(head -n 2; tail -n 2)`
2. Keep in mind `wc -l` is not robust in some cases
3. Use `column -t` to neat output, default delimiter is `\t`, can be changed by `-s`
4. `-E` turn on `grep`'s extended regular expression, aliasd to `egrep`
5. Deal with Non-ASCII files, `alias nonascii="LC_CTYPE=C grep --color='auto' -n -P '[\x80-\xFF]'"`
6. `sort -s` makes sort stable (turns off last-resort sorting)
7. `sort -c` check sorting status
8. `sort -k1,1V` understands numbers inside strings
9. `uniq -d` outputs duplicated lines
10. Use `join -1 column1 -2 column2 (-a 1) file1 file2` to join two files by specific column
11. `awk -F"," -v OFS="\t" {}`
12. associative array in awk works like dictionaries in Python
13. `bioawk -c hdr` is powerful for processing long-column with header files
14. `sed` for capturing text between delimiters
15. Use subshell to combine two sequential commands’ standard output into a single stream. Like
    `(zgrep "^#" gtf.gz; zgrep -v "^#" gtf.gz | sort -Vk1,1 -k4,4n) | gzip > sorted.gtf.gz`
16. `mkfifo namedpipe` to create a named pipe object
17. Process substitution, input to the program `<(...)`, output to the program `>(...)` like `>(gzip > file.txt.gz`

## 8. Introducing R
- factor is class, its type is integer
- Set `colClasses` to `NULL` can force R to skip the column
- Set `nrow` to include several number of rows of a file when using `read.delim()`
- `read.delim()` read string column as factors by default, disable it by set `stringAsFactors=F`
- `comment.char` ignore lines begin with this argument
- `na.strings=c('na', 'Na', 'NA')` set all these values to NA
- Set `drop=F` when subsetting one column from a dataframe and keep it a dataframe
- `which.min(), which.max()`
- `subset(data, conditions)`
- `scale_x_log10(), scale_x_continuous(limits=c(start, end))`
- transparency level `alpha`
- Cleveland’s **Visualizing Data**
- binning data, `cut()` by breaks (how many groups) or by pre-specified ranges
- single column: `geom_density()` - continuous, and `geom_bar()` - discrete
- `match(x, y)` returns the first occurrence of each of x’s values in y
- merge two datasets is comman task in data analysis, use `match()` or `merge()`
- visualize by categories: `facet_wrap(), facet_grid()`, like `table()` for summarizing data
- data structure, data types (integer, double, character, logical, complex, and raw) and class (numeric, factor) are different
- `str()` refers to structure

## 9. Working with Range data
## 10. Working with Sequence data
## 11. Working with alignment data
## 12. Shell scripting, pipelines and parallelizing tasks
## 13. Out-of-memory approaches: Tabix and SQLite