## 1. Genome Assembly QC (External Long-Read Assembly)
### 1a. Assembly metrics
<details>
<summary><strong>QUAST v5.2.0</strong></summary>

```bash
quast.py ext_long_read_assembly/raw_and_QC/GCA_016746235.2_Prin_Dtei_1.1_genomic.fna \
  -o ext_long_read_assembly/raw_and_QC/quast_results/ -t 2
```
</details>

**Key output:** `ext_long_read_assembly/raw_and_QC/quast_results/`  
- [report.pdf](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dtei/quast-report.pdf) 

- report.txt:
```
All statistics are based on contigs of size >= 500 bp, unless otherwise noted (e.g., "# contigs (>= 0 bp)" and "Total length (>= 0 bp)" include all contigs).

Assembly                    GCA_016746235.2_Prin_Dtei_1.1_genomic
# contigs (>= 0 bp)         92                                   
# contigs (>= 1000 bp)      92                                   
# contigs (>= 5000 bp)      90                                   
# contigs (>= 10000 bp)     84                                   
# contigs (>= 25000 bp)     62                                   
# contigs (>= 50000 bp)     47                                   
Total length (>= 0 bp)      149510488                            
Total length (>= 1000 bp)   149510488                            
Total length (>= 5000 bp)   149503360                            
Total length (>= 10000 bp)  149452684                            
Total length (>= 25000 bp)  149084604                            
Total length (>= 50000 bp)  148595768                            
# contigs                   92                                   
Largest contig              32100711                             
Total length                149510488                            
GC (%)                      43.00                                
N50                         24551875                             
N90                         2665119                              
auN                         23994449.7                           
L50                         3                                    
L90                         6                                    
# N's per 100 kbp           2.20                                 
                   
```
**Notes:**  

The QUAST results indicate that this assembly is highly contiguous and of very high quality. All 92 contigs are ≥1 kb, with a total assembled length of ~149.5 Mb, consistent with expectations for this species’ genome size. The N50 is 24.55 Mb, meaning half of the genome is contained in just three contigs (L50 = 3), demonstrating extremely long, uninterrupted sequence regions. The largest contig is ~32.1 Mb. Only six contigs are needed to reach 90% of the genome length (L90 = 6). The GC content is 43.0%, which is within the expected range for the species and suggests no major contamination. The assembly contains very few ambiguous bases (2.20 N’s per 100 kbp), indicating minimal gaps and suggesting a high degree of completeness. It is likely the genome was already polished.

### 1b. Genome completion

<details>
<summary><strong>BUSCO v5.8.3</strong></summary>

```bash
busco -i ext_long_read_assembly/raw_and_QC/GCA_016746235.2_Prin_Dtei_1.1_genomic.fna \
  -l drosophila_odb12 -m genome -o Dtei_BUSCO
```
</details>

**Key output:** `ext_long_read_assembly/raw_and_QC/busco_results_summary/`  
- `busco_results_summary/full_table.tsv`  
- `busco_results_summary/missing_busco_list.tsv`  
- `busco_results_summary/short_summary.txt`

**Results summary:**

```
C:92.3%[S:71.6%,D:20.6%],F:3.0%,M:4.7%,n:9348
8625    Complete BUSCOs (C)
6696    Complete and single-copy BUSCOs (S)
1929    Complete and duplicated BUSCOs (D)
282     Fragmented BUSCOs (F)
441     Missing BUSCOs (M)
9348    Total BUSCO groups searched
```
**Notes:**  

The results show that 92.3% of expected single-copy orthologs were identified in the assembly. Of these, 71.6% were found as single copies, while 20.6% were duplicated, which may indicate uncollapsed haplotypes, segmental duplications, or recent gene family expansions. Only 4.7% of BUSCOs were missing and 3.0% were fragmented, suggesting that the assembly captures the vast majority of the expected gene content for a _Drosophila_ genome, though the high duplication rate may warrant further investigation.


## 2. Read QC (Evogen Short Reads and RNA Reads)

