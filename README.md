---
output: github_document
---

<!-- README.md is generated from README.Rmd. Please edit that file -->


# speedyseq

The goal of `speedyseq` is to accelerate common operations that are currently
very slow in `phyloseq`: the function `psmelt` (and the plotting functions that
use it) and the taxonomic aggregation functions `tax_glom` and `tip_glom`.

The current version of `speedyseq` reimplements `psmelt()` to be much faster
than `phyloseq`'s current version, and includes copies of `plot_bar()`,
`plot_heatmap()`, and `plot_tree()` so that when called from `speedyseq` the
faster `psmelt()` will be used. It also implements a faster version of
`tax_glom()`. 

**NOTE: `speedyseq`'s `tax_glom` function is currently not behaving as expected
and should not be used in your analysis!**

My aim is for these functions to create output that is identical
to what would be obtained from `phyloseq`; however, I have not tested this
extensively, and offer no gaurantee that these versions won't fail in obvious
or subtle ways. If you have any problems or find any discrepancies from
`phyloseq`'s behavior, please post an issue.

## Installation

Install with `devtools`
```r
# install.packages("devtools")
devtools::install_github("mikemc/speedyseq")
```

## Usage

Method 1: Call `speedyseq` functions explicitly when you want to use
`speedyseq`'s version instead of `phyloseq`:

```r
library(phyloseq)
data(GlobalPatterns)
system.time(
    # Calls phyloseq's psmelt
    df1 <- psmelt(GlobalPatterns) # slow
)
#>    user  system elapsed 
#>  94.152   0.097  94.509
system.time(
    df2 <- speedyseq::psmelt(GlobalPatterns) # fast
)
#>    user  system elapsed 
#>   0.386   0.000   0.388
```

Method 2: Load `speedyseq` after `phyloseq`, which will cause calls to the
overlapping function names to go to `speedyseq` by default:

```r
library(phyloseq)
library(speedyseq)
#> 
#> Attaching package: 'speedyseq'
#> The following objects are masked from 'package:phyloseq':
#> 
#>     plot_bar, plot_heatmap, plot_tree, psmelt, tax_glom
data(GlobalPatterns)
system.time(
    ps1 <- phyloseq::tax_glom(GlobalPatterns, "Genus") # slow
)
#>    user  system elapsed 
#>  57.316   0.103  57.616
system.time(
    # Calls speedyseq's tax_glom
    ps2 <- tax_glom(GlobalPatterns, "Genus") # fast
)
#>    user  system elapsed 
#>   0.291   0.000   0.292
```
