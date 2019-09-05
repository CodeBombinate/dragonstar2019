# AtlasInf

<!-- badges: start -->
[![Travis-CI Build Status](https://travis-ci.org/hadley/babynames.svg?branch=master)](https://travis-ci.org/hadley/babynames)
<!-- badges: end -->

The goal of AtlasInf is to generate vISHs which indecate gene's expression pattern about drosophila embryo. This package contains three datasets provided respectively by websites as follows:

* `reference database`: The reference database comes from the Berkeley Drosophila Transcription Network Project. The in situ expression of 84 genes (columns) is quantified across the 3039 Drosophila embryonic locations (rows) in the file bdtnp.txt for raw data and binarized_bdtnp.csv for binarized data. The order of the rows in both files follows the order of the coordinates in the geometry.txt file. The 84 genes were binarized by manually choosing thresholds for each gene.
  (Source: http://bdtnp.lbl.gov/Fly-Net/)

* `Spatial coordinates`: One half of Drosophila embryo has 3039 cells places as x, y and z (columns) for a total of 3039 embryo locations (rows) and a total of 3039*3 coordinates in the file geometry.txt.
  (Source: https://shiny.mdc-berlin.de/DVEX/)

* `Single cell RNA sequencing`: The single-cell RNA sequencing data is provided as a matrix with 8924 genes as rows and 1297 cells as columns. In the raw version of the matrix, the entries are the raw unique gene counts (quantified by using unique molecular identifiers -- UMI). The normalized version is obtained by dividing each entry by the total number of UMIs for that cell, adding a pseudocount and taking the logarithm of that. All entries are finally multiplied by a constant. The normalized data can be obtained in dge_normalized.txt, the raw data in dge_raw.txt.
  (Source: https://shiny.mdc-berlin.de/DVEX/)

## Installation

You can install the development version of AtlasInf from GitHub with:

``` r
devtools::install_github("hadley/babynames")
```

## Example

This is a basic example which shows you how to use these package:

### Reading the data

The first step is to load datasets needed

``` r
library(AtlasInf)
dge_raw<-system.file("extdata", "dge_raw.txt", package = "AtlasInf")
dge_raw <- read.table(dge_raw, row.names = 1)
bdtnp <-system.file("extdata", "bdtnp.txt", package = "AtlasInf")
bdtnp <- read.table(bdtnp, header = T)
geometry <-system.file("extdata", "geometry.txt", package = "AtlasInf")
geometry <- read.table(geometry, header = T)
```
### Building knn networks and integrating these
``` r
atlasinf<-main(dge_raw,bdtnp,geometry)
```
### Ploting gene expression pattern
``` r
plot_atlas(atlasinf,geometry,"sna")
```
### Evaluating computed atlas with designed metrics
``` r
score<-scoring1(atlasinf,geometry,nrow(bdtnp),6)
print(paste("score evaluated with metric 1:",as.character(score)))
```
### Computing atlas in the method provided in package "DistMap"

``` r
atlas<-distmap_atlas(dge_raw,bdtnp,geometry)
atlas<-((atlas+30.67076)/(34.77176+30.67076))*(0.9107-0.6533)+0.6533
score1<-scoring1(atlas,geometry,nrow(bdtnp),6)
print(paste("score evaluated with metric 1:",as.character(score1)))
```
###
