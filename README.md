---
title: "gINTomics vignette"
author:
- name: Angelo Velle
  affiliation: 
  - &id Biology Department, University of Padova, Italy
  email: angelo.velle@phd.unipd.it
- name: Chiara Romualdi
  affiliation: 
  - &id Biology Department, University of Padova, Italy
  email: chiara.romualdi@unipd.it
abstract: >
  gINTomics is an R package for Multi-Omics data integration and visualization. gINTomics is designed to detect the association between the expression of a target and of its regulators, taking into account also their genomics modifications such as Copy Number Variations (CNV) and methylation. For RNA sequencing data, the counts will be fitted using a negative binomial model, while in the case of microarray or other types of data, a linear model will be applied. In some cases the number of regulators for a given target could be very high, in order to handle this eventuality, we provide a penalized linear model that will automatically keep only the most important regulators. All the models are gene-specific, so each gene/miRNA will have its own model with its covariates. The package will automatically download information about TF-target couples (dorothea), miRNA-target couples (multiMiR) and TF-miRNA couples (TransmiR). The couples will be used to define the covariates used in the integration models.
output: 
  BiocStyle::html_document:
      toc: true
      toc_float:
          collapsed: true
vignette: >
  %\VignetteIndexEntry{gINTomics vignette}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
date: 18 luglio 2023
link-citations: true
---

# Installation
To install this package:


`devtools::install_github("angelovelle96/gINTomics")`


# How to use gINTomics

In the following section, we use an example MultiAssayExperiment of ovarian cancer to show how to use `gINTomics` with a standard workflow. The object contains all the available input data types: Gene expression data, miRNA expression data, gene methylation data, gene Copy Number Variations and miRNA Copy Number Variations.

## Generate a MultiAssayExperiment

The package contains a pre built MultiAssayExperiment, anyway in this section we will divide it in signle omics matrices and see how to build a proper MultiAssayExperiment from zero.

# loading packages
`library(gINTomics)`
`library(MultiAssayExperiment)`
`data("ov_test_tcga_omics")`


Here we will extract the single omics from the MultiAssayExperiment

`gene_exp_matrix <- as.matrix(assay(mmultiassay_ov[["gene_exp"]]))`
`miRNA_exp_matrix <- as.matrix(assay(mmultiassay_ov[["miRNA_exp"]]))`
`meth_matrix <- as.matrix(assay(mmultiassay_ov[["methylation"]]))`
`gene_cnv_matrix <- as.matrix(assay(mmultiassay_ov[["cnv_data"]]))`
`miRNA_cnv_matrix <- as.matrix(assay(mmultiassay_ov[["miRNA_cnv_data"]]))`


Now let's build a proper MultiAssayExperiment starting from single matrices


`new_multiassay <- create_multiassay(methylation = meth_matrix, 
                                    gene_exp = gene_exp_matrix,
                                    cnv_data = gene_cnv_matrix,
                                    miRNA_exp = miRNA_exp_matrix,
                                    miRNA_cnv_data = miRNA_cnv_matrix)`





## Run CNV integration

In this section we will see how to perform an expression~CNV integration. The input data (for both expression and CNV) should be provided as matrices or data.frames. Rows represent samples, while each column represents the different response variables/covariates of the models. Expression data could be both normalized or not normalized, the function is able to normalize data by setting the `normalize` argument to TRUE (default). If you want you can specify custom interaction through the `interactions` argument otherwise  the function will automatically look for the genes of `expression` in `cnv_data`.

`gene_cnv_integration <- run_cnv_integration(expression = t(gene_exp_matrix),
                                            cnv_data = t(gene_cnv_matrix))`






