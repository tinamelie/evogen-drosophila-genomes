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

**Output (path):** `ext_long_read_assembly/raw_and_QC/quast_results/`  
- `report.html`  
- `report.pdf`  
- `report.tex`  
- `report.tsv`  
- `report.txt`  
- `transposed_report.tex`  
- `transposed_report.tsv`  
- `transposed_report.txt`  
- `quast.log`  
- `icarus.html`  
- `icarus_viewers/contig_size_viewer.html`  
- `basic_stats/GCA_005876975.1_DoreRS1_genomic_GC_content_plot.pdf`  
- `basic_stats/GC_content_plot.pdf`  
- `basic_stats/Nx_plot.pdf`  
- `basic_stats/cumulative_plot.pdf`  

**Results:**  
- N50: 5,273,554  
- L50: 8  
- GC content: 41.21%  
- Total length: 182,891,478

**Notes:**  
- Images also referenced inline:  
  - `Nx_plot.pdf` ![Nx_plot](ext_long_read_assembly/raw_and_QC/quast_results/Nx_plot.png)  
  - `GC_content_plot.pdf` ![GC_content_plot](ext_long_read_assembly/raw_and_QC/quast_results/GC_content_plot.png)

---

### 1b. BUSCO v5.4.2 (lineage: drosophila_odb12)

<details>
<summary><strong>Command</strong></summary>

```bash
busco -i ext_long_read_assembly/raw_and_QC/GCA_005876975.1_DoreRS1_genomic.fna \
  -l drosophila_odb12 -m genome -o dore2
```
</details>

**Output (path):** `ext_long_read_assembly/raw_and_QC/run_dore2/`  
- `busco_results_summary/full_table.tsv`  
- `busco_results_summary/missing_busco_list.tsv`  
- `busco_results_summary/short_summary.txt`

**Results (BUSCO Output):**
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

---

## 2. Read QC (FASTQC)

<details>
<summary><strong>FASTQC v0.11.x — Commands</strong></summary>

```bash
# Genomic short reads
fastqc -o evogen_short_reads/raw_reads_and_QC/FASTQC \
  evogen_short_reads/raw_reads_and_QC/*.fastq.gz

# RNA-seq reads
fastqc -o evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC \
  evo_gen_rna_seq_reads/raw_reads_and_QC/*.fq.gz
```
</details>

**Output (path):**  
- Genomic QC reports: `evogen_short_reads/raw_reads_and_QC/FASTQC/`  
  - `C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html`  
  - `C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.zip`  
  - `C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html`  
  - `C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.zip`  
- RNA-seq QC reports: `evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC/`  
  - `A3_0TLl19_l1_1_fastqc.html`  
  - `A3_0TLl19_l1_1_fastqc.zip`  
  - `A3_0TLl19_l1_2_fastqc.html`  
  - `A3_0TLl19_l1_2_fastqc.zip`  
  - `A4_0TP114_l1_1_fastqc.html`  
  - `A4_0TP114_l1_1_fastqc.zip`  
  - `A4_0TP114_l1_2_fastqc.html`  
  - `A4_0TP114_l1_2_fastqc.zip`  
  - `B3_0TFl19_l1_1_fastqc.html`  
  - `B3_0TFl19_l1_1_fastqc.zip`  
  - `B3_0TFl19_l1_2_fastqc.html`  
  - `B3_0TFl19_l1_2_fastqc.zip`  
  - `B4_0TMl19_l1_1_fastqc.html`  
  - `B4_0TMl19_l1_1_fastqc.zip`  
  - `B4_0TMl19_l1_2_fastqc.html`  
  - `B4_0TMl19_l1_2_fastqc.zip`  
  - `Im1611_GCCAAT_L005_R1_fastqc.html`  
  - `Im1611_GCCAAT_L005_R1_fastqc.zip`  
  - `Im1611_GCCAAT_L005_R2_fastqc.html`  
  - `Im1611_GCCAAT_L005_R2_fastqc.zip`

**Results:**  

**Notes:**  
- Genomic: `evogen_short_reads/raw_reads_and_QC/FASTQC/`  
- RNA-seq: `evo_gen_rna_seq_reads/raw_reads_and_QC/FASTQC/NAME.pdf` images such as `Images/per_base_quality.png`

---

## 3. Trimming

### 3a. Evogen Short Reads — Trimmomatic v0.39 (single-sample command)

<details>
<summary><strong>Command</strong></summary>

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

