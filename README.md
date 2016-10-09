prcbench
========

[![Travis](https://img.shields.io/travis/takayasaito/prcbench.svg?maxAge=2592000)](https://travis-ci.org/takayasaito/prcbench) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/takayasaito/prcbench?branch=master&svg=true)](https://ci.appveyor.com/project/takayasaito/prcbench) [![codecov.io](https://codecov.io/github/takayasaito/prcbench/coverage.svg?branch=master)](https://codecov.io/github/takayasaito/prcbench?branch=master) [![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/prcbench)](https://cran.r-project.org/package=prcbench)

The aim of the `prcbench` package is to provide a testing workbench for evaluating precision-recall curves under various conditions. It contains integrated interfaces for the following five tools. It also contains predefined test data sets.

| Tool          | Link                                                                                               |
|---------------|----------------------------------------------------------------------------------------------------|
| ROCR          | [Tool web site](http://rocr.bioinf.mpi-sb.mpg.de), [CRAN](https://cran.r-project.org/package=ROCR) |
| AUCCalculator | [Tool web site](http://mark.goadrich.com/programs/AUC)                                             |
| PerfMeas      | [CRAN](https://cran.r-project.org/package=PerfMeas)                                                |
| PRROC         | [CRAN](https://cran.r-project.org/package=PRROC)                                                   |
| precrec       | [CRAN](https://cran.r-project.org/package=precrec)                                                 |

Dependencies
------------

### Java

`AUCCalculator` requires a Java runtime (&gt;= 6).

### Bioconductor libraries

`PerfMeas` requires Bioconductor libraries. To automatically install the dependencies, add a Bioconductor repository to the repository list as:

``` r
## Include a Bioconductor repository
setRepositories(ind = 1:2)
```

Installation
------------

-   Install the release version of `prcbench` from CRAN with `install.packages("prcbench")`.

-   Alternatively, you can install a development version of `prcbench` from [our GitHub repository](https://github.com/takayasaito/prcbench). To install it:

    1.  Make sure you have a working development environment.
        -   **Windows**: Install Rtools (available on the CRAN website).
        -   **Mac**: Install Xcode from the Mac App Store.
        -   **Linux**: Install a compiler and various development libraries (details vary across different flavors of Linux).

    2.  Install `devtools` from CRAN with `install.packages("devtools")`.

    3.  Install `prcbench` from the GitHub repository with `devtools::install_github("takayasaito/prcbench")`.

Potential installation issues
-----------------------------

### Bioconductor libraries

You can manually install the dependencies from Bioconductor if `install.packages` fails to access the Bioconductor repository.

``` r
## try http:// if https:// URLs are not supported
source("https://bioconductor.org/biocLite.R")
biocLite("limma")
biocLite("graph")
biocLite("RBGL")
```

### rJava

Some OSs require further configuration for rJava.

Use:

``` r
Sys.setenv(JAVA_HOME = "<path to JRE>")
```

or

    #!/bin/bash

    export JAVA_HOME = "<path to JRE>"
    R CMD javareconf

### microbenchmark

[microbenchmark](https://cran.r-project.org/package=microbenchmark) does not work on some OSs. `prcbench` uses `system.time` when `microbenchmark` is not available.

Documentation
-------------

-   [Introduction to prcbench](https://cran.r-project.org/web/packages/prcbench/vignettes/introduction.html) - a package vignette that contains the descriptions of the functions with several useful examples. Click the link above or view the vignette with `vignette("introduction", package = "prcbench")` in R.

-   Help pages - all the main functions have their own help pages with examples. CRAN provides a pdf version of all combined help files as a reference manual ([pdf](https://cran.r-project.org/web/packages/prcbench/prcbench.pdf)).

Examples
--------

Following two examples show the basic usage of `prcbench` functions.

### Benchmarking

The `run_benchmark` function outputs the result of [microbenchmark](https://cran.r-project.org/package=microbenchmark) for specified tools.

``` r
## Load library
library(prcbench)

## Run microbenchmark for aut5 on b10
testset <- create_testset("bench", "b10")
toolset <- create_toolset(set_names = "auc5")
res <- run_benchmark(testset, toolset)

## Use knitr::kable to show the result in a table format
knitr::kable(res$tab, digits = 2)
```

| testset | toolset | toolname      |   min|    lq|   mean|  median|    uq|     max|  neval|
|:--------|:--------|:--------------|-----:|-----:|------:|-------:|-----:|-------:|------:|
| b10     | auc5    | ROCR          |  2.92|  3.19|  33.77|    3.20|  3.33|  156.20|      5|
| b10     | auc5    | AUCCalculator |  2.22|  3.39|   7.36|    3.40|  6.22|   21.59|      5|
| b10     | auc5    | PerfMeas      |  0.15|  0.16|  96.94|    0.17|  0.19|  484.02|      5|
| b10     | auc5    | PRROC         |  3.25|  3.26|   5.26|    3.82|  4.10|   11.88|      5|
| b10     | auc5    | precrec       |  8.35|  8.45|  14.99|    8.57|  9.07|   40.49|      5|

### Evaluation of precision-recall curves

The `run_evalcurve` function evaluates precision-recall curves with predefined test datasets. The `autoplot` shows a plot with the result of the `run_evalcurve` function.

``` r
## ggplot2 is necessary to use autoplot
library(ggplot2)

## Plot base points and the result of precrec on c1, c2, and c3 test sets
testset <- create_testset("curve", c("c1", "c2", "c3"))
toolset <- create_toolset("precrec")
scores1 <- run_evalcurve(testset, toolset)
autoplot(scores1)
```

![](https://rawgit.com/takayasaito/prcbench/master/README_files/figure-markdown_github/unnamed-chunk-5-1.png)

``` r
## Plot the results of PerfMeas and PRROC on c1, c2, and c3 test sets
toolset <- create_toolset(c("PerfMeas", "PRROC"))
scores2 <- run_evalcurve(testset, toolset)
autoplot(scores2, base_plot = FALSE)
```

![](https://rawgit.com/takayasaito/prcbench/master/README_files/figure-markdown_github/unnamed-chunk-5-2.png)

Citation
--------

*Precrec: fast and accurate precision-recall and ROC curve calculations in R*

Takaya Saito; Marc Rehmsmeier

Bioinformatics 2016;

doi: [10.1093/bioinformatics/btw570](http://dx.doi.org/10.1093/bioinformatics/btw570)

External links
--------------

-   [Classifier evaluation with imbalanced datasets](https://classeval.wordpress.com/) - our web site that contains several pages with useful tips for performance evaluation on binary classifiers.

-   [The Precision-Recall Plot Is More Informative than the ROC Plot When Evaluating Binary Classifiers on Imbalanced Datasets](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0118432) - our paper that summarized potential pitfalls of ROC plots with imbalanced datasets and advantages of using precision-recall plots instead.
