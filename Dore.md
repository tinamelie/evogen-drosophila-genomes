# RNA-seq and Genome Annotation Workflow

## Project Directory Overview
```text
evo_gen_rna_seq_reads/
├── ext_long_read_assembly/
│   ├── raw_and_QC/                       # external assembly, QUAST, BUSCO
│   ├── polished_and_QC/                  # Polished FASTA + mapping stats
│   ├── repeatmasked_polished/            # RepeatMasker output
│   ├── star_repeatmasked_polished/       # STAR RNA-seq BAMs + logs
│   └── braker_star_repeatmasked_polished/# BRAKER gene predictions
├── evogen_short_reads/
│   ├── raw_reads_and_QC/                 # Evogen Illumina FASTQ + FASTQC
│   └── trimmed_reads_and_QC/             # Trimmomatic outputs + FASTQC
└── evo_gen_rna_seq_reads/
    ├── raw_reads_and_QC/                 # in-house RNA-seq FASTQ + FASTQC
    └── trimmed_reads_and_QC/             # Trimmomatic outputs + FASTQC
```

---

## Inputs

| # | Category | Files | Path |
|---|----------|-------|------|
| 1 | **External long-read assembly** | `GCA_005876975.1_DoreRS1_genomic.fna` | `ext_long_read_assembly/raw_and_QC/` |
| 2 | **Evogen short-read Illumina genomic data** | `C3F0NACXX_PG0409_02A02_H1_L006_R1.fastq.gz`  <br>`C3F0NACXX_PG0409_02A02_H1_L006_R2.fastq.gz` | `evogen_short_reads/raw_reads_and_QC/` |
| 3 | **Evogen in-house RNA-seq data** | `A3_0TLl19_l1_1.fq.gz / _l1_2.fq.gz` <br>`A4_0TP114_l1_1.fq.gz / _l1_2.fq.gz` <br>`B3_0TFl19_l1_1.fq.gz / _l1_2.fq.gz` <br>`B4_0TMl19_l1_1.fq.gz / _l1_2.fq.gz` <br>`Im1611_GCCAAT_L005_R1.fastq.gz / _R2.fastq.gz` | `evogen_rna_seq_reads/raw_reads_and_QC/` |

---

## 1. Genome Assembly QC (External Long-Read Assembly)

<details>
<summary><strong>Commands — QUAST v5.2.0</strong></summary>

```bash
quast.py ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna \
  -o ext_long_read_assembly/raw_and_QC/quast_results/ -t 2
```
</details>

**Key output:** `ext_long_read_assembly/raw_and_QC/quast_results/`  
- [report.pdf](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/quast-report.pdf) 

- report.txt:
```
All statistics are based on contigs of size >= 500 bp, unless otherwise noted (e.g., "# contigs (>= 0 bp)" and "Total length (>= 0 bp)" include all contigs).

Assembly                    GCA_005876975.1_DoreRS1_genomic
# contigs (>= 0 bp)         422                            
# contigs (>= 1000 bp)      422                            
# contigs (>= 5000 bp)      422                            
# contigs (>= 10000 bp)     422                            
# contigs (>= 25000 bp)     419                            
# contigs (>= 50000 bp)     346                            
Total length (>= 0 bp)      182891478                      
Total length (>= 1000 bp)   182891478                      
Total length (>= 5000 bp)   182891478                      
Total length (>= 10000 bp)  182891478                      
Total length (>= 25000 bp)  182828126                      
Total length (>= 50000 bp)  179891206                      
# contigs                   422                            
Largest contig              17040013                       
Total length                182891478                      
GC (%)                      41.21                          
N50                         5273554                        
N90                         107271                         
auN                         6929303.7                      
L50                         8                              
L90                         137                            
# N's per 100 kbp           0.00                           
```
**Notes:**  

---

### 1b. BUSCO v5.8.3 (lineage: drosophila_odb12)

<details>
<summary><strong>Command</strong></summary>

```bash
busco -i ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna \
  -l drosophila_odb12 -m genome -o Dore_BUSCO
```
</details>

**Key output:** `ext_long_read_assembly/raw_and_QC/busco_results_summary/`  
- `busco_results_summary/full_table.tsv`  
- `busco_results_summary/missing_busco_list.tsv`  
- `busco_results_summary/short_summary.txt`

**Results summary:**
```
C:98.4%[S:93.8%,D:4.7%],F:0.1%,M:1.5%,n:9348,E:6.4%
9202    Complete BUSCOs (C)    (of which 592 contain internal stop codons)
8765    Complete and single-copy BUSCOs (S)
437     Complete and duplicated BUSCOs (D)
9       Fragmented BUSCOs (F)
137     Missing BUSCOs (M)
9348    Total BUSCO groups searched     
```

