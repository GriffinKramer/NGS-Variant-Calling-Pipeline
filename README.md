# NGS Variant Calling Pipeline
A reproducible Nextflow pipeline for variant detection and benchmarking. Supports two modes: 
simulation mode (mutate a reference genome, simulate reads, then call variants) and standard 
mode (align real paired-end reads and call variants). Both modes run two independent variant 
callers — **bcftools** and **Snippy** — and compare their outputs.

Developed as part of the MSc Bioinformatics program at the University of Bath.

## Pipeline Overview

Mode 1 — Simulation:
  Reference FASTA → [Mutate + Simulate Reads] → Alignment → Variant Calling → VCF Comparison

Mode 2 — Real Reads:
  FASTQ (paired-end) → Alignment → Variant Calling → VCF Comparison

### Steps
1. **(Optional) Mutation simulation** — introduces random mutations into the reference genome and simulates paired-end reads using a custom Python script
2. **Alignment** — aligns reads to the reference with `minimap2`, filtered and sorted with `samtools`
3. **Variant calling** — calls variants independently with `bcftools mpileup/call` and `snippy`
4. **VCF comparison** — compares the two VCFs against each other (and against ground-truth mutations in simulation mode) using a custom Python script

## Requirements

- [Nextflow](https://www.nextflow.io/) ≥ 21.0
- [minimap2](https://github.com/lh3/minimap2)
- [samtools](http://www.htslib.org/)
- [bcftools](http://www.htslib.org/)
- [Snippy](https://github.com/tseemann/snippy)
- Python 3 with standard libraries

## Usage

### Simulation mode
Mutates the reference, simulates reads, aligns, calls variants, and compares against ground truth.

```bash
nextflow run main.nf --reference [reference file] --prefix [intermediate file name] --outdir [out directory] --simulate_reads true
```

### Standard mode
Runs alignment and variant calling on real paired-end FASTQ files.

```bash
nextflow run main.nf --reference [reference file] --fastq1 [fq1.fastq.gz] --fastq2 [fq2.fastq.gz] –prefix [intermediate file name] --outdir [out directory]
```
## Output

| File | Description |
|---|---|
| `<prefix>.bam` / `.bai` | Aligned and indexed BAM |
| `<prefix>_bcftools.vcf` | Variants called by bcftools |
| `<prefix>_snippy.vcf` | Variants called by Snippy |
| `<prefix>_compare.txt` | Full VCF comparison output |
| `<prefix>_compare_summary.txt` | Summary statistics of caller agreement |
| `<prefix>_mutations.csv` | Ground-truth mutations (simulation mode only) |

---
