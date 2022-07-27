---
title:  "ComplexHeatmap LearingNotes"
date:   2022-07-18
categories: [R, heatmap]
tags: [LearingNotes]
render_with_liquid: false
---

[Tutorial websites](https://jokergoo.github.io/ComplexHeatmap-reference/book/)

When drawing a heatmap, I think following points are important:
1. assign beautiful colors in regard to numerical variables and categorical variables
	1.1 integer number or float number -> discrete color or continuous color
2. clustering
	2.1 self-defined clustering method
	2.2 beautiful dendrograms
	2.3 reorder row or column
	2.4 relabel row or column names
3. heatmap split

3. add row and column annotations, annotation name, bar size, position, text annotations
4. legend positions
5. split cells, add context in cells

# Assign colors
```R
# this is for continuous values
circlize::colorRamp2(breaks, colors)

# for discrete case, directly assign colors to different categories, a named vectors
```

# Clustering
```R
# self-defined clustering method, a function that accept two arguments
robust_dist = function(x, y){

}
Heatmap(test1, clustering_distance_rows = robust_dist)
```

## Comparison between different distancing methods
Euclidean

Pearson

Spearman

Kendall

## Comparison between different clustering methods
ward.D

ward.D2

single

complete

average

median

centroid

## Comparison between different partition methods
k-means

pam

