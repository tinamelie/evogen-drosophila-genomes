# _Drosophila orena_: Genome polishing and gene prediction using RNA-seq and short reads

**Note:** Click the arrow (▶) next to a program name to expand and view the commands.

## Project Directory Overview
```text

├── ext_long_read_assembly/
│   ├── raw_and_QC/                       # external assembly, QUAST, BUSCO
│   ├── polished_and_QC/                  # Polished FASTA + mapping stats
│   ├── repeatmasked_polished/            # RepeatMasker output
│   ├── star_repeatmasked_polished/       # STAR RNA-seq BAMs + logs
│   └── braker_star_repeatmasked_polished/# BRAKER gene predictions
├── evogen_short_reads/
│   ├── raw_reads_and_QC/                 # Evogen Illumina FASTQ + FASTQC
│   └── trimmed_reads_and_QC/             # Trimmomatic outputs + FASTQC
└── evogen_rna_seq_reads/
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
### 1a. Assembly metrics
<details>
<summary><strong>QUAST v5.2.0</strong></summary>

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

The QUAST results show that the genome assembly is high quality and highly contiguous. All 422 contigs are ≥10 kb, with a total assembled length of ~183 Mb, consistent with _Drosophila_ genome size expectations. The N50 is 5.3 Mb, meaning half the genome is contained in contigs of that length or longer indicating long, uninterrupted sequence regions. The largest contig is ~17 Mb, and only 8 contigs are needed to reach 50% of the genome length (L50 = 8), which is excellent. The GC content is 41.2%, which is typical for _Drosophila_ and suggests no major contamination. There are zero ambiguous bases (N’s), indicating that the assembly is gap-free and likely already polished.

### 1b. Genome completion

<details>
<summary><strong>BUSCO v5.8.3</strong></summary>

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

- BUSCO was run on NERSC after being prohibitively slow on Mac Mini. BUSCO run has not been attempted on Mac Studio.

- The results show that 98.4% of expected single-copy _Drosophila_ genes were found in the assembly, which means it’s nearly complete. Of those, 93.8% were found once (as expected) and 4.7% were duplicated, possibly due to uncollapsed haplotypes or recent duplications. Only 1.5% of genes were missing, which is very low. This confirms that the assembly captures nearly the full gene content of a typical _Drosophila_ genome.

- lineage: drosophila_odb12
---

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
- [`C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html)  
- [`C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html)

**Notes:**  
The FastQC reports for both R1 and R2 show generally high-quality data. The first ~10 bases in both reads show lower quality and biased base composition, which is typical and was handled by trimming with `HEADCROP:10`. R2 showed a minor “per-tile sequence quality” warning, indicating slight unevenness across the flow cell, but nothing critical. No adapter contamination was detected. Post-trimming reads were clean and suitable for mapping.

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
- [`A3_0TLl19_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A3_0TLl19_l1_1_fastqc.html)  
- [`A3_0TLl19_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A3_0TLl19_l1_2_fastqc.html)  
- [`A4_0TP114_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A4_0TP114_l1_1_fastqc.html)  
- [`A4_0TP114_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A4_0TP114_l1_2_fastqc.html)  
- [`B3_0TFl19_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B3_0TFl19_l1_1_fastqc.html)  
- [`B3_0TFl19_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B3_0TFl19_l1_2_fastqc.html)  
- [`B4_0TMl19_l1_1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B4_0TMl19_l1_1_fastqc.html)  
- [`B4_0TMl19_l1_2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B4_0TMl19_l1_2_fastqc.html)  
- [`Im1611_GCCAAT_L005_R1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/Im1611_GCCAAT_L005_R1_fastqc.html)  
- [`Im1611_GCCAAT_L005_R2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/Im1611_GCCAAT_L005_R2_fastqc.html)  


**Notes:**  
RNA-seq reads showed typical quality patterns, including lower quality and base composition bias at the start of reads. They have high overall quality, with the usual lower scores and base bias in the first ~10 bases. This justified the use of `HEADCROP:10`. No major issues or adapter content was detected, and all samples passed quality thresholds for downstream use.

The sample Im1611_GCCAAT is a bit lower in quality compared to the others. The scores drop off a little earlier, and there’s some extra variation in base composition and tile quality. This is not unusual for RNA-seq and trimming handles most of it. The reads are still usable and were included in downstream processing without problems.


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
The short reads were trimmed using a sliding window approach to clean up any low-quality regions near the ends of the reads. The SLIDINGWINDOW:4:30 setting means it looks at each 4-base window and trims once the average quality in a window drops below 30. This is a somewhat strict setting to make sure only high-quality bases are kept - appropriate for polishing purposes rather than assembly.

Trimming worked well—about 87% of read pairs survived. A small number of reads were trimmed too short or had quality issues and were dropped or left as single-end. This is normal and expected. The cleaned reads were used for mapping.

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

**Key output:** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/`  
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
Trimming was successful for all samples. Most had extremely high survival rates (>99.9%), meaning nearly all read pairs passed filters. The trimming steps removed low-quality bases (especially at the start) and any adapter contamination.

The sample Im1611_GCCAAT had a lower survival rate (~95.5%) and more orphaned reads (reads where one mate passed and the other didn’t). This is expected based on its lower initial quality, but it still retained over 37 million high-quality pairs, which is more than enough for downstream use.

---

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
 - [`C3F0NACXX_PG0409_02A02_H1_L006_R1_paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R1_paired_fastqc.html)  
 - [`C3F0NACXX_PG0409_02A02_H1_L006_R2_paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/C3F0NACXX_PG0409_02A02_H1_L006_R2_paired_fastqc.html)  


**Notes**
Post-trimming quality scores are high across the read length, and the base bias at the start is gone. No adapter contamination is present. GC content and duplication levels are within expected ranges.

### 4b. RNA-seq Read QC (Post-trimming)

<details>
<summary><strong>FASTQC v0.12.1</strong></summary>

```bash
fastqc -o evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC \
       evo_gen_rna_seq_reads/trimmed_reads_and_QC/*_paired.fq.gz
```
</details>

**Reports:** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC/`  
 - [`A3_0TLl19_l1_1.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A3_0TLl19_l1_1.trim.paired_fastqc.html)  
 - [`A3_0TLl19_l1_2.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A3_0TLl19_l1_2.trim.paired_fastqc.html)  
 - [`A4_0TP114_l1_1.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A4_0TP114_l1_1.trim.paired_fastqc.html)  
 - [`A4_0TP114_l1_2.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/A4_0TP114_l1_2.trim.paired_fastqc.html)  
 - [`B3_0TFl19_l1_1.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B3_0TFl19_l1_1.trim.paired_fastqc.html)  
 - [`B3_0TFl19_l1_2.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B3_0TFl19_l1_2.trim.paired_fastqc.html)  
 - [`B4_0TMl19_l1_1.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B4_0TMl19_l1_1.trim.paired_fastqc.html)  
 - [`B4_0TMl19_l1_2.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/B4_0TMl19_l1_2.trim.paired_fastqc.html)  
 - [`Im1611_GCCAAT_L005_R1.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/Im1611_GCCAAT_L005_R1.trim.paired_fastqc.html)  
 - [`Im1611_GCCAAT_L005_R2.trim.paired_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dore/Im1611_GCCAAT_L005_R2.trim.paired_fastqc.html)  


**Notes:**  

All the RNA-seq samples look clean after trimming. No adapter content remains, and the per-base quality scores are good across the full read lengths. The weird base bias at the start of the reads is gone, and overall quality is consistent across samples.

The sample Im1611, which was lower quality before, now looks normal. Trimming clearly helped. Duplication levels and GC content are within expected ranges for all samples. Nothing stands out or looks off. 

---

## 5. Read mapping and genome polishing (Short reads + External genome)

### 5a. Alignment

<details>
<summary><strong>bwa-mem2 v2.2.1</strong></summary>

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
- `alignmentsR1.sam`  
- `alignmentsR2.sam`

**Notes:**  
Trimmed short reads were aligned to the external genome using bwa-mem2. The genome was indexed first, then R1 and R2 were aligned separately to generate two SAM files for polypolish. These alignments were used directly for polishing.

### 5b. Read polishing

<details>
<summary><strong>Polypolish v0.5.0</strong></summary>

```bash
# Draft and polished assemblies
RAW_ASM=ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna
POL_ASM=ext_long_read_assembly/polished_and_QC/GCA_005876975.1_DoreRS1_genomic_polished.fasta
READ1=../evogen_short_reads/C3F0NACXX_PG0409_02A02_H1_L006_R1_paired.fq.gz
READ2=../evogen_short_reads/C3F0NACXX_PG0409_02A02_H1_L006_R2_paired.fq.gz

# Polish the draft assembly
polypolish polish "$RAW_ASM" alignmentsR1.sam alignmentsR2.sam > "$POL_ASM"

# Map reads back to draft and polished assemblies for comparison
for ASM in "$RAW_ASM" "$POL_ASM"; do
  BASE=$(basename "${ASM%.*}")
  bwa-mem2 index "$ASM"
  bwa-mem2 mem -t 16 "$ASM" "$READ1" "$READ2" \
    | samtools sort -@16 -o "${BASE}.bam" -
  samtools flagstat "${BASE}.bam" > "${BASE}.flagstat"
done

**Key output:** `ext_long_read_assembly/polished_and_QC/`  
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
Out of ~307 million reads, about 74.5% mapped to the genome. This is slightly lower than ideal but still usable. The lower mapping rate may be due to remaining contamination, variation between individuals, or repetitive regions that are hard to map. Over 71% of reads were properly paired, which shows that the read quality and trimming were good. About 1.7% of reads mapped as singletons, which is typical. A small fraction mapped across different contigs — this is normal and not a problem.

---

## 6. Repeat masking

<details>
<summary><strong>RepeatMasker v4.2.0 (+ HMMER 3.4)</strong></summary>

```bash
# Repeatmasker commands
RepeatMasker -pa 12 -xsmall -e hmmer -species 7215 \
  ext_long_read_assembly/polished_and_QC/GCA_005876975.1_DoreRS1_genomic_polished.fasta

# Headers cleaned for STAR run
sed 's/ .*//' repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked > ../GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa

```
</details>

**Output:** `ext_long_read_assembly/repeatmasked_polished/`  
- `GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa`    
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.out`  

- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.tbl`:  

```
==================================================
file name: GCA_005876975.1_DoreRS1_genomic_polished.fasta
sequences:           422
total length:  182885124 bp  (182885124 bp excl N/X-runs)
GC level:         41.21 %
bases masked:   54993362 bp ( 30.07 %)
==================================================
               number of      length   percentage
               elements*    occupied  of sequence
--------------------------------------------------
Retroelements        20425     34109577 bp   18.65 %
   SINEs:                0            0 bp    0.00 %
   Penelope:             0            0 bp    0.00 %
   LINEs:             5348      5745751 bp    3.14 %
    CRE/SLACS            0            0 bp    0.00 %
     L2/CR1/Rex        571       360549 bp    0.20 %
     R1/LOA/Jockey    1034      1777954 bp    0.97 %
     R2/R4/NeSL          2          654 bp    0.00 %
     RTE/Bov-B           0            0 bp    0.00 %
     L1/CIN4             0            0 bp    0.00 %
   LTR elements:     15077     28363826 bp   15.51 %
     BEL/Pao          2506      3855726 bp    2.11 %
     Ty1/Copia         188       473628 bp    0.26 %
     Gypsy/DIRS1     12383     24034472 bp   13.14 %
       Retroviral        0            0 bp    0.00 %

DNA transposons      16164      6945591 bp    3.80 %
   hobo-Activator       60        16871 bp    0.01 %
   Tc1-IS630-Pogo      455       282932 bp    0.15 %
   En-Spm                0            0 bp    0.00 %
   MULE-MuDR             0            0 bp    0.00 %
   PiggyBac              0            0 bp    0.00 %
   Tourist/Harbinger     0            0 bp    0.00 %
   Other (Mirage,     2328      1075048 bp    0.59 %
    P-element, Transib)

Rolling-circles       8924      2215948 bp    1.21 %

Unclassified:         4331       786342 bp    0.43 %

Total interspersed repeats:    41841510 bp   22.88 %


Small RNA:             682       768181 bp    0.42 %

Satellites:           3699      3392104 bp    1.85 %
Simple repeats:     114775      6175358 bp    3.38 %
Low complexity:      12701       600261 bp    0.33 %
==================================================

* most repeats fragmented by insertions or deletions
  have been counted as one element
                                                      

The query species was assumed to be 7215          
RepeatMasker version 4.2.0 , default mode                                         
run with nhmmscan version 3.4 (Aug 2023)
FamDB: HMM-Dfam_3.9
```
**Notes:**  
- RepeatMasker marked 30% of the genome as repetitive, mostly LTR retroelements like Gypsy, plus some LINEs and DNA transposons. Those repeats were "soft masked" (converted to lowercase) so the sequence remains intact but aware of repeats. That version was fed into STAR for RNA‑seq mapping. Everything ran as expected; no errors and masking worked correctly.

- `-pa 12` following the message prompt that detected available CPUs.
- -species 7215 tells RepeatMasker to use repeat families specific to _Drosophila_ and related species.

---

## 7. RNA-seq Alignment

<details>
<summary><strong>STAR v2.7.11b  — Commands</strong></summary>

```bash
# Genome index
STAR --runThreadN 9 --runMode genomeGenerate \
     --genomeDir ext_long_read_assembly/star_index \
     --genomeFastaFiles \
       ext_long_read_assembly/repeatmasked_polished/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa \
     --genomeSAindexNbases 12

# Batch alignment (starloop.sh)
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

#Merging resulting alignments
samtools merge -@8 star_output_merged.bam STAR_output_A3_0TLl19_l1_1_Aligned.sortedByCoord.out.bam STAR_output_A4_0TP114_l1_1_Aligned.sortedByCoord.out.bam STAR_output_B3_0TFl19_l1_1_Aligned.sortedByCoord.out.bam STAR_output_B4_0TMl19_l1_1_Aligned.sortedByCoord.out.bam STAR_output_Im1611_GCCAAT_L005_R1_Aligned.sortedByCoord.out.bam

```
</details>

**Output:** `ext_long_read_assembly/star_repeatmasked_polished/`  
- `STAR_output/STAR_output_A3_0TLl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output/STAR_output_A4_0TP114_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output/STAR_output_B3_0TFl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output/STAR_output_B4_0TMl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output/STAR_output_Im1611_GCCAAT_L005_R1_Aligned.sortedByCoord.out.bam`
- `merged_STAR_output/star_output_merged.bam`
- `STAR_output/logs/STAR_output_[sample]_Log.final.out` (one per sample, contains alignment stats)

**Results summary:**  
```
| Sample        | Total Reads | Uniquely Mapped | % Unique | % Multi-mapped | % Unmapped | Mismatch Rate | % Spliced |
|---------------|-------------|------------------|----------|----------------|------------|----------------|------------|
| A3_0TLl19     | 62,503,852  | 56,407,216       | 90.25%   | 6.54%          | 3.21%      | 0.52%          | 93.0%      |
| A4_0TP114     | 69,024,252  | 63,288,388       | 91.67%   | 5.38%          | 2.95%      | 0.52%          | 92.9%      |
| B3_0TFl19     | 58,557,684  | 52,759,143       | 90.15%   | 6.51%          | 3.34%      | 0.59%          | 91.6%      |
| B4_0TMl19     | 67,858,012  | 58,266,942       | 85.85%   | 9.77%          | 4.38%      | 0.57%          | 89.2%      |
| Im1611_GCCAAT | 59,323,024  | 53,726,444       | 90.54%   | 6.04%          | 3.42%      | 0.56%          | 90.7%      |
```

**Notes:**  
- All RNA-seq samples were mapped successfully to the masked and polished genome using STAR. The uniquely mapped read percentage ranged from ~86% to ~92%, which indicates high-quality alignment. Multi-mapping reads ranged from ~5% to ~10%, which is expected given repetitive regions in the genome. Very few reads were unmapped. Mismatch rates were all below 0.6%, which is low and typical for good-quality RNA-seq data. About 88–93% of reads aligned to annotated splice junctions, which is consistent with correct intron-exon structure being captured. Alignment quality was high and the data are suitable for annotation.

- The individual BAM files were merged into a single file before running BRAKER.

---

## 8. Gene Prediction

<details>
<summary><strong>BRAKER v2.1.6</strong></summary>

```bash

#No reference run
(base) test@macstuTS dtei_repeatmasker % docker run --rm --platform linux/amd64 \
  -v "$(pwd)":/work \
  -v ~/Documents/gm_et_mac_osx:/opt/gm:ro \
  -u $(id -u):$(id -g) \
  -e AUGUSTUS_CONFIG_PATH=/work/augustus_config \
  -w /work \
  teambraker/braker3:latest \
  bash -c 'cp -r /opt/Augustus/config /work/augustus_config && \
    braker.pl \
      --genome /work/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa \
      --species Drosophila_orena_RS1 \
      --bam /work/star_output_merged.bam\
      --GENEMARK_PATH /opt/gm/gmes_petap.pl \
      --BAMTOOLS_PATH /usr/bin \
      --gff3 --threads 9'

# Reference run
(base) test@macstuTS dtei_repeatmasker % docker run --rm --platform linux/amd64 \
  -v "$(pwd)":/work \
  -v ~/Documents/gm_et_mac_osx:/opt/gm:ro \
  -u $(id -u):$(id -g) \
  -e AUGUSTUS_CONFIG_PATH=/work/augustus_config \
  -w /work \
  teambraker/braker3:latest \
  bash -c 'cp -r /opt/Augustus/config /work/augustus_config && \
    braker.pl \
      --genome /work/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa \
      --species Drosophila_orena_RS1 \
      --bam /work/star_output_merged.bam\
      --GENEMARK_PATH /opt/gm/gmes_petap.pl \
      --BAMTOOLS_PATH /usr/bin \
      --gff3 --threads 9 \
      --prot_seq /work/dmel-all-translation-r6.64.fasta'

# Checking stas for each

for f in braker.gtf; do
  echo "== $f =="; awk '{c[$3]++} END{for(k in c) printf "%s\t%d\n", k, c[k]}' "$f" | sort
done

```
</details>

**Key outputs:** `ext_long_read_assembly/braker_star_repeatmasked_polished/`  
- `braker.gtf`  
- `braker.gff3`  
- `braker.aa`  

**Results summary:**  

| Feature         | No -prot_seq | With -prot_seq |
|-----------------|-----------|---------|
| CDS             | 86575     | 76854   |
| exon            | 86575     | 76854   |
| gene            | 16286     | 14419   |
| intron          | 66904     | 59372   |
| start_codon     | 19667     | 17463   |
| stop_codon      | 19684     | 17465   |
| transcript_total| 19716     | 18223   |


**Notes:**  
- Two BRAKER runs were conducted: one using RNA-seq evidence alone (no --prot_seq), and one using both RNA-seq and protein hints from _D. melanogaster_ (dmel-all-translation-r6.64.fasta) - current_ Drosophila melanogaster_ from Flybase. You can find these in the without_prot and with_prot subfolders in the BRAKER directory. 

- Adding protein evidence resulted in more conservative predictions: fewer genes, exons, and CDS, likely reflecting a higher-confidence set constrained by known protein models. The RNA-seq-only run predicted more features overall, possibly including novel or species-specific elements, but at the risk of overprediction.


## 9. Gene Model Comparison

<details>
<summary><strong>GFFCompare v0.12.10</strong></summary>

```bash
# Compare RNA-seq-only gene models to RNA+protein-supported models
gffcompare -r ref.gff3 -o refcomp noref.gff3

# Compare RNA+protein-supported models to RNA-seq-only models
gffcompare -r noref.gff3 -o norefcomp ref.gff3
```
</details>

**Output files:**  
ADD THESE

**Results summary:**

| Comparison Direction | Sensitivity (transcript) | Precision (transcript) | Matching Loci | Novel Loci | Missed Loci |
|----------------------|---------------------------|-------------------------|----------------|-------------|---------------|
| RNA-only → Ref       | 63.8%                     | 71.2%                   | 11,684         | 286         | 2,097         |
| Ref → RNA-only       | 72.1%                     | 63.1%                   | 11,720         | 2,097       | 286           |

**Notes:**  
GFFCompare was used to compare gene predictions from BRAKER with and without protein evidence.

- When comparing RNA-only predictions to the protein-supported set:
  - 63.8% of transcripts matched (sensitivity), and 71.2% were correct (precision).
  - 2,097 known loci were missed, and 286 were new predictions.

- When comparing the protein-supported set to the RNA-only set:
  - Sensitivity was higher (72.1%), but precision was lower (63.1%).

This suggests that RNA-only predictions identify more genes, but with lower agreement to the reference. Protein evidence makes the predictions more conservative, possibly filtering out weaker or ambiguous signals. The RNA-only run may capture novel or divergent features, but also risks including spurious or less reliable models.

