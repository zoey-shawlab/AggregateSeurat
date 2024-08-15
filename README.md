# Pseudobulk Seurat Object 

## Introduction 
Single-cell RNA sequencing (scRNA-seq) is a revolutionary method that captures gene expression profiles from individual cells. By isolating and sequencing RNA from these cells, scRNA-seq uncovers distinct gene expression patterns, enabling researchers to identify and characterize various cell types, track developmental trajectories, and investigate disease mechanisms. However, scRNA-seq data often requires advanced computational bioinformatics to reveal meaningful insights. To address this challenge, pseudobulking aggregates scRNA-seq data into specific groups or conditions, which reduces noise and facilitates better analysis. The Pseudobulk Seurat Shiny application simplifies this process by allowing users to perform pseudobulking on Seurat objects, visualize the aggregated results, and download them for further study.

### Input File 
The input file should be a Seurat object in either .h5seurat or .rds format. An example input file can be found [here]. Once the input file is uploaded, the Seurat object will be displayed as a table in the "Original Seurat Object" tab on the main panel.

### Pseudobulking 
A dropdown selection menu will display the column names from the original Seurat object. Users can then review these columns and choose which ones to use for pseudobulking. The resulting pseudobulked data will be shown in the "Pseudobulked Seurat" tab.

### Output File 
  The application allows the user to download a variety of files based on the pseudobulked seurat object
  - RDS file: Pseudobulked Seurat Object
  - TSV Metadata file: Pseudobulked Metadata from Seurat Object
  - Layers: layers extracted from the RNA assay and exported as separate files as TSV
      - Counts: TSV file contains the raw counts for each gene across the pseudobulked samples
      - Data: TSV file includes normalized gene expression values
      - Scale.data: TSV file provides scaled gene expression values

## Conclusion
The Pseudobulk Seurat Shiny application simplifies the process of aggregating and analyzing single-cell RNA sequencing data by enabling users to perform pseudobulking with ease. This will allow researchers to manage and interpret their scRNA-seq datasets more effectively. 