### 2a. Short Read QC  

<details>
<summary><strong>FASTQC v0.12.1</strong></summary>

```bash
# Genomic short reads
fastqc -o evogen_short_reads/raw_reads_and_QC/FASTQC \
  evogen_short_reads/raw_reads_and_QC/*.fastq.gz
```
</details>

**Reports:** `evogen_short_reads/raw_reads_and_QC/FASTQC/`  
- [`C3F0NACXX_PG0409_01A01_H1_L005_R1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/C3F0NACXX_PG0409_01A01_H1_L005_R1_fastqc.html)  
- [`C3F0NACXX_PG0409_01A01_H1_L005_R2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/C3F0NACXX_PG0409_01A01_H1_L005_R2_fastqc.html)

**Notes:**  
The FastQC reports for both R1 and R2 show uniformly high-quality data. Per-base sequence quality is consistently high across the full read length, with no regions of concern. Per-tile sequence quality is uniform, indicating no spatial bias across the flow cell. Base composition and GC content match expectations, and N content is negligible. No adapter contamination or overrepresented sequences were detected. Sequence duplication levels were not flagged and are within normal expectations for this dataset. 


### 2b. RNA-seq Read QC  

<details>
<summary><strong>FASTQC v0.12.1</strong></summary>

```bash
# RNA-seq reads
fastqc -o evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC \
  evo_gen_rna_seq_reads/raw_reads_and_QC/*.fq.gz
```
</details>

**Reports:** `evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC/`  
- [`A1_TOU_Ll17_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A1_TOU_Ll17_l1_1_fastqc.html)  
- [`A1_TOU_Ll17_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A1_TOU_Ll17_l1_2_fastqc.html)  
- [`A2_TOUPl17_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A2_TOUPl17_l1_1_fastqc.html)  
- [`A2_TOUPl17_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A2_TOUPl17_l1_2_fastqc.html)  
- [`B1_TOU_Fl19_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B1_TOU_Fl19_l1_1_fastqc.html)  
- [`B1_TOU_Fl19_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B1_TOU_Fl19_l1_2_fastqc.html)  
- [`B2_TOUM11921_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B2_TOUM11921_l1_1_fastqc.html)  
- [`B2_TOUM11921_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B2_TOUM11921_l1_2_fastqc.html)  
- [`Im1610_ACAGTG_L005_R1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/Im1610_ACAGTG_L005_R1_fastqc.html)  
- [`Im1610_ACAGTG_L005_R2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/Im1610_ACAGTG_L005_R2_fastqc.html)  

Raw read quality across all libraries (R1 and R2) was generally high, with all samples passing “Per base sequence quality” and “Per sequence quality scores.” Several R1 files (A1_TOU_Ll17_l1_1, A2_TOUPl17_l1_1, Im1610_R1) and all other forward reads showed “FAIL” for “Per base sequence content” and “Per sequence GC content,” reflecting bias early in reads and deviation from a normal GC distribution. Many libraries also failed “Sequence Duplication Levels,” suggesting either biological redundancy (e.g., high-expression transcripts or low-complexity regions) or potential over-sequencing. Overrepresented sequences were flagged in some forward reads (A1_TOU_Ll17_l1_1, A2_TOUPl17_l1_1) but were absent in most reverse reads. Adapter contamination was consistently absent across all files. Notably, Im1610_R2 failed “Per tile sequence quality,” indicating localized quality drops on the flow cell, though per-base scores remained high overall. Despite these biases, the data are of sufficient quality for downstream analysis after standard trimming and filtering steps.


**Notes:**  
FastQC results for all libraries showed generally high per-base sequence quality across the full read length, with no per-base quality failures. The most frequent issues were in “Per base sequence content” and “Per sequence GC content,” which failed in all forward reads (A1_TOU_Ll17_l1_1, A2_TOUPl17_l1_1, B1_TOU_Fl19_l1_1, B2_TOUM11921_l1_1, Im1610_R1) and in most reverse reads for GC content. These failures reflect base composition bias in the first portion of the reads and deviations from an ideal GC distribution, patterns common in high-throughput sequencing data. “Sequence Duplication Levels” also failed in multiple libraries, particularly in reverse reads, suggesting either high biological redundancy or over-sequencing.  

