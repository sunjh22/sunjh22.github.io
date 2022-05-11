---
title:  "Common useful linux tricks"
date:   2022-04-26
categories: [Linux]
render_with_liquid: false
---

Some useful bioinformatics tricks that I always forgot when in need.

## sort a bed file according to chromosome and start position
First three columns are chromosome, start and end
	
```bash
$ sort -Vk 1 -k 2,3n demo.bed
```
	
## compare two files, show comman lines
	
```bash
$ comm -12 file1 file2
```

## Convert text between uppercase and lowercase
[A good reference](https://www.networkworld.com/article/3529409/converting-between-uppercase-and-lowercase-on-the-linux-command-line.html)

Converting all letters

```bash
$ echo "Hello There" | tr [:lower:] [:upper:]
$ echo "Hello There" | tr a-z A-Z
$ echo "Hello There" | awk '{print toupper($0)}'
$ echo $dept | sed 's/[a-z]/\U&/g'
```

Capitalizing first letters only

```bash
$ echo design \& engineering| sed -e "s/\b\(.\)/\u\1/g"
```

Making sure only first letters are uppercase
		
```bash
$ echo -n "design & engineering" | python3 -c "import sys; print(sys.stdin.read().title())"
```

 