**Output (path):** `evogen_short_reads/trimmed_reads_and_QC/`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R1_paired.fq.gz`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R1_unpaired.fq.gz`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R2_paired.fq.gz`  
- `C3F0NACXX_PG0409_02A02_H1_L006_R2_unpaired.fq.gz`  
- `FASTQC/C3F0NACXX_PG0409_02A02_H1_L006_R1_paired_fastqc.html`  
- `FASTQC/C3F0NACXX_PG0409_02A02_H1_L006_R1_paired_fastqc.zip`

**Results:**  
- Input pairs: 176,570,473  
- Paired surviving: 153,387,586 (86.87%)

**Notes:** 

---

### 3a (loop script as used) — Trimmomatic v0.39

<details>
<summary><strong>Command</strong></summary>

```bash
#!/bin/bash

threads=8
adapter_file="custom-adapters.fa"

# source / destination directories
raw_dir="evo_gen_rna_seq_reads/raw_reads_and_QC"
out_dir="evo_gen_rna_seq_reads/trimmed_reads_and_QC"
mkdir -p "${out_dir}"

# Base sample identifiers (omit the read suffix)
samples=(
  "A3_0TLl19_l1"
  "A4_0TP114_l1"
  "B3_0TFl19_l1"
  "B4_0TMl19_l1"
  "Im1611_GCCAAT_L005_R"   # note the trailing “_R” to handle _R1 / _R2
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

**Output (path):** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/`  
- `A3_0TLl19_l1_1.trim.paired.fq.gz`  
- `A3_0TLl19_l1_1.trim.unpaired.fq.gz`  
- `A3_0TLl19_l1_2.trim.paired.fq.gz`  
- `A3_0TLl19_l1_2.trim.unpaired.fq.gz`  
- `A4_0TP114_l1_1.trim.paired.fq.gz`  
- `A4_0TP114_l1_1.trim.unpaired.fq.gz`  
- `A4_0TP114_l1_2.trim.paired.fq.gz`  
- `A4_0TP114_l1_2.trim.unpaired.fq.gz`  
- `B3_0TFl19_l1_1.trim.paired.fq.gz`  
- `B3_0TFl19_l1_1.trim.unpaired.fq.gz`  
- `B3_0TFl19_l1_2.trim.paired.fq.gz`  
- `B3_0TFl19_l1_2.trim.unpaired.fq.gz`  
- `B4_0TMl19_l1_1.trim.paired.fq.gz`  
- `B4_0TMl19_l1_1.trim.unpaired.fq.gz`  
- `B4_0TMl19_l1_2.trim.paired.fq.gz`  
- `B4_0TMl19_l1_2.trim.unpaired.fq.gz`  
- `Im1611_GCCAAT_L005_R1.trim.paired.fq.gz`  
- `Im1611_GCCAAT_L005_R1.trim.unpaired.fq.gz`  
- `Im1611_GCCAAT_L005_R2.trim.paired.fq.gz`  
- `Im1611_GCCAAT_L005_R2.trim.unpaired.fq.gz`  
- `FASTQC/A3_0TLl19_l1_1.trim.paired_fastqc.html`  
- `FASTQC/A3_0TLl19_l1_1.trim.paired_fastqc.zip`  
- `FASTQC/A3_0TLl19_l1_2.trim.paired_fastqc.html`  
- `FASTQC/A3_0TLl19_l1_2.trim.paired_fastqc.zip`  
- `FASTQC/A4_0TP114_l1_1.trim.paired_fastqc.html`  
- `FASTQC/A4_0TP114_l1_1.trim.paired_fastqc.zip`  
- `FASTQC/A4_0TP114_l1_2.trim.paired_fastqc.html`  
- `FASTQC/A4_0TP114_l1_2.trim.paired_fastqc.zip`  
- `FASTQC/B3_0TFl19_l1_1.trim.paired_fastqc.html`  
- `FASTQC/B3_0TFl19_l1_1.trim.paired_fastqc.zip`  
- `FASTQC/B3_0TFl19_l1_2.trim.paired_fastqc.html`  
- `FASTQC/B3_0TFl19_l1_2.trim.paired_fastqc.zip`  
- `FASTQC/B4_0TMl19_l1_1.trim.paired_fastqc.html`  
- `FASTQC/B4_0TMl19_l1_1.trim.paired_fastqc.zip`  
- `FASTQC/B4_0TMl19_l1_2.trim.paired_fastqc.html`  
- `FASTQC/B4_0TMl19_l1_2.trim.paired_fastqc.zip`  
- `FASTQC/Im1611_GCCAAT_L005_R1.trim.paired_fastqc.html`  
- `FASTQC/Im1611_GCCAAT_L005_R1.trim.paired_fastqc.zip`  
- `FASTQC/Im1611_GCCAAT_L005_R2.trim.paired_fastqc.html`  
- `FASTQC/Im1611_GCCAAT_L005_R2.trim.paired_fastqc.zip`

**Results:**  
see table below

**Notes:**  
Adapter file = `custom-adapters.fa`

---

### 3b. Trimming: Evogen RNA-Seq Reads

<details>
<summary><strong>Trimmomatic v0.39 — Command</strong></summary>

```bash
# Applied to all samples (scripted loop)
trimmomatic PE -phred33 -threads 8 A3_0TLl19_l1_1.fq.gz A3_0TLl19_l1_2.fq.gz \
  A3_0TLl19_l1_1.trim.paired.fq.gz A3_0TLl19_l1_1.trim.unpaired.fq.gz \
  A3_0TLl19_l1_2.trim.paired.fq.gz A3_0TLl19_l1_2.trim.unpaired.fq.gz \
  ILLUMINACLIP:custom-adapters.fa:2:30:10 HEADCROP:10 LEADING:3 TRAILING:3 \
  SLIDINGWINDOW:4:15 MINLEN:36
```
</details>

**Output (path):** `evo_gen_rna_seq_reads/trimmed_reads_and_QC/` (files listed above)

**Results:**  

**Adapter sequences used:**  
- AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT  
- AGATCGGAAGAGCACACGTCTGAACTCCAGTCA  
- CTGTCTCTTATACACATCT  
- AGATGTGTATAAGAGACAG  

**Results by sample:**

| Sample             | Input Pairs | Paired Surviving | Forward Only | Reverse Only | Dropped | % Surviving |
|--------------------|-------------|------------------|--------------|---------------|---------|-------------|
| A3_0TLl19          | 40,763,583  | 40,762,377       | 0            | 1,206         | 0       | 100.00%     |
| A4_0TP114          | 45,457,493  | 45,456,202       | 0            | 1,291         | 0       | 100.00%     |
| B3_0TFl19          | 38,153,929  | 38,149,395       | 3,810        | 724           | 0       | 99.99%      |
| B4_0TMl19          | 44,019,572  | 44,014,226       | 4,501        | 845           | 0       | 99.99%      |
| Im1611_GCCAAT      | 38,907,278  | 37,145,203       | 1,334,893    | 275,618       | 151,564 | 95.47%      |

**Notes:**  
Let me know if you want the remaining steps restructured in the same clarified style.

---

## 4. Post-trimming Read QC (FASTQC v0.11.x)

<details>
<summary><strong>Commands</strong></summary>

```bash
fastqc -o evogen_short_reads/trimmed_reads_and_QC/FASTQC \
       evogen_short_reads/trimmed_reads_and_QC/*_paired.fq.gz

fastqc -o evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC \
       evo_gen_rna_seq_reads/trimmed_reads_and_QC/*_paired.fq.gz
```
</details>

**Output (path):**  
- `evogen_short_reads/trimmed_reads_and_QC/FASTQC/`  
  - `C3F0NACXX_PG0409_02A02_H1_L006_R1_paired_fastqc.html`  
  - `C3F0NACXX_PG0409_02A02_H1_L006_R1_paired_fastqc.zip`  
- `evo_gen_rna_seq_reads/trimmed_reads_and_QC/FASTQC/`  
  - `A3_0TLl19_l1_1.trim.paired_fastqc.html`  
  - `A3_0TLl19_l1_1.trim.paired_fastqc.zip`  
  - `A3_0TLl19_l1_2.trim.paired_fastqc.html`  
  - `A3_0TLl19_l1_2.trim.paired_fastqc.zip`  
  - `A4_0TP114_l1_1.trim.paired_fastqc.html`  
  - `A4_0TP114_l1_1.trim.paired_fastqc.zip`  
  - `A4_0TP114_l1_2.trim.paired_fastqc.html`  
  - `A4_0TP114_l1_2.trim.paired_fastqc.zip`  
  - `B3_0TFl19_l1_1.trim.paired_fastqc.html`  
  - `B3_0TFl19_l1_1.trim.paired_fastqc.zip`  
  - `B3_0TFl19_l1_2.trim.paired_fastqc.html`  
  - `B3_0TFl19_l1_2.trim.paired_fastqc.zip`  
  - `B4_0TMl19_l1_1.trim.paired_fastqc.html`  
  - `B4_0TMl19_l1_1.trim.paired_fastqc.zip`  
  - `B4_0TMl19_l1_2.trim.paired_fastqc.html`  
  - `B4_0TMl19_l1_2.trim.paired_fastqc.zip`  
  - `Im1611_GCCAAT_L005_R1.trim.paired_fastqc.html`  
  - `Im1611_GCCAAT_L005_R1.trim.paired_fastqc.zip`  
  - `Im1611_GCCAAT_L005_R2.trim.paired_fastqc.html`  
  - `Im1611_GCCAAT_L005_R2.trim.paired_fastqc.zip`

**Results:**  
Outputs: corresponding `.../FASTQC/` directories (raw + images preserved).

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

**Output (path):**  
- `alignmentsR1.sam`  
- `alignmentsR2.sam`

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

**Output (path):** `ext_long_read_assembly/polished_and_QC/`  
- `GCA_005876975.1_DoreRS1_genomic_polished.fasta`  
- `GCA_005876975.1_DoreRS1_genomic_polished.flagstat`

**Results:**  
Combined flagstat: Total 307,086,568 • Mapped 228,784,008 (74.50 %) • Properly-paired 218,148,562 (71.11 %)

**Notes:**  

---

## 6. Repeat Masking — RepeatMasker v4.2.0 (+ HMMER 3.4)

<details>
<summary><strong>Command</strong></summary>

```bash
RepeatMasker -pa 12 -xsmall -e hmmer -species 7215 \
  ext_long_read_assembly/polished_and_QC/polished.fasta
```
</details>

**Output (path):** `ext_long_read_assembly/repeatmasked_polished/`  
- `GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked.clean.fa`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.cat.gz`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.masked`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.out`  
- `repeatmasked/GCA_005876975.1_DoreRS1_genomic_polished.fasta.tbl`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta.cat.all.gz`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-7.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-7.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-8.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-8.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-9.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-9.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-10.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-10.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-11.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-11.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-12.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-12.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-13.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-13.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-14.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-14.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-15.masked`  
- `repeatmasked/RM_2261.WedJul162235212025/GCA_005876975.1_DoreRS1_genomic_polished.fasta_batch-15.tmp.simple1`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730537-2308.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730537-2308.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730537-2309.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730537-2309.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730537-2310.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730537-2310.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2382.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2382.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2383.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2383.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2384.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2384.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2385.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730570-2385.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730593-2408.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730593-2408.out`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730601-2430.err`  
- `repeatmasked/RM_2261.WedJul162235212025/hmmerResults-1752730601-2430.out`

**Results:**  

**Notes:**  
- `-species 7215` specifies *Drosophila* to use the appropriate repeat library for the genus.  
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

**Output (path):** `ext_long_read_assembly/star_repeatmasked_polished/STAR_output/`  
- `STAR_output_A3_0TLl19_l1_1_Aligned.sortedByCoord.out.bam`  
- `STAR_output_A4_0TP114_l1_1_Aligned.sortedByCoord.out.bam`  
*(plus corresponding outputs for other samples as produced by the loop)*

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
  --softmasking \ BE SURE TO INCLUDE OUTPUT (PATH) RESULTS AND NOTES FOR EACH SECTION1!!!!!! SINGLE CODE BLOCK!~!!!! INCLUDE THE SECTIONS!!!
  --species=Drosophila_orena_RS1 \
  --threads 9 --gff3
```
</details>

**Outputs (path):** `ext_long_read_assembly/braker_star_repeatmasked_polished/`  
- `braker.gtf`  
- `braker.gff3`  
- `braker.codingseq`  
- `braker.aa`  
- `braker.log`  
- `bam_header.map`  
- `genome_header.map`  
- `hintsfile.gff`  
- `errors/aa2nonred.stderr`  
- `what-to-cite.txt`  
- `Augustus/augustus.hints.aa`  
- `Augustus/augustus.hints.codingseq`  
- `Augustus/augustus.hints.gff3`  
- `Augustus/augustus.hints.gtf`  
- `GeneMark-ET/genemark.f.multi_anchored.gtf`  
- `GeneMark-ET/genemark.gtf`  
- `GeneMark-ET/gmhmm.mod`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_exon_probs.pbl`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_igenic_probs.pbl`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_intron_probs.pbl`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_metapars.cfg`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_metapars.cgp.cfg`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_metapars.utr.cfg`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_parameters.cfg`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_parameters.cfg.orig1`  
- `species/Drosophila_orena_RS1/Drosophila_orena_RS1_weightmatrix.txt`  
- `species/Drosophila_orena_RS1/ex1.cfg`

**Results:**  

**Notes:**  