Several reverse reads (A1_TOU_Ll17_l1_2, A2_TOUPl17_l1_2, B1_TOU_Fl19_l1_2, B2_TOUM11921_l1_2, Im1610_R2) lacked PASS/WARN/FAIL calls for modules such as “Per tile sequence quality,” “Overrepresented sequences,” or “Adapter content,” which indicates those modules were not triggered rather than passed silently. No adapters were detected in any library, and overrepresented sequences were minimal except in forward reads of A1_TOU_Ll17_l1_1 and A2_TOUPl17_l1_1.  

Overall, while composition and GC content warnings are present, the underlying sequence quality is strong, and the datasets are suitable for downstream analysis after standard trimming to remove biased bases at the start of reads.

**Notes:**  
Overall per-base sequence quality was high across all libraries.  
- **A1_TOU_Ll17 (R1, R2)** – Slight quality drop and base composition bias in the first ~5–10 bp; mild per-tile quality variation in R2.  
- **A2_TOUPl17 (R1, R2)** – Similar first ~5–10 bp quality dip and base composition bias; minor per-tile quality warnings in R2.  
- **B1_TOU_Fl19 (R1, R2)** – GC distribution outside theoretical range; overrepresented sequences detected but mostly rRNA or low-complexity sequences; mild sequence duplication levels.  
- **B2_TOUM11921 (R1, R2)** – Noticeable per-tile quality variation in R2; GC distribution shifted; low-level overrepresented sequences.  
- **Im1610_ACAGTG (R1, R2)** – Slight base composition bias in first bases; R2 had per-tile quality variation; duplication levels modestly elevated but consistent across samples.  

No adapter contamination was detected in any library. Overrepresented sequences were mainly biological in origin. All datasets are suitable for downstream mapping after standard preprocessing.

---

## 3. Trimming

### 3a. Evogen Short Reads

<details>
<summary><strong>Trimmomatic v0.39</strong></summary>

```bash
trimmomatic PE -phred33 -threads 8 \
  C3F0NACXX_PG0409_01A01_H1_L005_R1.fastq.gz \
  C3F0NACXX_PG0409_01A01_H1_L005_R2.fastq.gz \
  C3F0NACXX_PG0409_01A01_H1_L005_R1.trim.paired.fastq.gz \
  C3F0NACXX_PG0409_01A01_H1_L005_R1.trim.unpaired.fastq.gz \
  C3F0NACXX_PG0409_01A01_H1_L005_R2.trim.paired.fastq.gz \
  C3F0NACXX_PG0409_01A01_H1_L005_R2.trim.unpaired.fastq.gz \
  ILLUMINACLIP:custom-adapters.fa:2:30:10 \
  HEADCROP:10 LEADING:3 TRAILING:3 \
  SLIDINGWINDOW:4:15 MINLEN:36
```
</details>

**Key output:** `evogen_short_reads/trimmed_reads_and_QC/`  
- `C3F0NACXX_PG0409_01A01_H1_L005_R1.trim.paired.fq.gz`  
- `C3F0NACXX_PG0409_01A01_H1_L005_R1.trim.unpaired.fq.gz`  
- `C3F0NACXX_PG0409_01A01_H1_L005_R2.trim.paired.fq.gz`  
- `C3F0NACXX_PG0409_01A01_H1_L005_R2.trim.unpaired.fq.gz`
- 

**Results summary:**  
```
Input Read Pairs: 172225062
Both Surviving: 161380883 (93.70%)
Forward Only Surviving: 7650968 (4.44%)
Reverse Only Surviving: 1061761 (0.62%)
Dropped: 2131450 (1.24%)
```

