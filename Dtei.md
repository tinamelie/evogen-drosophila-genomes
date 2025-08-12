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
- [`C3F0NACXX_PG0409_02A02_H1_L006_R1_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/C3F0NACXX_PG0409_01A01_H1_L005_R1_fastqc.html)  
- [`C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html`](https://tinamelie.github.io/evogen-drosophila-genomes/reports/Dtei/C3F0NACXX_PG0409_02A02_H1_L006_R2_fastqc.html)

**Notes:**  
The FastQC reports for both R1 and R2 show uniformly high-quality data. Per-base sequence quality is consistently high across the full read length, with no regions of concern. Per-tile sequence quality is uniform, indicating no spatial bias across the flow cell. Base composition and GC content match expectations, and N content is negligible. No adapter contamination or overrepresented sequences were detected. Sequence duplication levels were not flagged and are within normal expectations for this dataset. 



