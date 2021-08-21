<h1 align="center"> ✨HackBio Internship✨ </h1>
 
 ![Visitor](https://visitor-badge.laobi.icu/badge?page_id=https://github.com/qamrq/team_genomics_one_a.git)

 ![image](https://media-exp1.licdn.com/dms/image/C561BAQHKcVQGbcedOA/company-background_10000/0/1598491473588?e=2159024400&v=beta&t=rxECjvQ_YSc28Dn0n9YOtDoFFmvXjatRiqc__C2mpU0)
 <br>
 <br>

 
## <img src= "https://raw.githubusercontent.com/MartinHeinz/MartinHeinz/master/wave.gif" width="30px"> Genomics One-A Lab 👩‍🔬 👨‍🔬 
Welcome to our Genomics Lab where we do everything with genes🧬. This is a team of diverse, dynamic and Genomic enthusiasts working together towards a common goal to work for the society by practicing Science :microscope:

<p align="center">
 <br>
 <img align="center" src="https://www.nursingcenter.com/getattachment/d8003813-38ac-4105-a4c7-bd3e4676e748/Your-Health-in-the-Hands-of-Genetics-and-Genomics.aspx"  width="400px">
<br>
	<br>
	</p>
	

## Stage 2 Task 🧐🧐
- Reproduce a bioinformatics tutorial that correlates with our biostack (Genomics) 
- Advertise a quick and simple design of our desired tutorial on #transfer-market channel on Slack workspace 
- Create a comprehensive markdown of the steps followed 

Exciting!😍

<br>

## Project Title: "Diagnosis of Osteopetrosis using  Exome Sequencing Data Analysis" :computer:
Have a look at the New state-of-the-art molecular diagnostic genetic test-"Exome Sequencing"

There are around 180,000 exons in humans, with a total length of approximately 30 million base pairs (30 Mb). Thus, while accounting for only 1% of the human genome, the exome is thought to include up to 85% of all disease-causing mutations.

Exome sequencing, as an alternative to whole-genome sequencing in the detection of genetic disease, is less expensive while yet covering significantly more potential disease-causing variant sites than genotyping arrays. This is especially important in the case of rare genetic conditions, since the causal variations may present in the human population at too low a frequency to be included on genotyping arrays.

Our Lab of Genomics-One-A will investigate :detective: exome sequencing data from a family triple:family_man_woman_boy: in which the boy child has osteopetrosis (rare genetic disorder with abnormally dense bones:bone: and prone to breakage) and both parents, who are consanguineous, are unaffected. Our lab’s goal is to figure out which genetic variant is causing the condition.

<br>

## Workflow ✍️
Step 1: Retrieve sequenced reads of father, mother and proband  in fastq format and import the datasets into the https://usegalaxy.org/ or https://usegalaxy.eu/

Step 2: Perform quality check using FastQC tool and aggregate all the FastQC results into single report using MultiQC tool.

Step 3: Perform adapter trimming using Trimmomatic tool on fastq datasets.

Step 4: Perform read mapping using Map with BWA-MEM tool to generate BAM file.

Step 5: Perform mapped reads post-processing using Filter SAM or BAM, output SAM or BAM tool and RmDup tool to filter low quality mapped reads and remove duplicate reads respectively. 

Step 6: Perform variant calling step using FreeBayes tools to generate VCF file containing SNPs, MNPs and Indels.

Step 7: Perform FreeBayes post-processing using bcftool norm to normalize indels and split multiallelic sites into multiple rows.

Step 8: Perform variant annotation using SnpEff eff tool and SnpSift tool.

Step 9: Perform the combined variant extraction/annotation/storage step using GEMINI load tool.

Step 10: Perform candidate variant detection using GEMINI inheritance pattern tool.

### Step 1: Data preparation
Retrieve sequenced reads of father, mother and proband  in fastq format from [Zendo](https://zenodo.org/record/3054169) and import the datasets into the https://usegalaxy.org/ or https://usegalaxy.eu/

```
- Check if the datatypes were assigned correctly in the format, fastqsanger.gz 
- Rename the datasets and add tags (#father, #mother, #proband) to six of the datasets accordingly. 
- Obtain the ‘hg19’ version of the human chromosome 8 sequence as the reference genome.
- Ensure the data types are specified in fasta format.

```

### Step 2: Quality control
- Run the **FastQC tool** on six of the fastq datasets with the following parameter:
```
    - “Short read data from your current history”: all 6 FASTQ datasets selected with Multiple datasets
```
- Use the **MultiQC tool** by setting the following parameter to aggregate the raw FastQC data of all input datasets into one report:

```
     - In “Results”
     - “Which tool was used generate logs?”: FastQC
     - In “FastQC output”
     - “Type of FastQC output?”: Raw data
     - “FastQC output”: all six RawData outputs of FastQC 
     - Inspect the webpage output produced by the tool to check if trimming/filtering is necessary before mapping the reads.
```

### Step 3: Adapter Trimming
- Use **Trimmomatic tool** on the all the fastq datasets to trim adapters and keep the settings By-default and Execute


### Step 4: Read Mapping
- Use the Map with **BWA-MEM tool** to map the reads from the ‘Father’, ‘Mother’, and the ‘Proband’ samples to the reference genome, respectively.
- Set the following parameters:
```
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
```   

- Perform the read mapping for ‘Mother’ and ‘Proband’ samples with the same parameters mentioned in the previous step with the following changes:

```     
       Mother Sample:
      . “Read group identifier (ID)”: 001
      . “Read group sample name (SM)”: mother
      
       Proband Sample:
      . “Read group identifier (ID)”: 002
      . “Read group sample name (SM)”: proband
      
```
      
### Step 5: Mapped Reads Post-processing

#### ⨀ Filtering Mapped reads
- Filter the mapped reads by selecting the tool, **Filter SAM or BAM, output SAM or BAM**, and set the following parameters:

```
  - “SAM or BAM file to filter”: all 3 mapped reads datasets of the family trio, outputs of Map with BWA-MEM tool
  - “Filter on bitwise flag”: yes
  - “Only output alignments with all of these flag bits set”: Do not select anything here!
  - “Skip alignments with any of these flag bits set”:
         ✅ “The read is unmapped”
         ✅ “The mate is unmapped”
  - Ensure if three new datasets are produced with one for each of the samples.

```
#### ⨀ Removing duplicate reads
- Select **RmDup tool** and set the following parameters:
```
   - “BAM file”: all 3 filtered reads datasets; the outputs of Filter SAM or BAM
   - “Is this paired-end or single end data”: BAM is paired end
   - “Treat as single-end”: No
```
Ensure if three more new datasets are produced after this step.


### Step 6: Variant Calling 

#### ⨀ Generating FreeBayes Calls
- Select the **FreeBayes tool** and set the parameters:
```
     . “Choose the source for the reference genome”:
     . “Run in batch mode?”: Merge output VCFs
     . “BAM dataset(s)”:
     . “Using reference genome”: Human: hg19
     . “Limit variant calling to a set of regions?”: Do not limit
     . “Choose parameter selection level”: 1. Simple diploid calling
```
Inspect the VCF outputs produced by FreeBayes.

### Step 7: FreeBayes Post-processing
- Use the **bcftools norm tool** and set the following parameters:
 ```
     . “VCF/BCF Data”: the VCF output of FreeBayes tool
     . “Choose the source for the reference genome”: Use a built-in genome
     . “Reference genome”: Human: hg19
     . “When any REF allele does not match the reference genome base”: Ignore the problem (-w)
     . “Left-align and normalize indels?”: Yes
     . “Perform deduplication for the following types of variant records”: do not deduplicate any records.
     . “~multiallelics”: split multiallelic sites into biallelic records (-)
     . “split the following variant types”: both
     . “output_type”: uncompressed VCF
```
Look out for the output listing the total number of variant lines processed, along with the number of splits, realigned, and skipped records

### Step 8: Variant annotation
- Use the **SnpEff Download** to download genome annotation database  hg19
- Create a PED-formatted pedigree dataset describing the single-family sample trio in the following format:
```
      #family_id    name     paternal_id    maternal_id    sex    phenotype
        FAM         father    0              0              1      1
        FAM         mother    0              0              2      1
        FAM         proband   father         mother         1      2
```
- Use the **SnpEff eff tool** and set the following parameters:
```
    - “Sequence changes (SNPs, MNPs, InDels)”: the output of bcftools norm tool
    - “Input format”: VCF
    - “Output format”: VCF (only if input is VCF)
    - “Genome source”: Locally installed reference genome
    - “Genome”: Homo sapiens: hg19 (or a similarly named option)
    - “Produce Summary Stats”: Yes
```
## Generate GEMINI Database

```
- Use the GEMINI load tool and set the following parameters:
    .“VCF dataset to be loaded in the GEMINI database”: the output of SnpEff eff tool
    .“The variants in this input are”: annotated with snpEff
    .“This input comes with genotype calls for its samples”: Yes
    
    .Sample genotypes were called by Freebayes for us.
    .“Choose a gemini annotation source”: select the latest available annotations snapshot (most likely, there will be only one)
    .“Sample and family information in PED format”: the pedigree file prepared above
    .“Load the following optional content into the database”
        ✅ “GERP scores”
        ✅ “CADD scores”
        ✅“Gene tables”
        ✅“Sample genotypes”
        ✅“variant INFO field”
    .Leave unchecked the following:
        - “Genotype likelihoods (sample PLs)”
        - “only variants that passed all filters”
```

### Candidate Variant Detection

```
- Use the GEMINI inheritance pattern tool and set the following parameters:
    .“GEMINI database”: the GEMINI database of annotated variants; output of GEMINI load tool
    .“Your assumption about the inheritance pattern of the phenotype of interest”: Autosomal recessive
        >“Additional constraints on variants”
        >“Additional constraints expressed in SQL syntax”: impact_severity != 'LOW'
        >“Include hits with less convincing inheritance patterns”: No
        >“Report candidates shared by unaffected samples”: No
- “Family-wise criteria for variant selection”: keep default settings
- In “Output - included information”
    .“Set of columns to include in the variant report table”: Custom (report user-specified columns)
    .“Choose columns to include in the report”:
        >“alternative allele frequency (max_aaf_all)”
    .“Additional columns (comma-separated)”: chrom, start, ref, alt, impact, gene, clinvar_sig, clinvar_disease_name, clinvar_gene_phenotype, rs_ids

```
**For Result & Analysis: [Click Here](https://github.com/662amandeep/Team-Genomics-One-A/blob/main/Results%20%26%20Analysis/Results.md)**

<p align="center">
   <br>
<img align="center" alt="gif" src="https://github.com/662amandeep/Team-Genomics-One-A/blob/main/Images/workflow.gif" width="650">
 <br>
<br>
</p>



## References :scroll:
Wolfgang Maier, Bérénice Batut, Torsten Houwaart, Anika Erxleben, Björn Grüning, 2021 Exome sequencing data analysis for diagnosing a genetic disease (Galaxy Training Materials). https://training.galaxyproject.org/training-material/topics/variant-analysis/tutorials/exome-seq/tutorial.html 

Batut et al., 2018 Community-Driven Data Analysis Training for Biology Cell Systems https://doi.org/10.1016%2Fj.cels.2018.05.012



## Contributors 🤝
Get to know our awesome team members and their contributions 👩‍💻👨‍💻

| Team members	| @Slack username  | Contributions |
| :------------- | :-------------	| :------------- |
| [Aditi Singh](https://github.com/Aditisingh11322)	| @AditiSingh12345  | Validation of Workflow and Retrieved Datasets and uploaded on Galaxy, Performed trimming of reads |
| [Amandeep Kaur (Team Lead)](https://github.com/662amandeep) |@Amandeep  | Conceptualization and validation of the workflow, performed variant annotation using SnpSift, Modified Graphical representation, Modified Github readme file and added all the required files and folders  | 
| [Ananya Saji](https://github.com/AnanyaSaji) |@AnanyaSaji |  Validation of Workflow and Performed FastQC of Father’s dataset |
| [Anjali Negi](https://github.com/genesis234) | @genesis  | Validation of Workflow and Performed FastQC of Mother’s dataset, Worked on Graphical representation  |
| [Darshana Joshi](https://github.com/darshana2509) | @DarshanaJoshi  | Validation of Workflow and Performed variant annotation using GEMINI load and candidate variant detection |
| [Harshitha BC](https://github.com/HarshithaBC) | @Harshitha  | Validation of Workflow and Performed FastQC of Proband’s dataset, Worked on GitHub readme file |
| [Jyothi](https://github.com/jyothi1721)| @Jyothi  | Validation of Workflow and Performed MultiQC of FastQC files  |
| [Lakshmi S Prasad](https://github.com/Lakshmip108) | @lakshmip  | Validation of Workflow and Performed mapping of Father’s datasets  | 
| [Lokesh V](https://github.com/lokegamechanger) | @Lokesh | Validation of Workflow and Performed mapping of Mother’s datasets  |
|[Nanthana Ravichandran](https://github.com/nanthanaravichandran)| @Nanthanaravi   | Validation of Workflow and Performed mapping of Proband’s datasets |
| [Nasrin Parvin](https://github.com/Nasrin0) | @Nasrin |  Validation of Workflow and Filter mapped reads of Father’s dataset |
| [Neha Rose Pious](https://github.com/neharosepious) | @Neharose |  Validation of Workflow and Filter mapped reads of Mother’s dataset |
| [Qamar Raza Qadri](https://github.com/qamrq)| @Qamar | Validation of Workflow and Filter mapped reads of Proband’s dataset, created Github Repo  |
| [Rajarshi Mondal](https://github.com/Rajarshi0)| @Rajarshi_Mondal | Validation of Workflow and Remove duplicates from Father’s mapped reads, Worked on GitHub readme file  |
| [Rohan Kumar](https://github.com/rohanraj11322) | @Rohanraj11 | Validation of Workflow and Remove duplicates from Mother’s and Proband’s mapped reads  |
| [Sooraj S](https://github.com/sooraj14072001) | @Sooraj  | Validation of Workflow and Performed Variant calling using FreeBayes call, Worked on Graphical representation  |
| [Suresh Kumar Mendem](https://github.com/suresh2014) | @Suresh  | Validation of Workflow and Performed Postprocessing FreeBayes call using bcftools, worked on Github readme file |
| [Tasneem](https://github.com/tasneem786) | @Tasneem | Validation of Workflow and Performed variant annotation using SnpEff eff, Worked on Graphical representation |



## Thank you for your Time and Patience :relaxed: 
 <p align="center">
   <br>
    <br>
<img align="center" alt="gif" src="https://github.com/arsentieva/arsentieva/blob/main/code.gif?raw=true" width="500" height="320" />
   <br>
</p>