**Notes:**
The short reads were trimmed using a sliding window approach to clean up any low-quality regions near the ends of the reads. The SLIDINGWINDOW:4:15 setting means it looks at each 4-base window and trims once the average quality in a window drops below 15. This is a somewhat strict setting to make sure only high-quality bases are kept – appropriate for polishing purposes rather than assembly.

Trimming worked well—about 93.7% of read pairs survived. A small number of reads were trimmed too short or had quality issues and were dropped or left as single-end. This is normal and expected. The cleaned reads were used for mapping.


### 3b. Evogen RNA-Seq Reads

<details>
<summary><strong>Trimmomatic v0.39</strong></summary>

```bash
#!/bin/bash

threads=8
adapter_file="custom-adapters.fa"

# source / destination directories
raw_dir="evo_gen_rna_seq_reads/raw_reads_and_QC"
out_dir="evo_gen_rna_seq_reads/trimmed_reads_and_QC"
mkdir -p "${out_dir}"

# Base sample identifiers
samples=(
  "A1_TOU_Ll17_l1"
  "A2_TOUPl17_l1"
  "B1_TOU_Fl19_l1"
  "B2_TOUM11921_l1"
  "Im1610_ACAGTG_L005_R"
)

for s in "${samples[@]}"; do
  # Raw read files
  if [[ "$s" == Im1611* ]]; then
    R1="${raw_dir}/${s}1.fastq.gz"
    R2="${raw_dir}/${s}2.fastq.gz"
    base="${s}"
  else
    R1="${raw_dir}/${s}_1.fq.gz"
    R2="${raw_dir}/${s}_2.fq.gz"
    base="${s}"
  fi

  # Output file names
  R1_p="${out_dir}/${base}_1.trim.paired.fq.gz"
  R1_u="${out_dir}/${base}_1.trim.unpaired.fq.gz"
  R2_p="${out_dir}/${base}_2.trim.paired.fq.gz"
  R2_u="${out_dir}/${base}_2.trim.unpaired.fq.gz"

  echo "Trimming ${base}..."
  trimmomatic PE -threads "${threads}" -phred33 \
    "${R1}" "${R2}" \
    "${R1_p}" "${R1_u}" \
    "${R2_p}" "${R2_u}" \
    ILLUMINACLIP:${adapter_file}:2:30:10 \
    HEADCROP:10 LEADING:3 TRAILING:3 \
    SLIDINGWINDOW:4:15 MINLEN:36
done
```
</details>