**Notes:** 

BUSCO was run on NERSC after being prohibitively slow on Mac Mini. BUSCO run has not been attempted on Mac Studio.

---

## 2. Read QC (Evogen Short Reads and RNA Reads)

<details>
<summary><strong>FASTQC v0.12.1 — Commands</strong></summary>

```bash
# Genomic short reads
fastqc -o evogen_short_reads/raw_reads_and_QC/FASTQC \
  evogen_short_reads/raw_reads_and_QC/*.fastq.gz

# RNA-seq reads
fastqc -o evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC \
  evo_gen_rna_seq_reads/raw_reads_and_QC/*.fq.gz
```
</details>

**Key output:**  
- Short read QC reports: `evogen_short_reads/raw_reads_and_QC/FASTQC/`  
    - [`C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html)
    - [`C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html)
 
- RNA-seq QC reports: `evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC/`  
    - [`A3_0TLl19_l1_1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A3_0TLl19_l1_1_fastqc.html)
    - [`A3_0TLl19_l1_2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A3_0TLl19_l1_2_fastqc.html)
    - [`A4_0TP114_l1_1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A4_0TP114_l1_1_fastqc.html)
    - [`A4_0TP114_l1_2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A4_0TP114_l1_2_fastqc.html)
    - [`B3_0TFl19_l1_1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B3_0TFl19_l1_1_fastqc.html)
    - [`B3_0TFl19_l1_2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B3_0TFl19_l1_2_fastqc.html)
    - [`B4_0TMl19_l1_1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B4_0TMl19_l1_1_fastqc.html)
    - [`B4_0TMl19_l1_2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B4_0TMl19_l1_2_fastqc.html)
    - [`Im1611_GCCAAT_L005_R1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/Im1611_GCCAAT_L005_R1_fastqc.html)
    - [`Im1611_GCCAAT_L005_R2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/Im1611_GCCAAT_L005_R2_fastqc.html)

**Results:**  

**Notes:**  

mand<Based on QC reports, Trimmomatic parameters were selected, specifically headcrop X. First X reads were consistently noisy, and headcrop x was selected. Sliding window was also maintiained 


---
## 3. Trimming

### 3a. Evogen Short Reads

<details>
<summary><strong>Trimmomatic v0.39</strong></summary>

```bash
trimmomatic PE -phred33 -threads 8 \
  C3F0NACXX_PG0409_02A02_H1_L006_R1.fastq.gz \
  C3F0NACXX_PG0409_02A02_H1_L006_R2.fastq.gz \
  C3F0NACXX_PG0409_02A02_H1_L006_R1_paired.fq.gz \
  C3F0NACXX_PG0409_02A02_H1_L006_R1_unpaired.fq.gz \
  C3F0NACXX_PG0409_02A02_H1_L006_R2_paired.fq.gz \
  C3F0NACXX_PG0409_02A02_H1_L006_R2_unpaired.fq.gz \
  SLIDINGWINDOW:4:30
```
</details>

**Key output:** `evogen_short_reads/trimmed_reads_and_QC/`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R1_paired.fq.gz`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R1_unpaired.fq.gz`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R2_paired.fq.gz`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R2_unpaired.fq.gz`  

**Results summary:**  
```
Input Read Pairs: 176570473
Both Surviving: 153387586 (86.87%)
Forward Only Surviving: 14747991 (8.35%)
Reverse Only Surviving: 2872748 (1.63%)
Dropped: 5562148 (3.15%)
```

**Notes:**
Sliding window as selected REASON

---

### 3b. Trimming: Evogen RNA-Seq Reads

<<details>
<summary><strong>Command</strong></summary>

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
  "A3_0TLl19_l1"
  "A4_0TP114_l1"
  "B3_0TFl19_l1"
  "B4_0TMl19_l1"
  "Im1611_GCCAAT_L005_R"
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

