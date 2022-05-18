# makeParams: Creates a parameter object of arguments to be used with other BEAT functions.

## Description

Creates a parameter object of arguments to be used with other BEAT functions.

## Usage

```
makeParams(localpath = getwd(), sampNames, convrates, is.reference, pminus = 0.2, regionSize = 10000, minCounts = 5, verbose = TRUE, computeRegions = TRUE, computeMatrices = TRUE, writeEpicallMatrix = TRUE)
```

## Arguments

localpath

Full path to working directory from which files are read and where results are saved.

sampNames

Vector of sample names to be analyzed.

convrates

Vector of empirically determined bisulfite conversion rates per sample. Determines p+, the model parameter for incomplete conversion (false negative rates).

is.reference

Vector of reference (TRUE) vs. single-cell (FALSE) status per sample.

pminus

Model parameter for false conversion (false positive rate).

regionSize

Region size in nucleotides into which genomic sites are grouped.

minCounts

Minimum counts necessary for each region to be included in epimutation modeling and analysis.

verbose

Shows more verbose console output during computation steps.

computeRegions

If set to TRUE, regions will be recomputed from individual positions and saved as cpgregions.RData objects for each sample.

computeMatrices

If set to TRUE, model parameters will be recomputed and saved as results.RData objects for each sample.

writeEpicallMatrix

If set to TRUE, epimutation calls will be written as RData object.

## Value

The function `makeParams` returns :paramsParameter object to be used in other BEAT functions.

## Examples

```
# Local working directory
localpath <- system.file('extdata', package = 'BEAT')
# Names of samples, expected filenames are e.g. reference.positions.csv
sampNames <- c("reference", "sample")
# Empirical BS-conversion rates, e.g. estimated from non-CpG methylation
convrates <- c(0.8,0.5)
# Vector denoting reference vs. single-cell status of given samples
is.reference <- c(TRUE,FALSE)
params <- makeParams(localpath, sampNames, convrates, is.reference, pminus = 0.2, regionSize = 10000, minCounts = 5, verbose = TRUE, computeRegions = TRUE, computeMatrices = TRUE, writeEpicallMatrix = TRUE)
# Example usage of the params object
positions_to_regions(params)
```