**Key output:** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/`  
- A1_TOU_Ll17_l1_1.trim.paired.fq.gz
- A1_TOU_Ll17_l1_2.trim.paired.fq.gz
- A2_TOUPl17_l1_1.trim.paired.fq.gz
- A2_TOUPl17_l1_2.trim.paired.fq.gz
- B1_TOU_Fl19_l1_1.trim.paired.fq.gz
- B1_TOU_Fl19_l1_2.trim.paired.fq.gz
- B2_TOUM11921_l1_1.trim.paired.fq.gz
- B2_TOUM11921_l1_2.trim.paired.fq.gz
- Im1610_ACAGTG_L005_R1.trim.paired.fq.gz
- Im1610_ACAGTG_L005_R2.trim.paired.fq.gz

**Results summary:**
# UPDATE THIS
Sample               | Input Pairs | Paired Surviving | Forward Only | Reverse Only | Dropped | % Surviving
---------------------|-------------|------------------|--------------|---------------|---------|-------------
A1_TOU_Ll17_l1       | 0           | 0                | 0            | 0             | 0       | 0.00%
A2_TOUPl17_l1        | 0           | 0                | 0            | 0             | 0       | 0.00%
B1_TOU_Fl19_l1       | 0           | 0                | 0            | 0             | 0       | 0.00%
B2_TOUM11921_l1      | 0           | 0                | 0            | 0             | 0       | 0.00%
Im1610_ACAGTG_L005_R | 0           | 0                | 0            | 0             | 0       | 0.00%

# ADD THESE

**Notes:**  

## 4. Post-trimming Read QC

### 4a. Short Read QC (Post-trimming)

<details>
<summary><strong>FASTQC v0.12.1</strong></summary>

```bash
fastqc -o evogen_short_reads/trimmed_reads_and_QC/FASTQC \
       evogen_short_reads/trimmed_reads_and_QC/*_paired.fq.gz
```
</details>

**Reports:** `evogen_short_reads/trimmed_reads_and_QC/FASTQC/`  
 - [`C3F0NACXX_PG0409_01A01_H1_L005_R1_paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/C3F0NACXX_PG0409_01A01_H1_L005_R1_paired_fastqc.html)  
 - [`C3F0NACXX_PG0409_01A01_H1_L005_R2_paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/C3F0NACXX_PG0409_01A01_H1_L005_R2_paired_fastqc.html)  
# ADD THESE
**Notes**
# ADD THESE

### 4b. RNA-seq Read QC (Post-trimming)

<details>
<summary><strong>FASTQC v0.12.1</strong></summary>

```bash
fastqc -o evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC \
       evo_gen_rna_seq_reads/trimmed_reads_and_QC/*_paired.fq.gz
```
</details>

**Reports:** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC/`  
- [A1_TOU_Ll17_l1_1.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A1_TOU_Ll17_l1_1.trim.paired_fastqc.html)
- [A1_TOU_Ll17_l1_2.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A1_TOU_Ll17_l1_2.trim.paired_fastqc.html)
- [A2_TOUPl17_l1_1.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A2_TOUPl17_l1_1.trim.paired_fastqc.html)
- [A2_TOUPl17_l1_2.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/A2_TOUPl17_l1_2.trim.paired_fastqc.html)
- [B1_TOU_Fl19_l1_1.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B1_TOU_Fl19_l1_1.trim.paired_fastqc.html)
- [B1_TOU_Fl19_l1_2.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B1_TOU_Fl19_l1_2.trim.paired_fastqc.html)
- [B2_TOUM11921_l1_1.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B2_TOUM11921_l1_1.trim.paired_fastqc.html)
- [B2_TOUM11921_l1_2.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/B2_TOUM11921_l1_2.trim.paired_fastqc.html)
- [Im1610_ACAGTG_L005_R1.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/Im1610_ACAGTG_L005_R1.trim.paired_fastqc.html)
- [Im1610_ACAGTG_L005_R2.trim.paired_fastqc.html](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/Im1610_ACAGTG_L005_R2.trim.paired_fastqc.html)

**Notes**
# ADD THESE

## 5. Read mapping and genome polishing (Short reads + External genome)

### 5a. Alignment

<details>
<summary><strong>bwa-mem2 v2.2.1</strong></summary>

```bash
bwa-mem2 index ext_long_read_assembly/raw_and_QC/GCA_016746235.2_Prin_Dtei_1.1_genomic.fna

bwa-mem2 mem -t 8 -v 3 -a \
  ext_long_read_assembly/raw_and_QC/GCA_016746235.2_Prin_Dtei_1.1_genomic.fna \
  evogen_short_reads/trimmed_reads_and_QC/C3F0NACXX_PG0409_01A01_H1_L005_R1_paired.fq.gz > alignmentsR1.sam

bwa-mem2 mem -t 8 -v 3 -a \
  ext_long_read_assembly/raw_and_QC/GCA_016746235.2_Prin_Dtei_1.1_genomic.fna \
  evogen_short_reads/trimmed_reads_and_QC/C3F0NACXX_PG0409_01A01_H1_L005_R2_paired.fq.gz > alignmentsR2.sam
```
</details>

**Output:**  
- `alignmentsR1.sam`  
- `alignmentsR2.sam`

**Notes:**  
Trimmed short reads were aligned to the external genome using bwa-mem2. The genome was indexed first, then R1 and R2 were aligned separately to generate two SAM files for polypolish. These alignments were used directly for polishing.

