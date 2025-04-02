# Transcriptomic-Analysis-of-Aortic-Endothelial-cells-Using-the-nf-core-rnaseq-Pipeline-STAR_Salmon-
A step-by-step explanation of running the nf-core/rnaseq pipeline (STAR_Salmon) using Nextflow for transcriptomic analysis of Human Aortic Endothelial cells. Each component of the pipeline including the samplesheet, parameters file, and execution script is broken down.
# Understanding the nf-core/rnaseq Pipeline Execution HAoECs (4 controls vs 4 knockdown samples)

## Introduction
This document provides a detailed explanation of running the `nf-core/rnaseq` pipeline version `3.18.0` using Nextflow. 

## Overview of the Pipeline
The `nf-core/rnaseq` pipeline is an automated workflow designed for RNA-seq analysis. It performs quality control, read alignment, quantification, and other downstream analyses. The pipeline is run using Nextflow, which enables reproducibility and scalability.

---

## 1. The Samplesheet (`Samplesheet_HAoECs.csv`)

The samplesheet is a CSV (Comma-Separated Values) file that contains metadata about the RNA-seq samples. Each row represents a sample, specifying its identifier, file paths for raw sequencing reads, and the strandedness of the library preparation.

### Example Samplesheet:
```csv
sample,fastq_1,fastq_2,strandedness
C1_311,/home/s2451842/HAoECs_raw_fastq/C1_311_1.fq.gz,/home/s2451842/HAoECs_raw_fastq/C1_311_2.fq.gz,auto
C2_212,/home/s2451842/HAoECs_raw_fastq/C2_212_1.fq.gz,/home/s2451842/HAoECs_raw_fastq/C2_212_2.fq.gz,auto
```

### Explanation of Columns:
- `sample`: The unique identifier for the sample.
- `fastq_1`: Path to the first read file (R1) in a paired-end sequencing experiment.
- `fastq_2`: Path to the second read file (R2) in a paired-end sequencing experiment.
- `strandedness`: Defines how the RNA reads are stranded (e.g., `auto`, `forward`, `reverse`). The pipeline determines this automatically when set to `auto`.

---

## 2. The Parameters File (`nfcore_params.yml`)

This YAML file specifies additional parameters for running the pipeline. Using a separate parameters file allows better organization and reproducibility.

### Example Parameters File:
```yaml
fasta: "/datastore/home/genomes/igenomes/Homo_sapiens/UCSC/hg38/Sequence/WholeGenomeFasta/genome.fa"
gtf: "/datastore/home/genomes/igenomes/Homo_sapiens/UCSC/hg38/Annotation/Archives/archive-2015-08-14-08-18-15/Genes/genes.gtf"
aligner: "star_salmon"
skip_biotype_qc: true
```

### Explanation of Parameters:
- `fasta`: Path to the reference genome in FASTA format. This file contains the entire human genome sequence (hg38 in this case).
- `gtf`: Path to the annotation file in GTF format. This file contains gene annotations, providing information about gene locations and features.
- `aligner`: Specifies the alignment method to use. `star_salmon` means that STAR (Spliced Transcripts Alignment to a Reference) is used for alignment, followed by Salmon for transcript quantification.
- `skip_biotype_qc`: If set to `true`, this parameter disables quality control filtering based on gene biotypes.

---

## 3. The Nextflow Execution Script (`nextflow.sh`)

This script contains the command to run the pipeline.

### Example Script:
```bash
NXF_OPTS='-Xms20g -Xmx40g'

nextflow run nf-core/rnaseq \
  --input Samplesheet_HAoECs.csv \
  -params-file nfcore_params.yml \
  -r 3.18.0 \
  -profile singularity \
  -with-dag \
  -with-timeline \
  -with-trace \
  -with-report \
  --outdir results \
  -resume
```

### Breakdown of the Command:

#### 1. Setting Java Memory Options
```bash
NXF_OPTS='-Xms20g -Xmx40g'
```
- `-Xms20g`: Sets the minimum memory allocation to 20GB.
- `-Xmx40g`: Sets the maximum memory allocation to 40GB.
- These options ensure Nextflow runs efficiently by preventing memory shortages.

#### 2. Running the Pipeline
```bash
nextflow run nf-core/rnaseq \
```
- `nextflow run`: Runs a Nextflow pipeline.
- `nf-core/rnaseq`: Specifies the RNA-seq pipeline from the `nf-core` community.

#### 3. Providing the Input Samplesheet
```bash
--input Samplesheet_HAoECs.csv \
```
- Specifies the CSV file containing metadata about the RNA-seq samples.

#### 4. Using the Parameters File
```bash
-params-file nfcore_params.yml \
```
- Loads pipeline parameters from the `nfcore_params.yml` file.

#### 5. Specifying the Pipeline Version
```bash
-r 3.18.0 \
```
- Ensures that the pipeline version `3.18.0` is used, maintaining consistency across runs.

#### 6. Running with Singularity
```bash
-profile singularity \
```
- Specifies the execution environment as `singularity`, a containerization tool that packages all dependencies.

#### 7. Generating Workflow Reports
```bash
-with-dag \
-with-timeline \
-with-trace \
-with-report \
```
- `-with-dag`: Produces a graphical representation of the workflow.
- `-with-timeline`: Generates a timeline of process execution.
- `-with-trace`: Produces a detailed log of pipeline execution.
- `-with-report`: Creates a summary report of the pipeline run.

#### 8. Specifying the Output Directory
```bash
--outdir results \
```
- Stores all results in the `results` directory.

#### 9. Resuming the Workflow
```bash
-resume
```
- If a previous run was interrupted, this flag allows the workflow to continue from where it left off, rather than restarting from scratch.

---

## 4. Summary

### What Happens When You Run This Pipeline?
1. **Nextflow starts** and reads the parameters from the YAML file.
2. **The pipeline checks the input files** to ensure they exist and are correctly formatted.
3. **RNA-seq data is processed**, including:
   - Quality control of raw reads.
   - Alignment of reads to the reference genome using STAR.
   - Transcript quantification using Salmon.
   - Generation of summary reports and QC metrics.
4. **Output files are saved** in the `results` directory.
5. **Reports and logs** are generated for reproducibility and troubleshooting.