**Output:** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/`  
- `A3_0TLl19_l1_1.trim.paired.fq.gz`  
- `A3_0TLl19_l1_2.trim.paired.fq.gz`  
- `A4_0TP114_l1_1.trim.paired.fq.gz`  
- `A4_0TP114_l1_2.trim.paired.fq.gz`  
- `B3_0TFl19_l1_1.trim.paired.fq.gz`  
- `B3_0TFl19_l1_2.trim.paired.fq.gz`  
- `B4_0TMl19_l1_1.trim.paired.fq.gz`  
- `B4_0TMl19_l1_2.trim.paired.fq.gz`  
- `Im1611_GCCAAT_L005_R1.trim.paired.fq.gz`  
- `Im1611_GCCAAT_L005_R2.trim.paired.fq.gz`  

**Results summary:**

| Sample             | Input Pairs | Paired Surviving | Forward Only | Reverse Only | Dropped | % Surviving |
|--------------------|-------------|------------------|--------------|---------------|---------|-------------|
| A3_0TLl19          | 40,763,583  | 40,762,377       | 0            | 1,206         | 0       | 100.00%     |
| A4_0TP114          | 45,457,493  | 45,456,202       | 0            | 1,291         | 0       | 100.00%     |
| B3_0TFl19          | 38,153,929  | 38,149,395       | 3,810        | 724           | 0       | 99.99%      |
| B4_0TMl19          | 44,019,572  | 44,014,226       | 4,501        | 845           | 0       | 99.99%      |
| Im1611_GCCAAT      | 38,907,278  | 37,145,203       | 1,334,893    | 275,618       | 151,564 | 95.47%      |

**Notes:**  
Adapter file = `custom-adapters.fa` - explain this
Orphaned reads for Im1611

---

## 4. Post-trimming Read QC

<details>
<summary><strong>FASTQC v0.12.1</strong></summary>

```bash
fastqc -o evogen_short_reads/trimmed_reads_and_QC/FASTQC \
       evogen_short_reads/trimmed_reads_and_QC/*_paired.fq.gz

fastqc -o evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC \
       evo_gen_rna_seq_reads/trimmed_reads_and_QC/*_paired.fq.gz
```
</details>

**Key output:**  
- Short read QC reports: `evogen_short_reads/raw_reads_and_QC/FASTQC/`  
  - [`C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html)
  - [`C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html)

- RNA-seq QC reports: `evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC/`  
  - [`A3_0TLl19_l1_1.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A3_0TLl19_l1_1.trim.paired_fastqc.html)  
  - [`A3_0TLl19_l1_2.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A3_0TLl19_l1_2.trim.paired_fastqc.html)  
  - [`A4_0TP114_l1_1.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A4_0TP114_l1_1.trim.paired_fastqc.html)  
  - [`A4_0TP114_l1_2.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/A4_0TP114_l1_2.trim.paired_fastqc.html)  
  - [`B3_0TFl19_l1_1.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B3_0TFl19_l1_1.trim.paired_fastqc.html)  
  - [`B3_0TFl19_l1_2.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B3_0TFl19_l1_2.trim.paired_fastqc.html)  
  - [`B4_0TMl19_l1_1.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B4_0TMl19_l1_1.trim.paired_fastqc.html)  
  - [`B4_0TMl19_l1_2.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/B4_0TMl19_l1_2.trim.paired_fastqc.html)  
  - [`Im1611_GCCAAT_L005_R1.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/Im1611_GCCAAT_L005_R1.trim.paired_fastqc.html)  
  - [`Im1611_GCCAAT_L005_R2.trim.paired_fastqc.html`](https://github.com/tinamelie/evogen-drosophila-genomes/blob/main/reports/Dore/Im1611_GCCAAT_L005_R2.trim.paired_fastqc.html)

**Results:**  

**Notes:**  

---

## 5. Genome Polishing (Illumina + Polypolish)

### 5a. Alignment — bwa-mem2 v2.2.1

<details>
<summary><strong>Commands</strong></summary>

```bash
bwa-mem2 index ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna

bwa-mem2 mem -t 8 -v 3 -a \
  ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna \
  evogen_short_reads/trimmed_reads_and_QC/C3F0NACXX_PG0409_02A02_H1_L006_R1_paired.fq.gz > alignmentsR1.sam

bwa-mem2 mem -t 8 -v 3 -a \
  ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna \
  evogen_short_reads/trimmed_reads_and_QC/C3F0NACXX_PG0409_02A02_H1_L006_R2_paired.fq.gz > alignmentsR2.sam
```
</details>

**Output:**  
- `alignmentsR1.sam FIX THIS`  
- `alignmentsR2.sam FIX THIS`

**Results:**  

**Notes:**  
Note: Paired-end reads were aligned separately as R1 and R2 for compatibility with Polypolish input handling.

---

### 5b. Polishing — Polypolish v0.5.0

<details>
<summary><strong>Command</strong></summary>

```bash
polypolish polish \
  ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna \
  alignmentsR1.sam alignmentsR2.sam \
  > ext_long_read_assembly/polished_and_QC/polished.fasta
```
</details>

**Output:** `ext_long_read_assembly/polished_and_QC/`  
- `GCA_005876975.1_DoreRS1_genomic_polished.fasta`  

- `GCA_005876975.1_DoreRS1_genomic_polished.flagstat`:
```307086568 + 0 in total (QC-passed reads + QC-failed reads)
306775172 + 0 primary
0 + 0 secondary
311396 + 0 supplementary
0 + 0 duplicates
0 + 0 primary duplicates
228784008 + 0 mapped (74.50% : N/A)
228472612 + 0 primary mapped (74.48% : N/A)
306775172 + 0 paired in sequencing
153387586 + 0 read1
153387586 + 0 read2
218148562 + 0 properly paired (71.11% : N/A)
223149292 + 0 with itself and mate mapped
5323320 + 0 singletons (1.74% : N/A)
3188996 + 0 with mate mapped to a different chr
1682655 + 0 with mate mapped to a different chr (mapQ>=5)
```
**Notes:**  

---

## 6. Repeat Masking

<details>
<summary><strong>RepeatMasker v4.2.0 (+ HMMER 3.4)</strong></summary>

```bash
# Repeatmasker commands
RepeatMasker -pa 12 -xsmall -e hmmer -species 7215 \
  ext_long_read_assembly/polished_and_QC/polished.fasta

# Headers cleaned for STAR run
```
</details>

**Output:** `ext_long_read_assembly/repeatmasked_polished/`  
- `GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa`*    
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.out`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.tbl`  

**Results:**  

**Notes:**  
- `-species 7215` specifies *Drosophila* 
- `-pa 12` was used to increase parallelism and reduce runtime, following the message prompt that detected available CPUs.

---

## 7. RNA-seq Alignment

<details>
<summary><strong>STAR v2.7.x — Commands</strong></summary>

```bash
# 7a. Genome index
STAR --runThreadN 9 --runMode genomeGenerate \
     --genomeDir ext_long_read_assembly/star_index \
     --genomeFastaFiles \
       ext_long_read_assembly/repeatmasked_polished/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa \
     --genomeSAindexNbases 12

# 7b. Batch alignment (starloop.sh)
#!/bin/bash
read_pairs=(
  "A3_0TLl19_l1_1.trim.paired.fq A3_0TLl19_l1_2.trim.paired.fq"
  "A4_0TP114_l1_1.trim.paired.fq A4_0TP114_l1_2.trim.paired.fq"
  "B3_0TFl19_l1_1.trim.paired.fq B3_0TFl19_l1_2.trim.paired.fq"
  "B4_0TMl19_l1_1.trim.paired.fq B4_0TMl19_l1_2.trim.paired.fq"
  "Im1611_GCCAAT_L005_R1.trim.paired.fq Im1611_GCCAAT_L005_R2.trim.paired.fq"
)
genome_dir="ext_long_read_assembly/star_index"
out_pref="ext_long_read_assembly/star_repeatmasked_polished/STAR_output_"
for p in "${read_pairs[@]}"; do
  r1=$(echo $p | cut -d' ' -f1); r2=$(echo $p | cut -d' ' -f2)
  STAR --runThreadN 8 --genomeDir "$genome_dir" \
       --readFilesIn "$r1" "$r2" \
       --outFileNamePrefix "${out_pref}$(basename $r1 .trim.paired.fq)_" \
       --outSAMtype BAM SortedByCoordinate
done
```
</details>

**Output:** `ext_long_read_assembly/star_repeatmasked_polished/STAR_output/`  
- `STAR_output_A3_0TLl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output_A4_0TP114_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output_B3_0TFl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output_B4_0TMl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output_Im1611_GCCAAT_L005_R1_Aligned.sortedByCoord.out.bam`

**Results:**  
- Uniquely mapped reads ≈ 88.45 %  
- Splice junctions ≈ 205 000

**Notes:**  

---

## 8. Gene Prediction — BRAKER v2.1.6

<details>
<summary><strong>Command</strong></summary>

```bash
braker.pl \
  --genome=ext_long_read_assembly/repeatmasked_polished/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa \
  --bam=ext_long_read_assembly/star_repeatmasked_polished/STAR_output_*_Aligned.sortedByCoord.out.bam \
  --softmasking \
  --species=Drosophila_orena_RS1 \
  --threads 9 --gff3
```
</details>

**Outputs:** `ext_long_read_assembly/braker_star_repeatmasked_polished/`  
- `braker.gtf`  
- `braker.gff3`  
- `braker.aa`  

**Results:**  

**Notes:**  
