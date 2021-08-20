# Detailed Workflow

## Introduction

Team Genomics-one-A agreed to work on the exome sequencing data for diagnosing a genetic disease named ‘Osteopetrosis’. Exome sequencing is the sequencing of the transcribed parts of the genome. In our project, we have analyzed exome sequencing data from a family trio, in which the boy child was affected with Osteopetrosis but his parents were unaffected. The ultimate goal of the project is to identify the genetic variation that is responsible for the disease.

## Data preparation
```
- Import the original sequencing reads datasets of the family trio from ‘Zenodo’. 
- Check if the datatypes were assigned correctly in the format, fastqsanger.gz 
- Rename the datasets and add tags (#father, #mother, #proband) to six of the datasets accordingly. 
- Obtain the ‘hg19’ version of the human chromosome 8 sequence as the reference genome.
- Ensure the data types are specified in fasta format.

```

## Quality control
```
Run the FastQC tool on six of the fastq datasets with the following parameter:

    - “Short read data from your current history”: all 6 FASTQ datasets selected with Multiple datasets
    - Use the MultiQC tool by setting the following parameter to aggregate the raw FastQC data of all input datasets into one report:
      . In “Results”
      . “Which tool was used generate logs?”: FastQC
      . In “FastQC output”
      . “Type of FastQC output?”: Raw data
      . “FastQC output”: all six RawData outputs of FastQC 
     - Inspect the webpage output produced by the tool to check if trimming/filtering is necessary before mapping the reads.
```

## Read Mapping

```
- Use the Map with BWA-MEM tool to map the reads from the ‘Father’, ‘Mother’, and the ‘Proband’ samples to the reference genome, respectively.
  
   Set the following parameters:
      . “Will you select a reference genome from your history or use a built-in index?”: Use a built-in genome index
      . “Using reference genome”: Human:hg19
      . “Single or Paired-end reads”: Paired
      . “Select first set of reads”: the forward reads (R1) dataset of the father sample
      . “Select second set of reads”: the reverse reads (R2) dataset of the father sample
      . “Set read groups information?”: Set read groups (SAM/BAM specification)
      . “Auto-assign”: No
      . “Read group identifier (ID)”: 000
      . “Auto-assign”: No
      . “Read group sample name (SM)”: father
      
    Perform the read mapping for ‘Mother’ and ‘Proband’ samples with the same parameters mentioned in the previous step with the following changes:
       
       Mother Sample:
      . “Will you select a reference genome from your history or use a built-in index?”: Use a built-in genome index
      . “Using reference genome”: Human:hg19
      . “Single or Paired-end reads”: Paired
      . “Select first set of reads”: the forward reads (R1) dataset of the father sample
      . “Select second set of reads”: the reverse reads (R2) dataset of the father sample
      . “Set read groups information?”: Set read groups (SAM/BAM specification)
      . “Auto-assign”: No
      . “Read group identifier (ID)”: 001
      . “Auto-assign”: No
      . “Read group sample name (SM)”: mother
      
       Proband Sample:
      . “Will you select a reference genome from your history or use a built-in index?”: Use a built-in genome index
      . “Using reference genome”: Human:hg19
      . “Single or Paired-end reads”: Paired
      . “Select first set of reads”: the forward reads (R1) dataset of the father sample
      . “Select second set of reads”: the reverse reads (R2) dataset of the father sample
      . “Set read groups information?”: Set read groups (SAM/BAM specification)
      . “Auto-assign”: No
      . “Read group identifier (ID)”: 002
      . “Auto-assign”: No
      . “Read group sample name (SM)”: proband
      
  ```
      
## Filtering Mapped reads

```
Filter the mapped reads by selecting the tool, Filter SAM or BAM, output SAM or BAM, and set the following parameters:

  - “SAM or BAM file to filter”: all 3 mapped reads datasets of the family trio, outputs of Map with BWA-MEM tool
  - “Filter on bitwise flag”: yes
  - “Only output alignments with all of these flag bits set”: Do not select anything here!
  - “Skip alignments with any of these flag bits set”:
         ✅ “The read is unmapped”
         ✅ “The mate is unmapped”
  - Ensure if three new datasets are produced with one for each of the samples.

```
## Removing duplicate reads

```
- Select RmDup tool and set the following parameters:
     . “BAM file”: all 3 filtered reads datasets; the outputs of Filter SAM or BAM
     . “Is this paired-end or single end data”: BAM is paired end
     . “Treat as single-end”: No
- Ensure if three more new datasets are produced after this step.

```
## Variant Calling 
  ### ⨀ Generating FreeBayes Calls
  
  ```
 - Select the FreeBayes tool and set the parameters:
     . “Choose the source for the reference genome”:
     . “Run in batch mode?”: Merge output VCFs
     . “BAM dataset(s)”:
     . “Using reference genome”: Human: hg19
     . “Limit variant calling to a set of regions?”: Do not limit
     . “Choose parameter selection level”: 1. Simple diploid calling
 - Inspect the VCF outputs produced by FreeBayes.

```
 ### ⨀ Post-processing of FreeBayes
 
 ```
- Use the bcftools norm tool and set the following parameters:
     . “VCF/BCF Data”: the VCF output of FreeBayes tool
     . “Choose the source for the reference genome”: Use a built-in genome
     . “Reference genome”: Human: hg19
     . “When any REF allele does not match the reference genome base”: Ignore the problem (-w)
     . “Left-align and normalize indels?”: Yes
     . “Perform deduplication for the following types of variant records”: do not deduplicate any records.
     . “~multiallelics”: split multiallelic sites into biallelic records (-)
     . “split the following variant types”: both
     . “output_type”: uncompressed VCF
- Look out for the output listing the total number of variant lines processed, along with the number of splits, realigned, and skipped records.
```

