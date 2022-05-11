---
title:  "Make Tutorial"
date:   2022-05-08
categories: [Pipeline]
tags: [Tutorial, Make]
render_with_liquid: false
---

This post is a summary of this awesome [Make tutorial](http://swcarpentry.github.io/make-novice/)

Make is a tool which can run commands to read files, process these files in some way, and write out the processed files.

Make is called a build tool - it builds data files, plots, papers, programs or libraries. It can also update existing files if desired.

Make tracks the dependencies between the files it creates and the files used to create these. If one of the original files (e.g. a data file) is changed, then Make knows to recreate, or update, the files that depend upon this file (e.g. a plot).

## Makefiles
Components: rules, targets, dependencies and actions
```bash
target : dependency
	actions
```

In Makefile, the first target is the default target Make attempts to build.

**phony target** - tell Make always execute this rule no matter the target exists or not.
```bash
.PHONY : clean
clean :
	rm -f *.dat
```

> The order of rebuilding dependencies is arbitrary. You should not assume that they will be built in the order in which they are listed.
Dependencies must form a directed acyclic graph. A target cannot depend on a dependency which itself, or one of its dependencies, depends on that target.
{: .prompt-info}


`up to date` means that the Makefile has a rule with one or more actions whose target is the name of a file (or directory) and the file is up to date.

`Nothing to be done` means that the file exists but either :

- the Makefile has no rule for it, or
- the Makefile has a rule for it, but that rule has no actions

`make -n`, dry run, print the command but not run

## Automatic Variables
- `$@` is a Make automatic variable which means ‘the target of the current rule’. When Make is run it will replace this variable with the target name.

- `$^` is another automatic variable which means ‘all the dependencies of the current rule’. Again, when Make is run it will replace this variable with the dependencies.

- `$<` means ‘the first dependency of the current rule’.

## Dependencies on Data and Code
Dry run: `make` can show the commands it will execute without actually running them if we pass the `-n` flag

Key points

- Make results depend on processing scripts as well as data files.
- Dependencies are transitive: if A depends on B and B depends on C, a change to C will indirectly trigger an update to A.

## Pattern Rules
`%` is a Make wildcard. `$*` is a special variable which gets replaced by the stem with which the rule matched.

> The Make `%` wildcard can only be used in a target and in its dependencies. It cannot be used in actions. In actions, you may however use `$*`, which will be replaced by the stem with which the rule matched.
{: .prompt-info}

## Variables
`$(...)` tells Make to replace a variable with its value when Make is run

We can pull variables out into a new file that just holds variable definitions - `config.mk`

We can then import `config.mk` into `Makefile` using: `include config.mk`

## Functions
`wildcard.` is function that gets a list of files matching some pattern, which we can then save in a variable. For example
```bash
TXT_FILES=$(wildcard books/*.txt)
```

> **@echo** Make prints actions as it executes them. Using @ at the start of an action tells Make not to print this action. So, by using @echo instead of echo, we can see the result of echo (the variable’s value being printed) but not the echo command itself.
{: .prompt-tips}

`patsubst` (‘pattern substitution’) takes a pattern, a replacement string and a list of names in that order; each name in the list that matches the pattern is replaced by the replacement string. Again, we can save the result in a variable. For example
```bash
DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
```

## Self-Documenting Makefiles
Provide a `help` target in Makefiles

We can use `##` (or even `###)` for comments that describe what a rule does and that we want `sed` to detect, for example
```bash
.PHONY : help
help : Makefile
	@sed -n 's/^##//p' $<
```

