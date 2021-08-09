# Overview

SPAdes – St. Petersburg genome assembler [de novo genome assembly with de Bruijn graphs](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3342519/)

---

## Contents
1. [Genome assembliy and de Bruijn graphs]
1. [What’s Spades good for?](#introduction)
1. [Before using Spades](#Before-using-Spades)
1. [Using Spades](#Using-Spades)
1. [After using Spades](#After-using-Spades)

---

## What’s Spades good for?
* Short-reads de novo assemblies
* Short-reads + Long-read hybrid de novo assemblies
* Mainly for smaller size genomes (bacterial, viral, fungal, mitochondrial genomes etc.)

---

## Before using Spades
* Know your sequencing read data: FastQC
* What is the read length? 
* Do they contain adaptor sequences?
* Trimming of the sequencing read data: Trimmomatic, fastp
* Adaptors
* low quality sequences
* very short reads 
* Finding out available version of Spades in the server: 

```bash
$ module avail spades
```

* Loading in the flavour you’d like to use: 

```bash
$ module load  SPAdes/3.15.2-gimkl-2020a
```

---

## Using Spades
* Finding out available options and default values:

```bash
$ spades.py -h
```

## After using Spades
* Assembly quality assessment with QUAST:

```bash
# loading the module QUAST
$ Module load QUAST/5.0.2-gimkl-2018b

# running QUAST analysis for the assembly
$ quast.py *.fasta -o quast_output
```
---

* Visualization de novo assembly graphs to explore connections between the contigs:

```bash
# Loading dependency for Bandage
$ module load Qt5/5.13.2-GCCcore-9.2.0

# Copy the program Bandage to the folder storing the FASTG files:
$ cp /module_4/Bandage /folder

# Read [FASTG](http://fastg.sourceforge.net/FASTG_Spec_v1.00.pdf) file and drew image with Bandage:
$ ./Bandage image *.fastg *.svg

```
Download and inspect the output image file in WINDOWS using browser like `Microsoft Edage`.
* Tip: press the Ctrl and roll the mouse wheel to zoom the image.
